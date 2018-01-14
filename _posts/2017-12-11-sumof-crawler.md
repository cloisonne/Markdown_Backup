---
title: Python爬虫总结（附拉勾网多进程代理池爬虫）
categories:
  - Code
tags:
  - 爬虫
  - Python
permalink: sumof-crawler
date: 2017-11-11 14:14:19
---

<h2 id="intro">前言</h2>一直秉承增量学习的策略，写了一些爬虫，还没有到需要scrapy的地步，爬取拉勾网遇到了瓶颈，因为反爬策略比较强，在此总结。


<!-- more -->

---------------

### 写过的东西

最近写的都放在了gayhub上：

<div class="github-widget" data-repo="cloisonne/Show-me-the-code_Python"></div>

包括有：`获取代理池`、`爬取bing图片`等等。

### 拉勾网爬虫

分为以下步骤：

- 获取所有板块url，存入mongoDB
- 在所需的url中，爬取所有的工作链接url，存入mongoDB
- 断点续传，多进程爬取所有储存的url中的信息，存入mongoDB
- 数据分析（待完成）

其中

#### 断点续传的实现方式：

``` python
db_url = [i['url'] for i in url_list.find()]
recent_url = [j['url'] for j in job_info.find()]
x = set(db_url)
y = set(recent_url)
rest_of_urls = x-y
```

#### 多进程使用Pool进程池：

``` python
cookies = login_lagou()
pool = Pool(processes=4)
pool.apply_async(get_all_job_info(rest_of_urls,cookies))
print(len(rest_of_urls))
# pool.apply_async(get_all_links_from(channel,cookies))
pool.close()
pool.join()
```

#### 代理测试使用telnet方法：

``` python
def test_link(ip,_port):
    try:
        telnetlib.Telnet(ip, port=_port, timeout=5)
    except:
        print ('connect failed')
    else:
        print ('success')
```

#### 数据库计数器实时监测：

``` python
while True:
    print(job_info.find().count())
    time.sleep(5)
```

----

### 高级爬虫技术总结

**高端反爬技巧**：

- 校验 User-Agent（没用）
- 图片假链（人点不到，爬虫必中）
- 阶梯访问量控制

> 基于访问数量，人类的访问一般集中在短时间内大部分网页，爬虫是线性增长，可设置短期策略和长期策略识别非人类。

**常见的反反爬技巧**：

- User-Agent池
- Referer保持
- 代理池
- 图像识别验证码
- 登陆状态cookies保持
- 多账号反爬
- 分布式爬虫
- selenium

---

### 未来工作

重心主要在数据分析和机器学习上，爬虫如果再有接触的话，应当写一个自己的fetcher类，糅合代理、多线程、异步IO、断点续传等功能。

参考[python爬虫抓网页的总结](http://outofmemory.cn/code-snippet/1653/python-pachong-zhua-wangye-summary)
