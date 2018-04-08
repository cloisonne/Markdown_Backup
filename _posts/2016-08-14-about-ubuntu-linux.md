---
title: 使用Ubuntu系统中遇到的问题
categories:
  - Notes
tags:
  - Ubuntu
  - Linux
  - Gnome3
permalink: about-Ubuntu-linux
abbrlink: 61830
date: 2016-08-14 17:10:54
---

<h2 id="intro">前言</h2>记录自己在Ubuntu系统学习和建设中遇到的问题和解决方案，一些小tip选择加入[TiddlyWiki](/wiki)


<!-- more -->

## Ubuntu系统问题

### 删除显示为deinstall的linux内核

```
uname -a  
dpkg --get-selections|grep linux  
sudo dpkg -P linux-image-3.5.0-4[2-9]-generic  
sudo dpkg -P linux-image-3.5.0-51-generic 
```

### 嵌入式上的wifi网络

```
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```

添加

```
network={
    ssid="The_ESSID_from_earlier"
    psk="Your_wifi_password"
}
```

重启接口
```
sudo ifup wlan0
```

### 嵌入式上如何启动图形界面

在shell中输入：`startx` 或者 `init 5`


为了在Linux启动时直接进入Console界面，我们可以编辑/etc/inittab文件。找到id:5: initdefault:这一行，将它改为id:3:initdefault:后重新启动系统即可。我们看到，简简单单地将5改为3，就能实现启动时进入X-window图形操作界面或Console字符界面的转换，这是因为Linux操作系统有六种不同的运行级(run level)，在不同的运行级下，系统有着不同的状态，这六种运行级分别为：
0：停机(记住不要把initdefault 设置为0，因为这样会使Linux无法启动 )
1：单用户模式，就像Win9X下的安全模式。
2：多用户，但是没有 NFS 。
3：完全多用户模式，标准的运行级。
4：一般不用，在一些特殊情况下可以用它来做一些事情。
5：X11，即进到 X-window 系统。
6：重新启动 (记住不要把initdefault 设置为6，因为这样会使Linux不断地重新启动)。


### 旧版本ubuntu无法更新问题

旧版本已经不再支持，需要换用old源：

``` 
deb http://old-releases.ubuntu.com/ubuntu/ raring main universe restricted multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring main universe restricted multiverse

deb http://old-releases.ubuntu.com/ubuntu/ raring-security main universe restricted multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring-security main universe restricted multiverse

deb http://old-releases.ubuntu.com/ubuntu/ raring-updates main universe restricted multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring-updates main universe restricted multiverse

deb http://old-releases.ubuntu.com/ubuntu/ raring-backports main restricted universe multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring-backports main restricted universe multiverse

deb http://old-releases.ubuntu.com/ubuntu/ raring-proposed main restricted universe multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring-proposed main restricted universe multiverse
```

源文件的地址：

> /etc/apt/sources.list

### 删除多余内核

解决boot分区空间不足。

```bash
sudo dpkg --get-selections |grep linux
uname -a
sudo apt-get purge linux-headers-&linux-image-
sudo update-grub
```

-------------

### linux下的文件和文件夹权限问题

ubuntu下查看权限的命令为：

    ls -l filename

ubuntu下设置权限的命令为：

> 一共有10位数
其中： 最前面那个 - 代表的是类型
中间那三个 rw- 代表的是所有者（user）
然后那三个 rw- 代表的是组群（group）
最后那三个 r-- 代表的是其他人（other）

> 后面那9位数：
r 表示文件可以被读（read）
w 表示文件可以被写（write）
x 表示文件可以被执行（如果它是程序的话）
\- 表示相应的权限还没有被授予

 

现在该说说修改文件权限了

 

在终端输入：

chmod o+w xxx.xxx

表示给其他人授予写xxx.xxx这个文件的权限

 

chmod go-rw xxx.xxx

表示删除xxx.xxx中组群和其他人的读和写的权限

 

`其中：`

u 代表所有者（user）

g 代表所有者所在的组群（group）

o 代表其他人，但不是u和g （other）

a 代表全部的人，也就是包括u，g和o

 

r 表示文件可以被读（read）

w 表示文件可以被写（write）

x 表示文件可以被执行（如果它是程序的话）

 

rwx也可以用数字来代替

r ------------4

w -----------2

x ------------1

\- ------------0

 

