---
title: python爬虫第一课-requests库
categories:
  - Codes
tags:
  - Python
  - 爬虫
permalink: about-requests
date: 2017-10-27 19:52:21
---

<h2 id="intro">前言</h2>学习python爬虫的第一课，涉及requests库、robots协议，及一个保存网页图片实例。


<!-- more -->

---------------



## 安装

> 这里使用pycharm，学生用户可以通过edu邮箱免费使用，安装库都十分方便。

---

## 初识requests代码

```

>>> import requests
>>> r = requests.get('https://www.baidu.com',auth = ('user','pass'))
>>> r.status_code
200
>>> r.headers['content-type']
'text/html'
>>> r.headers
{'Set-Cookie': 'BDORZ=27315; max-age=86400; domain=.baidu.com; path=/', 'Transfer-Encoding': 'chunked', 'Date': 'Sat, 28 Oct 2017 10:49:03 GMT', 'Server': 'bfe/1.0.8.18', 'Content-Type': 'text/html', 'Content-Encoding': 'gzip', 'Cache-Control': 'private, no-cache, no-store, proxy-revalidate, no-transform', 'Last-Modified': 'Mon, 23 Jan 2017 13:23:55 GMT', 'Connection': 'Keep-Alive', 'Pragma': 'no-cache'}
>>> r.encoding #根据头部 编码
'ISO-8859-1'
>>> r.text
'<!DOCTYPE html>\r\n<...
>>> r.apparent_encoding #根据内容 备选编码
'utf-8'
>>> r.content  #二进制形式
```

### requests库异常

遇到网络问题（如：DNS 查询失败、拒绝连接等）时，Requests 会抛出一个 ConnectionError 异常。
如果 HTTP 请求返回了不成功的状态码， Response.raise_for_status() 会抛出一个 HTTPError 异常。
若请求超时，则抛出一个 Timeout 异常。
若请求超过了设定的最大重定向次数，则会抛出一个 TooManyRedirects 异常。
所有Requests显式抛出的异常都继承自 requests.exceptions.RequestException 。

### 爬取网页的通用框架

    import requests
    
    def getHTMLText (url) :
        try:
            kv = {'user-agent':'Mozilla/5.0'}
            r = requests.get(url,timeout=30,headers=kv)
            r.raise_for_status()  #检查状态码
            r.encoding = r.apparent_encoding
            return r.text
        except:
            return "产生异常"
            
    if __name__ == "__main__":
        url = "http://www.baidu.com"
        print(getHTMLText)

---
		
## HTTP协议

 - GET 请求获取URL位置的资源 
 - HEAD 请求获取URL位置资源的响应消息报告，即获得该资源的头部信息 
 - POST请求向URL位置的资源后附加新的数据 
 - PUT 请求向URL位置存储一个资源，覆盖原URL位置的资源 
 - PATCH 请求局部更新URL位置的资源，即改变该处资源的部分内容
 - DELETE 请求删除URL位置存储的资源


 ---
 
 ## robots协议
 
 Robots Exclusion Standard
 
 基本语法：

    # 注释，*代表所有，/代表根目录
    User‐agent: *
    Disallow: /
---


## 百度/360搜索代码
 
``` python
 import requests
 keyword = 'Python'
 try:
    kv = {'wd':keyword}
    r = requests.get("http://www.baidu.com/s",params=kv)
    print(r.request.url)
    r.raise_for_status()
    print(len(r.text))
expect
    print("爬取失败")
    
 import requests
 keyword = 'Python'
 try:
    kv = {'q':keyword}
    r = requests.get("http://www.baidu.com/s",params=kv)
    print(r.request.url)
    r.raise_for_status()
    print(len(r.text))
expect
    print("爬取失败")
    
```

---

## 图片爬取

```
import requests
import os
url = ""
root = "D://pics//"
path = root+url.split('/')[-1]
try:
    if not os.path.exists(root):
        os.mkdir(root)
    if not os.path.exists(path):
        r = requests.get(url)
        with open(path, 'wb') as f:
            f.write(r.content)
            f.close()
            print("文件保存成功")
    else:
        print("文件已存在")
expect:
    print("爬取失败")
```