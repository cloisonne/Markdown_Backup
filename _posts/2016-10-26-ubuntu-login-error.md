---
title: Ubuntu开机登录界面死循环
categories:
  - Notes
tags:
  - Ubuntu
permalink: ubuntu-login-error
date: 2016-10-26 20:25:40
---

<h2 id="intro">前言</h2>关于Ubuntu 16.04突然无法开机，在登录界面进入死循环的解决方法。


<!-- more -->

---------------

### 处理过程

首先，Ctrl+Alt+F1进入了命令行，试了一些重启命令无果，例如：

```
service gdm3 restart
```

这里补充一下在控制台临时使用英文的命令（中文乱码）

```
export LANG=C
```


随后remove了两个上次关机前安装的软件，还是无果。

看网上这个问题出现的主要地方有以下：

- 显卡驱动
- 文件权限
- 安装了一些冲突的软件

各种无果后十分发狂，随后才查找到了原来登录失败会生成如下的log文件：

`/home/user/.xsession-errors`

主要的几行如下：

```
(WARN-6410 /build/fcitx-J2yftF/fcitx-4.2.9.1/src/lib/fcitx-config/fcitx-config.c:922) 配置项不合法:  行150 缺少'='
(WARN-6410 /build/fcitx-J2yftF/fcitx-4.2.9.1/src/module/dbus/dbusstuff.c:246) DBus Service Already Exists
(ERROR-6410 /build/fcitx-J2yftF/fcitx-4.2.9.1/src/lib/fcitx/instance.c:440) Exiting.
sogou-qimpanel-watchdog: Fatal IO error 11 (资源暂时不可用) on X server :0.


```

很显然，问题出在fcitx和sogou输入法中，卸载后重装，问题解决。

---

### Ubuntu日志文件总结

**Linux Log files and usage**

=> /var/log/messages : General log messages

=> /var/log/boot : System boot log

=> /var/log/debug : Debugging log messages

=> /var/log/auth.log : User login and authentication logs

=> /var/log/daemon.log : Running services such as squid, ntpd and others log message to this file

=> /var/log/dmesg : Linux kernel ring buffer log

=> /var/log/dpkg.log : All binary package log includes package installation and other information

=> /var/log/faillog : User failed login log file

=> /var/log/kern.log : Kernel log file

=> /var/log/lpr.log : Printer log file

=> /var/log/mail.* : All mail server message log files

=> /var/log/mysql.* : MySQL server log file

=> /var/log/user.log : All userlevel logs

=> /var/log/xorg.0.log : X.org log file

=> /var/log/apache2/* : Apache web server log files directory

=> /var/log/lighttpd/* : Lighttpd web server log files directory

=> /var/log/fsck/* : fsck command log

=> /var/log/apport.log : Application crash report / log file


**shell下的命令**：

``` shell
tail -f /var/log/apport.log
more /var/log/xorg.0.log
cat /var/log/mysql.err
less /var/log/messages
grep -i fail /var/log/boot
```
