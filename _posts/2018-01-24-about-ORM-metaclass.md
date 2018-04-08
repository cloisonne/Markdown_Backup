---
title: 关于Python元类metaclass和在ORM中的应用
categories:
  - Codes
tags:
  - Python
permalink: about-ORM-metaclass
abbrlink: 29034
date: 2018-01-24 19:02:34
---

<h2 id="intro">前言</h2>元类其实并不复杂，即生成类的一个类，type可以动态创建类，同时，可以使用更为强大的metaclass。
ORM是这种模式的应用场景，即Object Relational Mapping。此外本文还介绍了协程coroutine和logging模块。


<!-- more -->

---------------

## type和metaclass生成类

### type生成类

``` python
>>> def fn(self, name='world'): # 先定义函数
...     print('Hello, %s.' % name)
...
>>> Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class
```

### metaclass生成类

``` python
class ListMetaclass(type):
    def __new__(cls, name, bases, attrs):
        attrs['add'] = lambda self, value: self.append(value)
        return type.__new__(cls, name, bases, attrs)
class MyList(list, metaclass=ListMetaclass):
    pass
```

---

## ORM实例

### 廖雪峰简单ORM
``` python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

# 用metaclass写一个ORM的demo

class Field(object):

    def __init__(self, name, column_type):
        self.name = name
        self.column_type = column_type

    # 返回对象的字符串表达式
    def __str__(self):
        return '<%s:%s>' % (self.__class__.name, self.name)

class StringField(Field):

    def __init__(self, name):
        super(StringField, self).__init__(name,'varchar(100)')

class IntagerField(Field):

    def __init__(self, name):
        super(IntagerField, self).__init__(name, 'bigint')

class ModelMetaclass(type):

    def __new__(cls, name, bases, attrs):
        if name == 'Model':
            return type.__new__(cls,name,bases,attrs)
        print('Found model: %s' % name)
        mappings = dict()
        for k,v in attrs.items():
            if isinstance(v, Field):
                print('Found mapping: %s ==> %s' % (k,v))
                mappings[k] = v
        for k in mappings.keys():
            attrs.pop()
        attrs['__mappings__'] = mappings
        attrs['__table__'] = name
        return type.__new__(cls, name, bases, attrs)

class Model(dict, metaclass=ModelMetaclass):

    def __init__(self, **kw):
        super(Model, self).__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Model' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value

    def save(self):
        fields = []
        params = []
        args = []
        for k, v in self.__mappings__.items():
            fields.append(v.name)
            params.append('?')
            args.append(getattr(self, k, None))
        sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(fields), ','.join(params))
        print('SQL: %s' % sql)
        print('ARGS: %s' % str(args))

# 使用方法

class User(Model):
    id = IntagerField('id')
    name = StringField('username')
    email = StringField('email')
    password = StringField('password')

u = User(id = 12345, name = 'Li', email = '123', password = '22')

u.save()
```

### 更复杂的一个ORM