`行动：`

\+ 表示添加权限

\- 表示删除权限

= 表示使之成为唯一的权限

 

当大家都明白了上面的东西之后，那么我们常见的以下的一些权限就很容易都明白了：

-rw------- (600) 只有所有者才有读和写的权限

-rw-r--r-- (644) 只有所有者才有读和写的权限，组群和其他人只有读的权限

-rwx------ (700) 只有所有者才有读，写，执行的权限

-rwxr-xr-x (755) 只有所有者才有读，写，执行的权限，组群和其他人只有读和执行的权限

-rwx--x--x (711) 只有所有者才有读，写，执行的权限，组群和其他人只有执行的权限

-rw-rw-rw- (666) 每个人都有读写的权限

-rwxrwxrwx (777) 每个人都有读写和执行的权限


转载自：http://www.linuxsky.org/doc/newbie/200801/220.html
若分配给某个文件所有权限，则利用下面的命令：

`sudo chmod -R 777` 文件或文件夹的名字（其中sudo是管理员权限）

-----

## grub2手动引导命令

```
grub rescue> ls  //查看分区
grub rescue> ls (hd0,gpt8) //寻找根分区
grub rescue> set
grub rescue> set  prefix=(hd0,gpt8)/boot/grub
grub rescue> set  root=hd0,gpt8
grub rescue> insmod normal
grub rescue> normal

```

---
## Gnome3问题

-----
- **对Gnome3的感受就是爱并折磨着～**

### fcitx和ibus在gnome3下的冲突问题

因为在Ubuntu下对搜狗输入法的痴爱（皮肤和WIN下的皮肤通用，并且词库强大），必须使用fcitx来作为输入法管理工具，而gnome3和ibus是绑定关系，天然的gnome3自带ibus，二者冲突导致了fcitx在QT4等界面下会出现失效，盲目卸载ibus还会出现系统设置崩溃的问题。

- 对于ibus的看法

> ibus是gnome3的天然输入法，盲目卸载可能会导致问题，最关键的两个套件包括**ibus-gtk**和**ibus-gtk2**，其余的ibus组件可以卸载目前没有发现问题。

```sh
sudo apt-get remove ibus
sudo apt-get autoremove
```


- fcitx的配置

> 出问题的时候不妨尝试**卸载fcitx和搜狗输入法**，然后重新安装搜狗输入法和fcitx

```bash
sudo apt-get install fcitx-pinyin
im-config -s fcitx -z all_ALL
```

> 此时 /etc/X11/xinit/xinput.d/fcitx 应为

```
XIM=fcitx
XIM_PROGRAM=/usr/bin/fcitx
XIM_ARGS=""
GTK_IM_MODULE=fcitx
QT4_IM_MODULE=fcitx
DEPENDS="fcitx"
```

> 可通过查看环境变量来判断

```bash
env | grep fcitx
```

> 也可以通过fcitx自带的诊断工具`fcitx-diagnose`

- **提示**

在init文件中直接export *fcitx* 变量的方法不可取！

-----

## Ubuntu工具收集

### Boot-repair

修复双系统引导

```
sudo -i
add-apt-repository ppa:yannubuntu/boot-repair && apt-get update
apt-get install -y boot-repair && boot-repair
```


### grub神器 GRUB Customizer

- 地址

[Grub Customizer](https://launchpad.net/grub-customizer)

- 安装方法

``` bash
sudo add-apt-repository ppa:danielrichter2007/grub-customizer
sudo apt-get update
sudo apt-get install grub-customizer
```

- 一些tips

利用Grub Customizer可以轻松的对Ubuntu自带的grub引导进行管理，同时可以很方便地写入MBR，从而对WIN和Linux双系统进行引导管理。

**Grub Customizer可以定制启动时的grub界面**

事实上可以直接编辑`/boot/grub/grub.cfg`文件进行相应的配置，有了Grub Customizer更为方便。

### shadowsocks-QT5 科学上网

[shdowsocks-QT5](https://github.com/shadowsocks/shadowsocks-qt5)

```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```

`注意`: 注意如果代理类型是SOCKS5，请在系统设置的HTTP代理一栏留空。若代理类型是HTTP(S)，请在SOCKS5代理一栏留空

### wine-qq和搜狗输入法

> 使用[优麒麟](http://www.ubuntukylin.com/application/)提供的deb安装包安装，没有太大困难。

-----
