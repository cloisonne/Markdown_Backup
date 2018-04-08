---
title: 百万英雄答题辅助(记一次成功的pull request)
categories:
  - Codes
tags:
  - Python
permalink: auto-million-heroes
abbrlink: 18682
date: 2018-01-12 15:36:29
---

<h2 id="intro">前言</h2>在gayhub上clone了别人的辅助，由跳一跳改动而来的，效果不太好，自己做了debug和改进并PR到作者。


<!-- more -->

---------------

### 代码分析

全部代码见gayhub：

<div class="github-widget" data-repo="cloisonne/MillionHeroes"></div>

#### 不同分辨率适配

config采用json格式，使用起来非常方便。

``` py
def open_accordant_config():
    """
    调用配置文件
    """
    screen_size = _get_screen_size()
    print(screen_size)
    config_file = "{path}/config/{screen_size}/config.json".format(
        path=sys.path[0],
        screen_size=screen_size
    )
    if os.path.exists(config_file):
        with open(config_file, 'r') as f:
            print("Load config file from {}".format(config_file))
            return json.load(f)
    else:
        with open('{}/config/default.json'.format(sys.path[0]), 'r') as f:
            print("Load default config")
            return json.load(f)


def _get_screen_size():
    """
    获取手机屏幕大小
    """
    size_str = os.popen('adb shell wm size').read()
    if not size_str:
        print('请安装 ADB 及驱动并配置环境变量')
        sys.exit()
    m = re.search(r'(\d+)x(\d+)', size_str)
    if m:
        #return "{height}x{width}".format(height=m.group(2), width=m.group(1))
        #print("{width}x{height}".format(height=m.group(2), width=m.group(1)))
        return "{width}x{height}".format(height=m.group(2), width=m.group(1))
    return "1080x1920"
```

#### 换用百度OCR

使用了百度aip

``` py
get_text_from_image = partial(bai_get_text,
                              app_id=config['app_id'],
                              app_key=config['app_key'],
                              app_secret=config['app_secret'],
                              api_version=config['api_version'][0],
                              timeout=5)
							  
    im = Image.open(r"./screenshot.png")
    screen_end = time.time()
    print('截图用时：' + str(screen_end - start) + '秒')

    # region = im.crop((70,300, 1010,600))    #裁剪的区域 百万超人 手机1080*1920 高度范围300~600 // 芝士超人250-350
    # region = im.crop((70,200,650,400))    #裁剪的区域 百万超人 手机720*1080 高度范围300~600 // 芝士超人250-350

    region = (tuple(region_kind))
    region = im.crop(region)
    region.save("./crop_test1.png")

    f = open('./crop_test1.png', 'rb')
    img_data = f.read()
    f.close()

    ocr_start =time.time()

    keyword = get_text_from_image(
        image_data=img_data,
    )
    print(keyword)
```

#### 网页抓取流程优化

省去了不需要的内容，提升速度。

``` py
def page(html):
    # 初始化
    soup = BeautifulSoup(html, 'lxml')
    results = []

    # 获取结果来源
    result_set = soup.find(id='content_left')  # 结果全显示在页面左边
    if result_set is None:
        print("抓取失败，跳过")
        #return
    else:
        result_set = result_set.find_all('div', class_='c-container')  # 结果class固定，其余为硬广
        #print(result_set)
    #c_abstract = None
    for i in range(len(result_set)):  # 因为要index所以就用range来
        result = result_set[i]  # 其实就是result_div
        if 'result-op' not in result['class']:  # 不是软广
            r_from = result.find(class_='c-abstract')
            if not r_from:
                return None
            for em in r_from.find_all('em'):  # 移除abstract中的em标签
                em.unwrap()
            c_abstract = r_from.get_text()
            print(c_abstract)
    return
```

#### 主程序流程优化

加入了流程的优化，把load config的时间提前，便于节约时间。

``` py
# -*- coding:utf-8 -*-

import urllib.request, sys,base64,json,os,time,baiduSearch,screenshot,re
from PIL import Image
from common import config
from functools import partial
from common.baiduocr import get_text_from_image as bai_get_text


index = input("input: 百万英雄-1 冲顶大会-2 芝士超人-3\n")
config = config.open_accordant_config()
region_kind = config['region_million']
if index=='1':
    region_kind = config['region_million']
elif index=='2':
    region_kind = config['region_top']
elif index=='3':
    region_kind = config['region_super']
else:
    print('输入格式错误')

get_text_from_image = partial(bai_get_text,
                              app_id=config['app_id'],
                              app_key=config['app_key'],
                              app_secret=config['app_secret'],
                              api_version=config['api_version'][0],
                              timeout=5)

def start_answer():
    input('题目出现后按回车：\n')

    start = time.time()
    # 开始截图
    #screenshot.check_screenshot()

    screenshot.pull_screenshot()

    im = Image.open(r"./screenshot.png")
    screen_end = time.time()
    print('截图用时：' + str(screen_end - start) + '秒')

    # region = im.crop((70,300, 1010,600))    #裁剪的区域 百万超人 手机1080*1920 高度范围300~600 // 芝士超人250-350
    # region = im.crop((70,200,650,400))    #裁剪的区域 百万超人 手机720*1080 高度范围300~600 // 芝士超人250-350

    region = (tuple(region_kind))
    region = im.crop(region)
    region.save("./crop_test1.png")

    f = open('./crop_test1.png', 'rb')
    img_data = f.read()
    f.close()

    ocr_start =time.time()

    keyword = get_text_from_image(
        image_data=img_data,
    )
    print(keyword)
    ocr_end = time.time()
    print('OCR用时：' + str(ocr_end - ocr_start) + '秒')

    try:
        baiduSearch.search(keyword)
    except:
        print('error')
        pass

    end = time.time()
    print('程序用时：' + str(end - start) + '秒')
    print(keyword)


#---- 主程序
while(1):
    start_answer()



```

---

### PR流程


1. folk该repository

1. clone该repository

1. 加入远程连接

	git remote -v
	
	git remote add upstream https://github.com/**/*.git
	
1. 建立新的分支

	git checkout -b name
	
1. 修改commit 并push到新分支

1. 发起PR  "New pull requests"

1. 等待merge

---

### 总结

#### 程序可改进的

加入不同搜索引擎的词频，相关性等值，可以实现自动答题，目前已经有人实现了相关功能。

#### 程序的复制性

整个程序复制性很强，pull截图拉到python里处理可以解决大部分手机的问题。

---
