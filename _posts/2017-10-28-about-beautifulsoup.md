---
title: python爬虫第二课-BeautifulSoup
categories:
  - Codes
tags:
  - Python
  - 爬虫
permalink: about-beautifulsoup
abbrlink: 44202
date: 2017-10-28 23:03:56
---

<h2 id="intro">前言</h2>主要包括对Beautiful Soup的学习和理解，及一个提取大学排名的实例。


<!-- more -->

---------------

## Beautiful Soup

### BeautifulSoup的对象

Tag

    soup = BeautifulSoup('<b class="boldest">Extremely bold</b>')
    tag = soup.b
    type(tag)
    # <class 'bs4.element.Tag'>

Name

    tag.name
    # u'b'
    
Attributes

    tag['class']
    tag.attrs

NavigableString

    tag.string

prettify()方法

    soup.prettify()
    soup.a.prettify()
    
### BS的遍历
标签树的下行遍历

 - .contents 子节点的列表，将<tag>所有儿子节点存入列表 
 - .children子节点的迭代类型，与.contents类似，用于循环遍历儿子节点
 - .descendants子孙节点的迭代类型，包含所有子孙节点，用于循环遍历

标签树的上行遍历

 - .parent 节点的父亲标签 
 - .parents 节点先辈标签的迭代类型，用于循环遍历先辈节点

标签树的平行遍历

 - .next_sibling 返回按照HTML文本顺序的下一个平行节点标签
 - .previous_sibling 返回按照HTML文本顺序的上一个平行节点标签 
 - .next_siblings 迭代类型，返回按照HTML文本顺序的后续所有平行节点标签
 - .previous_siblings 迭代类型，返回按照HTML文本顺序的前续所有平行节点标签
 
---
 
 

## 信息标记方式和提取

### 三种信息标记方式

1. XML eXtensible Markup Lauguage

		<img src=“china.jpg” size=“10”> … </img>

2. JSON JavaScript Object Notation
 
    有类型的键值对key:value

3. YAML YAML Ain't Markup Language
 
    无类型键值对key:value

### HTML提取方式

**例：**

    from bs4 import BeautifulSoup
    soup = BeautifulSoup(demo,"lxml")
    for link in soup.find_all('a'):
        print(link.get('href'))
        
**find_all**(name, attrs, recursive, string, **kwargs)

 - name : 对标签名称的检索字符串
 - attrs: 对标签属性值的检索字符串，可标注属性检索
 - recursive: 是否对子孙全部检索，默认True
 - string: <>…</>中字符串区域的检索字符串
 
**扩展方法：**
 - <>.find() 搜索且只返回一个结果，同.find_all()参数 
 - <>.find_parents() 在先辈节点中搜索，返回列表类型，同.find_all()参数 
 - <>.find_parent() 在先辈节点中返回一个结果，同.find()参数 
 - <>.find_next_siblings() 在后续平行节点中搜索，返回列表类型，同.find_all()参数 
 - <>.find_next_sibling() 在后续平行节点中返回一个结果，同.find()参数 
 - <>.find_previous_siblings() 在前序平行节点中搜索，返回列表类型，同.find_all()参数 
 - <>.find_previous_sibling() 在前序平行节点中返回一个结果，同.find()参数
 
 
---


## 提取大学排名的例子

``` python
import requests
from bs4 import BeautifulSoup
import bs4

def getHTMLText(url):
    try:
        r = requests.get(url,timeout=30)
        r.raise_for_status()
        r.encoding = r.apparent_encoding
        return r.text
    except:
        return ''

def fillUnvList(ulist,html):
    soup = BeautifulSoup(html,'lxml')
    for tr in soup.find('tbody').children:
        if isinstance(tr,bs4.element.Tag):
            tds = tr('td') #tds = tr.find_all('td')
            ulist.append([tds[0].string,tds[1].string,tds[3].string])


def printUnvList(ulist,num):
    tplt = "{:^10}\t{:^6}\t{:^10}"
    print(tplt.format('排名','学校','总分',chr(12288))) # 中文对齐  pycharm失败
    for i in range(num):
        u = ulist[i]
        print(tplt.format(u[0],u[1],u[2],chr(12288)))
    #print('Suc' + str(num))

def main():
    uinfo = []
    url = 'http://www.zuihaodaxue.cn/zuihaodaxuepaiming2017.html'
    html = getHTMLText(url)
    fillUnvList(uinfo,html)
    printUnvList(uinfo,20)

main()
```

