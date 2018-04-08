---
title: 博客搭建过程和工具总结
categories:
  - Notes
tags:
  - Hexo
  - Tools
  - Markdown
  - Github
permalink: blog-building-toolkit
top: 99
abbrlink: 17629
date: 2016-07-18 21:42:13
---
<h2 id="intro">前言</h2>长期记录，很多东西都是边学边做，要培养自己规范记录的习惯。

还不怎么熟悉图床和图链的使用，暂时就用文字叙述，主要还是给自己看。

<!-- more -->
## 2018.3.8更新

- 加入了插件[hexo-abbrlink](https://github.com/rozbo/hexo-abbrlink/) 目前文章ID为随机数，解决了gitment laber不够短的问题

---
## 2018.3.7更新

- 修改了gitment生成ID的方式，参考[添加Gitment评论系统踩过的坑](http://xichen.pub/2018/01/31/2018-01-31-gitment/)
- 增加了插件hexo-admin，踩坑，删除nodejs插件目录后重装恢复，增加了hexo编辑文档的方式
		cnpm install

---
## 2018.1.23更新

- 用python写了gitment自动初始化工具

---
## 2018.1.22更新

- 加入了[gitment](https://github.com/imsun/gitment/)评论，修改了gitment的css样式，匹配了我的blog
- 在留言板中也加入了gitment

---

## 2018.1.17更新

- 新建了一个wordpress小站[我的文字小站](https://ykk.000webhostapp.com/)，托管在三蛋空间
- 之前换了友言评论，差强人意，有时间换成gitment
- 修改主站，增加了跳转链接<http://ykksmile.top/writing>,并修改了博客左边栏适配
- 考虑要不要自己新开一个主题，原作者不更新了，我还有足够的精力吗？

---

## 博客搭建过程

 - 博客搭建主要是依赖[Hexo][0.1]，这是一个`快速、简洁且高效的博客框架`，支持的主题十分丰富。
 - 搭建环境是Ubuntu 16.04，因为之前做毕设课题是`SDN`和`物联网`，硬件驱动都是在Linux中编译；后面研究方向又是集中在`GNU Radio`上，所以早早的就进了Linux这个大坑。
 - 在Linux里遇到炒鸡多的问题，就连装个QQ也折腾了好久，好多东西比如`Ubuntu中文站`都是后来才知道的。后面会再开一贴记录Linux折腾的过程，之前的大多不记得了，就从今往后吧。
    
[0.1]: https://hexo.io/zh-cn/index.html "Hexo中文主页"

-----

### Github

做静态主页必然是要从[Github][0.2]入手：
    
 - 注册账号，建立repository，使用Github提供的Github Pages。
 - 部署Hexo，需要前置程序：
	 - Node.js
	 - Git
 - 安装和建站（省去folder）：
        npm install -g hexo-cli
        hexo init
        npm install
 - 学习使用git命令，下载主题仓库
    		git clone URL
 - 基础命令
		hexo new [layout] <title>
		hexo clean
		hexo g
		hexo s
 - 主题修改过后使用部署插件
		npm install hexo-deployer-git --save
		hexo deploy
 - 这里Github部署使用SSH，需要在官网匹配一个公钥，注意branch的填写
> 初始地址：<http://cloisonne.github.io/> (cloisonne为我的ID)

[0.2]: https://github.com/ "Github主页"

-----

### 补充-关于nodejs的安装

官网直接下载完整包 [Node.js](https://nodejs.org/en/)

``` bash
cd node-v-linux-x64/bin
ls
./node -v
```

设置全局：

``` bash
ln -s /home/node-v-linux-x64/bin/node /usr/local/bin/node
ln -s /home/node-v-linux-x64/bin/npm /usr/local/bin/npm
```

后面安装的hexo目录位于nodejs/bin 也需要设置全局

```bash
sudo ln -s /opt/node-v4.4.7-linux-x64/bin/hexo /usr/local/bin/hexo
```

-----

### 补充-Windows下nodejs的问题

国内npm被墙 采用淘宝镜像[cnpm][1]

    $ npm install -g cnpm --registry=https://registry.npm.taobao.org


### 补充-关于私钥公钥的生成

```
$ git config --global user.name "cloisonne"
$ git config --global user.email "tylerluan@163.com"
$ ssh-keygen -t rsa -C "tylerluan@163.com"
```

生成两个文件id_rsa和id_rsa.pub 公钥位于`id_rsa.pub`
### Next主题

一开始是使用的[Next主题][1.1]，这个主题的好处在于有很强大的说明文档。
利用自带的`_config.yml`可以完成基础的定制，也借助它我熟悉了Hexo的主要框架和一个blog的主要结构。

> 在Next中首先遇到的问题在于如何修改底部标签，事实上如果了解Hexo主题的开发过程，这一问题就很好解决。
自己摸索找到了`theme/layout/_partial`这里放着关于布局的JavaScript文件。

包括学习Markdown，并且发表第一篇post，过程一直很顺利，后面[多说][1.2]在手机适配端出现了问题，恰逢毕业季，很久都未解决，迫不得已放弃。

[1.1]: http://theme-next.iissnan.com/ "干净整洁的主题"
[1.2]: http://duoshuo.com/ "多说评论系统主页"

### Yelee主题

后来看上了[Yilia][2.1]主题，主题特点是简单，注重对内容的突出。但是简单就意味着缺少了很多功能，比如搜索等等，[Yelee][2.2]正是在Yilia基础上更改的主题，增加了很多可用的功能。

> 本主题基于主题 Hexo-Theme-Yilia 修改而来，在此再次感谢原作者 Litten。修复了一些 bugs，改变了大量的样式，添加了不少特性。对原主题百般折腾后，发觉变动越来越大，索性就发布个新主题了，主题随我微博名 "夜Yelee" 。个人喜欢简洁的样式，重视内容的浏览，同时希望作为个人网站的博客，能稍微凸显出博主个性。各种修改折腾大抵基于以上考虑。

这里附上Yelee主题使用说明，基本配置大致都有了：[Yelee主题使用说明][2.3]

 - 关于修改左边栏上方的**Overlay**配色，这一段代码写在`yelee/source/js/main.js`
 - 关于如何解决Hexo置顶问题：[解决Hexo置顶问题 ][2.4]
 - 多说样式的修改：[多说样式折腾记录 — 添加 UA 浏览器标识、旋转头像等 ][2.5]

[2.1]: https://github.com/litten/hexo-theme-yilia "Yilia主题"
[2.2]: https://github.com/MOxFIVE/hexo-theme-yelee "Yelee主题"
[2.3]: http://moxfive.coding.me/yelee/ "Yelee主题使用说明"
[2.4]: http://www.netcan666.com/2015/11/22/%E8%A7%A3%E5%86%B3Hexo%E7%BD%AE%E9%A1%B6%E9%97%AE%E9%A2%98/ "解决Hexo置顶问题"
[2.5]: http://moxfive.xyz/2015/09/29/duoshuo-style/ "对多说评论的改造"

### 各种插件

Hexo的各种插件，包括sitemap、rss、git部署等等：

``` bash
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
npm install hexo-renderer-marked@0.2 --save
npm install hexo-renderer-stylus@0.2 --save
npm install hexo-generator-feed@1 --save
npm install hexo-generator-sitemap@1 --save
npm install hexo-generator-baidu-sitemap --save

```

**置顶插件**

        $ npm uninstall hexo-generator-index --save
    $ npm install hexo-generator-index-pin-top --save



第三方插件，主要有：

 ~~- 多说评论~~
 - 百度统计
 - 不蒜子统计
 - sitemap
 - RSS

### coding部署

因为百度屏蔽了Github的抓取，为了让百度能够正常抓取，这里借鉴了知乎
[如何解决百度爬虫无法爬取搭建在Github上的个人博客的问题][3.1]
完成了Github和国内Coding的共同部署，并通过DNS实现了国内访问Coding，国外访问Github。

附上Deploy的配置（Github的branch `master`可以省略）：

```
deploy:
- type: git
  repo: https://github.com/cloisonne/cloisonne.github.io
- type: git
  repo: https://git.coding.net/cloisonne/cloisonne.git
  branch: coding-pages
```

~~登录方式依然是SSH，SSH隔段时间就要重新输入密码，最近还在寻找更好的方式。~~

```
deploy:
- type: git
  repo: git@github.com:cloisonne/cloisonne.github.io.git
- type: git
  repo: git@git.coding.net:cloisonne/cloisonne.git
  branch: coding-pages
```

已解决每次需要输入密码的问题。

[3.1]: http://www.zhihu.com/question/30898326/ "如何解决百度爬虫无法爬取搭建在Github上的个人博客的问题"

### 域名和DNS

因为主要是以学习为主，这里使用了免费的[tk域名][4.1]，注册了~~<http://smileykk.tk>~~　已换成<http://ykksmile.top>

> 注意tk域名目前免费最大时长为12小时，建议一次注册选择最大时长，注册时间选错了可以注销重新申请。

DNS使用了流行的[DNSPod][4.2],具体过程比较简单，参考了[解决 Github Pages 禁止百度爬虫的方法][4.3]

- 将域名解析到DNSPod提供的地址

```
	f1g1ns1.dnspod.net
	f1g1ns2.dnspod.net
```

- 修改DNSPod解析记录
- 添加Github的CNAME文件,添加Coding自定义域名

[4.1]: http://www.dot.tk/zh/index.html?lang=zh/ "注册Dot TK域名"
[4.2]: https://www.dnspod.cn/ "好用的DNS工具"
[4.3]: http://bblove.me/2015/11/25/how-to-solve-the-problem-that-github-blocks-the-baidu-spider/ "参考的DNS方案"

### TiddlyWiki

TiddlyWiki是一个轻量级的静态的个人wiki工具，具体使用方法参考TigglyWiki的官方文档：

在Hexo中的应用：
> 放在theme/source/wiki/index.html 即可通过`http://URL/wiki/`访问

TiddlyWiki的Markdown插件安装：

> To add the plugin to your own TiddlyWiki5, just drag this link to the browser window:

```
$:/plugins/tiddlywiki/markdown
```

## 工具链接

### 网站搭建

1. **My blog**: <http://smileykk.tk> / <http://cloisonne.github.io> / <http://cloisonne.coding.me/cloisonne>
1. **GitHub**: <https://github.com/cloisonne>
1. **Coding**: <https://coding.net/>
1. **Hexo**: <https://hexo.io/zh-cn>
1. **Node.js**: <https://nodejs.org/en/>
1. **Next主题**: <http://theme-next.iissnan.com>
1. **Yelee主题**: <http://moxfive.coding.me/yelee>
1. **favicon制作**: <http://www.atool.org/ico.php>
2. **淘宝npm镜像**： <https://npm.taobao.org/>

### 网站维护

1. ~~多说评论系统: <http://duoshuo.com/>~~
1. **DNSPod**: <https://www.dnspod.cn>
1. **TK域名注册**: <http://www.dot.tk/zh/index.html?lang=zh>
1. **域名管理freenom**: <https://my.freenom.com/clientarea.php>
1. **站长工具大全**: <http://tool.lusongsong.com/>

### 相关学习

1. **小书匠Markdown编辑器**: <http://markdown.xiaoshujiang.com/>
1. **简书**: <http://www.jianshu.com>
1. **TiddlyWiki**: <http://tiddlywiki.com/>


  [1]: https://npm.taobao.org/