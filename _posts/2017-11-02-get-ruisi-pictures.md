---
title: 睿思图片爬取实战（附代码）
categories:
  - Code
tags:
  - Python
  - 爬虫
permalink: get-ruisi-pictures
date: 2017-11-02 15:24:46
---

<h2 id="intro">前言</h2>主要使用了Requests加Beautifulsoup，没有用Requests的session，选择了使用原始的get和post方法，爬取了约3.5G的睿思摄影板块图片。


<!-- more -->

---------------

### 思路

什么是睿思？

睿思: <http://rs.xidian.edu.cn/forum.php> 是西电校内最大的PT站，长这样：
![睿思主页][1]

之前学习了[Requests][2]库和[BeautifulSoup][3]库，已经可以爬取一些简单的网站了，选择睿思是因为不跑校外流量，爬取图片又相对简单易行，对于基本没看过python的我来说，比较现实。
最初是想爬取睿思的缘聚睿思板块，但图片太少，最终选择爬取摄影板块，长这样：
![摄影主页][4]

乍一看以为是瀑布流，实际分析过后就会发现瀑布最后刷新出来的其实是`page=2`，也就是说读取不同的page就可以抓取整个板块的照片了。

为了防止抓到回复中的照片（感觉有点担忧过度，回复带的图片大多是论坛的静态图片），只抓取每个帖子第一楼的照片，然后将其输出到电脑保存。

爬取结果，共3.5G，去除10K以下的无效图片，有2815张：

![爬取结果][5]

---

### 代码

详细代码请看gayhub：

<div class="github-widget" data-repo="cloisonne/Ruisi_Pic_Download"></div>



**formhash**是后期发帖需要的一个值，这里一并获取：
``` python
    def getFormhash(self,url):
        #formhash_url = config.FORMHASHURL
        formhash_text = self.getHTMLText(url)
        formhash_soup = BeautifulSoup(formhash_text,'lxml')
        formhash_tag = formhash_soup.find('input',attrs={'name':'formhash'})
        formhash = formhash_tag('value')
        return formhash
```
**cookies**采用cookiejar：
``` python
self.cookie = cookiejar.CookieJar()
```

为了保持登陆状态，需要一个全局cookies：
``` python
global cookies
cookies = wb_data.cookies
```

获取的图片链接有所不同，需要进行选择处理：
``` python
for i in img_url_list:
     if i[0] == 's':
        pass
    elif i[0] == 'h':
        true_list.append(i)
    elif i[0] == '.':
        true_list.append(config.DOMAIN+i[2:])
    else:
        pass
```
由于url中含有非法字符`?`，图片命名中利用该点特征，使用异常来保证输出图片的正确性：
``` python
try:
    if not os.path.exists(root):
        os.mkdir(root)
    if not os.path.exists(path):
        r = requests.get(url,headers=self.headers, cookies=cookies)
        with open(path, 'wb') as f:
            f.write(r.content)
            f.close()
            print(path+'  succeed')
except:
    print("失败一次")   #有url包含非法字符
```

---

### 问题总结


 - 最初是对discuz不了解，formhash和cookie比较麻烦，登陆状态的保持不知道如何做。
 
 - 使用Requests的session更简单，不需要cookies和构造复杂header，事实上，get和post方法调用的是requests方法，而requests最终也是调用的session。
 
 - 第一次完整的写python程序，结构有很大问题，总是想结构化，python还是代码越少越好。

 - 下面开始学习scarpy，然后进行简单的多线程抓取。
 
 - 对睿思的抓取继续下去的话可以抓发帖时间和发帖对象做用户行为分析。


  [1]: http://o7myibfc7.bkt.clouddn.com/ruisi_page.jpg?imageView2/3/w/600/h/300/q/90
  [2]: http://docs.python-requests.org/zh_CN/latest/index.html
  [3]: https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html#
  [4]: http://o7myibfc7.bkt.clouddn.com/sheying_page.jpg?imageView2/3/w/600/h/300/q/90
  [5]: http://o7myibfc7.bkt.clouddn.com/ruisi_pictures.jpg?imageView2/3/w/600/h/300/q/90