来自[编写orm时的重难点掌握](http://www.songluyi.com/python-%e7%bc%96%e5%86%99orm%e6%97%b6%e7%9a%84%e9%87%8d%e9%9a%be%e7%82%b9%e6%8e%8c%e6%8f%a1/)

代码比较复杂：
``` python
import sys
import asyncio
import logging
logging.basicConfig(level=logging.INFO)
# 一次使用异步 处处使用异步
import aiomysql
 
def log(sql,args=()):
    logging.info('SQL:%s' %sql)
@asyncio.coroutine
def create_pool(loop, **kw):
    logging.info(' start creating database connection pool')
    global __pool
    # 理解这里的yield from 是很重要的
    __pool=yield from aiomysql.create_pool(
        host=kw.get('host','localhost'),
        port=kw.get('port',3306),
        user=kw['user'],
        password=kw['password'],
        db=kw['db'],
        charset=kw.get('charset','utf8'),
        autocommit=kw.get('autocommit',True),
        maxsize=kw.get('maxsize',10),
        # 目前不清楚这个minsize是什么鬼
        minsize=kw.get('minsize',1),
        loop=loop
        )
 
@asyncio.coroutine
def destroy_pool():
    global __pool
    if __pool is not None :
        __pool.close()
        yield from __pool.wait_closed()
 
# 我很好奇为啥不用commit 事务不用提交么
@asyncio.coroutine
def select(sql, args, size=None):
    log(sql,args)
    global __pool
    # 666 建立游标
    # -*- yield from 将会调用一个子协程，并直接返回调用的结果
    # yield from从连接池中返回一个连接
    with (yield from __pool)as conn:
        cur = yield from conn.cursor(aiomysql.DictCursor)
        yield from cur.execute(sql.replace('?', '%s'), args)
        if size:
            rs = yield from cur.fetchmany(size)
        else:
            rs = yield from cur.fetchall()
        yield from cur.close()
        logging.info('rows have returned %s' %len(rs))
    return rs
 
 
# 封装INSERT, UPDATE, DELETE
# 语句操作参数一样，所以定义一个通用的执行函数
# 返回操作影响的行号
# 我想说的是 知道影响行号有个叼用
 
@asyncio.coroutine
def execute(sql,args, autocommit=True):
    log(sql)
    global __pool
    with (yield from __pool) as conn:
        try:
            # 因为execute类型sql操作返回结果只有行号，不需要dict
            cur = yield from conn.cursor()
            # 顺便说一下 后面的args 别掉了 掉了是无论如何都插入不了数据的
            yield from cur.execute(sql.replace('?', '%s'), args)
            yield from conn.commit()
            affected_line=cur.rowcount
            yield from cur.close()
            print('execute : ', affected_line)
        except BaseException as e:
            raise
        return affected_line
 
# 这个函数主要是把查询字段计数 替换成sql识别的?
# 比如说：insert into  `User` (`password`, `email`, `name`, `id`) values (?,?,?,?)  看到了么 后面这四个问号
def create_args_string(num):
    lol=[]
    for n in range(num):
        lol.append('?')
    return (','.join(lol))
 
# 定义Field类，负责保存(数据库)表的字段名和字段类型
class Field(object):
    # 表的字段包含名字、类型、是否为表的主键和默认值
    def __init__(self, name, column_type, primary__key, default):
        self.name = name
        self.column_type=column_type
        self.primary_key=primary__key
        self.default=default
    def __str__(self):
        # 返回 表名字 字段名 和字段类型
        return "&lt;%s , %s , %s&gt;" %(self.__class__.__name__, self.name, self.column_type)
# 定义数据库中五个存储类型
class StringField(Field):
    def __init__(self, name=None, primary_key=False, default=None, ddl='varchar(100)'):
        super().__init__(name,ddl,primary_key,default)
# 布尔类型不可以作为主键
class BooleanField(Field):
    def __init__(self, name=None, default=None):
        super().__init__(name,'Boolean',False, default)
# 不知道这个column type是否可以自己定义 先自己定义看一下
class IntegerField(Field):
    def __init__(self, name=None, primary_key=False, default=0):
        super().__init__(name, 'int', primary_key, default)
class FloatField(Field):
    def __init__(self, name=None, primary_key=False,default=0.0):
        super().__init__(name, 'float', primary_key, default)
class TextField(Field):
    def __init__(self, name=None, default=None):
        super().__init__(name,'text',False, default)
# class Model(dict,metaclass=ModelMetaclass):
 
# -*-定义Model的元类
 
# 所有的元类都继承自type
# ModelMetaclass元类定义了所有Model基类(继承ModelMetaclass)的子类实现的操作
 
# -*-ModelMetaclass的工作主要是为一个数据库表映射成一个封装的类做准备：
# ***读取具体子类(user)的映射信息
# 创造类的时候，排除对Model类的修改
# 在当前类中查找所有的类属性(attrs)，如果找到Field属性，就将其保存到__mappings__的dict中，同时从类属性中删除Field(防止实例属性遮住类的同名属性)
# 将数据库表名保存到__table__中
 
# 完成这些工作就可以在Model中定义各种数据库的操作方法
# metaclass是类的模板，所以必须从`type`类型派生：
class ModelMetaclass(type):
    # __new__控制__init__的执行，所以在其执行之前
    # cls:代表要__init__的类，此参数在实例化时由Python解释器自动提供(例如下文的User和Model)
    # bases：代表继承父类的集合
    # attrs：类的方法集合
    def __new__(cls, name, bases, attrs):
        # 排除model 是因为要排除对model类的修改
        if name=='Model':
            return type.__new__(cls, name, bases, attrs)
        # 获取table名称 为啥获取table名称 至于在哪里我也是不明白握草
        table_name=attrs.get('__table__', None) or name
        logging.info('found table: %s (table: %s) ' %(name,table_name ))
        # 获取Field所有主键名和Field
        mappings=dict()
        fields=[]
        primaryKey=None
        # 这个k是表示字段名
        for k, v in attrs.items():
            if isinstance(v, Field):
                logging.info('Found mapping %s===&gt;%s' %(k, v))
            # 注意mapping的用法
                mappings[k] = v
                if v.primary_key:
                    logging.info('fond primary key hahaha %s'%k)
                    # 这里很有意思 当第一次主键存在primaryKey被赋值 后来如果再出现主键的话就会引发错误
                    if primaryKey:
                        raise RuntimeError('Duplicated key for field')
                    primaryKey=k
                else:
                    fields.append(k)
 
        if not primaryKey:
            raise RuntimeError('Primary key not found!')
        # w下面位字段从类属性中删除Field 属性
        for k in mappings.keys():
            attrs.pop(k)
 
        # 保存除主键外的属性为''列表形式
        # 这一句的lambda表达式没懂
        escaped_fields=list(map(lambda f:'`%s`' %f, fields))
        # 保存属性和列的映射关系
        attrs['__mappings__']=mappings
        # 保存表名
        attrs['__table__']=table_name
        # 保存主键名称
        attrs['__primary_key__']=primaryKey
        # 保存主键外的属性名
        attrs['__fields__']=fields
        # 构造默认的增删改查 语句
        attrs['__select__']='select `%s`, %s from `%s` '%(primaryKey,', '.join(escaped_fields), table_name)
        attrs['__insert__'] = 'insert into  `%s` (%s, `%s`) values (%s) ' %(table_name, ', '.join(escaped_fields), primaryKey, create_args_string(len(escaped_fields)+1))
        attrs['__update__']='update `%s` set %s where `%s` = ?' %(table_name, ', '.join(map(lambda f:'`%s`=?' % (mappings.get(f).name or f), fields)), primaryKey)
        attrs['__delete__']='delete `%s` where `%s`=?' %(table_name, primaryKey)
        return type.__new__(cls, name, bases, attrs)
 
 
# 定义ORM所有映射的基类：Model
# Model类的任意子类可以映射一个数据库表
# Model类可以看作是对所有数据库表操作的基本定义的映射
 
 
# 基于字典查询形式
# Model从dict继承，拥有字典的所有功能，同时实现特殊方法__getattr__和__setattr__，能够实现属性操作
# 实现数据库操作的所有方法，定义为class方法，所有继承自Model都具有数据库操作方法
 
class Model(dict,metaclass=ModelMetaclass):
    def __init__(self, **kw):
        super(Model,self).__init__(**kw)
    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError("'Model' object have no attribution: %s"% key)
    def __setattr__(self, key, value):
        self[key] =value
    def getValue(self, key):
        # 这个是默认内置函数实现的
        return getattr(self, key, None)
 
    def getValueOrDefault(self, key):
        value=getattr(self, key , None)
        if value is None:
            field = self.__mappings__[key]
            if field.default is not None:
                value = field.default() if callable(field.default) else field.default
                logging.info('using default value for %s : %s ' % (key, str(value)))
                setattr(self, key, value)
 
        return value
 
    @classmethod
    # 类方法有类变量cls传入，从而可以用cls做一些相关的处理。并且有子类继承时，调用该类方法时，传入的类变量cls是子类，而非父类。
    @asyncio.coroutine
 
    def find_all(cls, where=None, args=None, **kw):
        sql = [cls.__select__]
        if where:
            sql.append('where')
            sql.append(where)
        if args is None:
            args = []
 
        orderBy = kw.get('orderBy', None)
        if orderBy:
            sql.append('order by')
            sql.append(orderBy)
        # dict 提供get方法 指定放不存在时候返回后学的东西 比如a.get('Fuck',None)
        limit = kw.get('limit', None)
        if limit is not None:
            sql.append('limit')
            if isinstance(limit, int):
                sql.append('?')
                args.append(limit)
            elif isinstance(limit, tuple) and len(limit) ==2:
                sql.append('?,?')
                args.extend(limit)
            else:
                raise ValueError('Invalid limit value : %s '%str(limit))
 
        rs = yield from select(' '.join(sql),args)
        return [cls(**r) for r in rs]
    @classmethod
    @asyncio.coroutine
    def findNumber(cls, selectField, where=None, args=None):
        '''find number by select and where.'''
        sql = ['select %s __num__ from `%s`' %(selectField, cls.__table__)]
        if where:
            sql.append('where')
            sql.append(where)
        rs = yield from select(' '.join(sql), args, 1)
        if len(rs) == 0:
            return None
        return rs[0]['__num__']
 
    # 这个黑魔法我还在研究呢~
    @classmethod
    @asyncio.coroutine
    def find(cls, primarykey):
        '''find object by primary key'''
        rs = yield from select('%s where `%s`=?' %(cls.__select__, cls.__primary_key__), [primarykey], 1)
        if len(rs) == 0:
            return None
        return cls(**rs[0])
 
    @classmethod
    @asyncio.coroutine
    def findAll(cls, **kw):
        rs = []
        if len(kw) == 0:
            rs = yield from select(cls.__select__, None)
        else:
            args=[]
            values=[]
            for k, v in kw.items():
                args.append('%s=?' % k )
                values.append(v)
            rs = yield from select('%s where %s ' % (cls.__select__,  ' and '.join(args)), values)
        return rs
    
    @asyncio.coroutine
    def save(self):
        args = list(map(self.getValueOrDefault, self.__fields__))
        print('save:%s' % args)
        args.append(self.getValueOrDefault(self.__primary_key__))
        rows = yield from execute(self.__insert__, args)
        if rows != 1:
            print(self.__insert__)
            logging.warning('failed to insert record: affected rows: %s' %rows)
 
    @asyncio.coroutine
    # 显示方言错误是什么鬼。。。
    def update(self):
        args = list(map(self.getValue, self.__fields__))
        args.append(self.getValue(self.__primary_key__))
        rows = yield from execute(self.__update__, args)
        if rows != 1:
            logging.warning('failed to update record: affected rows: %s'%rows)
 
    @asyncio.coroutine
    def remove(self):
        args = [self.getValue(self.__primary_key__)]
        rows = yield from execute(self.__updata__, args)
        if rows != 1:
            logging.warning('failed to remove by primary key: affected rows: %s' %rows)
 
 
if __name__=="__main__":
    class User(Model):
        id = IntegerField('id',primary_key=True)
        name = StringField('username')
        email = StringField('email')
        password = StringField('password')
    #创建异步事件的句柄
    loop = asyncio.get_event_loop()
 
    #创建实例
    @asyncio.coroutine
    def test():
        yield from create_pool(loop=loop,host='localhost', port=3308, user='sly', password='070801382', db='test')
        user = User(id=8, name='sly', email='slysly759@gmail.com', password='fuckblog')
        yield from user.save()
        r = yield from User.find('11')
        print(r)
        r = yield from User.findAll()
        print(1, r)
        r = yield from User.findAll(id='12')
        print(2, r)
        yield from destroy_pool()
 
    loop.run_until_complete(test())
    loop.close()
    if loop.is_closed():
        sys.exit(0)
```

---

## 知识点学习

### asyncio异步IO库

廖雪峰中的asyncio异步获取网页例子：

注意异步操作需要在`coroutine`中通过`yield from`完成

``` python
import asyncio

@asyncio.coroutine
def wget(host):
    print('wget %s...' % host)
    connect = asyncio.open_connection(host, 80)
    reader, writer = yield from connect
    header = 'GET / HTTP/1.0\r\nHost: %s\r\n\r\n' % host
    writer.write(header.encode('utf-8'))
    yield from writer.drain()
    while True:
        line = yield from reader.readline()
        if line == b'\r\n':
            break
        print('%s header > %s' % (host, line.decode('utf-8').rstrip()))
    # Ignore the body, close the socket
    writer.close()

loop = asyncio.get_event_loop()
tasks = [wget(host) for host in ['www.sina.com.cn', 'www.sohu.com', 'www.163.com']]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()
```

### logging日志库

知乎上有个问题里拿来的demo:

问题是为什么pycharm的traceback还有ERROR等打印顺序会发生变化，加了sleep就不会变化了。

我猜测是pycharm有一些异步执行的步骤，具体也没有在StackOverflow上找到答案。`作为TODO吧`

``` python
import logging
import time
logging.basicConfig(level=logging.DEBUG)

try:
    print ('try')
    time.sleep(2)
    r = 10 / int('a')
    print ('result', r)
except ValueError as e:
    time.sleep(2)
    logging.exception(e)
except ZeroDivisionError as e:
    print ('exception', e)
else:
    print ('no error')
finally:
    time.sleep(2)
    print ('finally.')
    print ('END**************')
```

logging的级别：

- DEBUG
- INFO
- WARNING
- ERROR
- CRITICAL
- NOSET

``` python
logger.debug('debug message')
logger.info('info message')
logger.warn('warn message')
logger.error('error message')
logger.critical('critical message')
```

### yield和yield from

#### 三个概念：

- 生成器 
	一边循环一边计算的机制，称为生成器：generator
- 可迭代
	可迭代是指一种可以在容器中逐个提取元素的能力。
	必须具备`__iter__()`
- 迭代器
    `__iter__()`
    `__next__()`

yield写斐波那契

``` python
def fab_yield(max):
    n, a, b = 0, 0, 1
    while n<max:
        yield b
        # print b
        a, b = b, a + b
        n = n + 1
f=fab_yield(100)
print(type(f))
for i in f:
    print(i)
```

#### yield from

> 总之大意是原本的yield语句只能将CPU控制权还给直接调用者，当你想要将一个generator或者coroutine里带有yield语句的逻辑重构到另一个generator（原文是subgenerator）里的时候，会非常麻烦，因为外面的generator要负责为里面的generator做消息传递；所以某人有个想法是让python把消息传递封装起来，使其对程序猿透明，于是就有了yield from。

假设A函数中有这样一个语句

> yield from B()

B()返回的是一个可迭代（iterable）的对象b，那么A()会返回一个 generator——照我们的命名规范，名字叫a——那么：

1. b迭代产生的每个值都直接传递给a的调用者。
1. 所有通过`send`方法发送到a的值都被直接传递给b. 如果发送的值是`None`，则调用b的`__next__()`方法，否则调用b的`send`方法。如果对b的方法调用产生`StopIteration`异常，a会继续执行`yield from`后面的语句，而其他异常则会传播到a中，导致a在执行`yield from`的时候抛出异常。
1. 如果有除`GeneratorExit`以外的异常被throw到a中的话，该异常会被直接throw到b中。如果b的`throw`方法抛出`StopIteration`， a会继续执行；其他异常则会导致a也抛出异常。
1. 如果一个`GeneratorExit`异常被throw到a中，或者a的`close`方法被调用了，并且b也有`close`方法的话，b的`close`方法也会被调用。如果b的这个方法抛出了异常，则会导致a也抛出异常。 反之，如果b成功close掉了，a也会抛出异常，但是是特定的`GeneratorExit`异常。
1. a中`yield fro`m表达式的求值结果是b迭代结束时抛出的`StopIteration`异常的第一个参数。
1. b中的`return <expr>`语句实际上会抛出`StopIteration(<expr>)` 异常，所以b中return的值会成为a中`yield from`表达式的返回值。


#### 关于协程Coroutine

- 不是线程切换，而是由程序自身控制，因此，没有线程切换的开销
- 不需要多线程的锁机制，因为只有一个线程，也不存在同时写变量冲突，在协程中控制共享资源不加锁，只需要判断状态就好了，所以执行效率比多线程高很多。



---
