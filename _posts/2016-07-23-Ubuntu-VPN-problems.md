---
title: VPN问题和科学上网地址
categories:
  - Notes
tags:
  - Ubuntu
  - VPN
date: 2016-07-23 21:20:38
permalink: Ubuntu-VPN-problems
---

<h2 id="intro">前言</h2>因为需要访问`GNU Radio`，需要翻墙，很自然的想到在Ubuntu16.04里使用VPN。
遇到的问题是VPN无法正常启动服务，~~至今仍未解决~~,**该问题已经解决**。

<!-- more -->

-----------------
### 问题描述

> 问题主要是在Ubuntu16.04系统中使用VPN无法启动服务，syslog如下：

``` bash
Jul 23 21:45:06 tyler-QTJ5 NetworkManager[2597]: <error> [1469281506.9518] platform-linux: do-change-link[9]: failure changing link: failure 19 (没有那个设备)
Jul 23 21:45:06 tyler-QTJ5 NetworkManager[2597]: <warn>  [1469281506.9518] device (ppp0): failed to disable userspace IPv6LL address handling
Jul 23 21:45:06 tyler-QTJ5 gnome-session[3306]: Gjs-Message: JS LOG: Removing a network device that was not added
Jul 23 21:45:06 tyler-QTJ5 NetworkManager[2597]: <info>  [1469281506.9549] devices removed (path: /sys/devices/virtual/net/ppp0, iface: ppp0)
Jul 23 21:45:06 tyler-QTJ5 NetworkManager[2597]: ** Message: nm-pptp-ppp-plugin: (nm_phasechange): status 1 / phase 'dead'
Jul 23 21:45:06 tyler-QTJ5 NetworkManager[2597]: ** Message: nm-pptp-ppp-plugin: (nm_exit_notify): cleaning up
Jul 23 21:45:06 tyler-QTJ5 pptp[5371]: nm-pptp-service-5359 warn[decaps_hdlc:pptp_gre.c:220]: short read (-1): Input/output error
Jul 23 21:45:06 tyler-QTJ5 pptp[5371]: nm-pptp-service-5359 warn[decaps_hdlc:pptp_gre.c:232]: pppd may have shutdown, see pppd log
Jul 23 21:45:06 tyler-QTJ5 pptp[5386]: nm-pptp-service-5359 log[callmgr_main:pptp_callmgr.c:245]: Closing connection (unhandled)
Jul 23 21:45:06 tyler-QTJ5 pptp[5386]: nm-pptp-service-5359 log[ctrlp_rep:pptp_ctrl.c:259]: Sent control packet type is 12 'Call-Clear-Request'
Jul 23 21:45:06 tyler-QTJ5 pppd[5366]: Exit.
Jul 23 21:45:06 tyler-QTJ5 pptp[5386]: nm-pptp-service-5359 log[call_callback:pptp_callmgr.c:84]: Closing connection (call state)
```

> 和自己的网络状况以及VPN的设置有关

--------------

### VPN选择

1. **Lantern**: <https://github.com/getlantern/forum/issues/833>  **每月800M免费流量，WIN10旧版本无限流量**
1. **天行VPN**

--------------

### 科学上网-google镜像

1. **快搜**: <http://so.chongbuluo.com/>   // 比较推荐
1. **谷歌镜像大全**: <http://google.adwiki.cn/> 

-------------

`以下为转载：`

> 
 原版页面：

https://go.coolnull.com/ （无广告，搜索体验良好）

http://google.suanfazu.com/ （整合搜索，非原版，相对稳定）

https://g.jikewenku.cn/ （无广告，搜索体验良好）

https://g.chenjx.cn/ （无广告，体验良好

https://g.namaho.com/ （无广告，搜索体验良好）

https://g.starmoe.xyz/ （无广告，搜索体验良好）

https://g.linuxeye.com （无广告，搜索体验良好）

https://g.inspire-energy.com.cn/ （无广告，搜索体验良好）

https://www.ytso.cc/ （无广告，搜索体验良好）

https://soguge.ga （无广告，搜索体验良好）

https://ggg.eeload.com/ （无广告，体验良好）

https://letsgg.tk/ （无广告，体验良好）

https://google.kfd.me/ （先访问主站，然后点击google）

https://www.guge.xxx/ （无广告，体验良好）

http://ljy2345.esy.es/ （无广告，搜索体验良好）

http://gg.aiorai.com/ （无广告，搜索体验良好）

http://ggso.gq/ （无广告，搜索体验良好）

http://gg.qwghq.com/ （无广告，体验良好）

http://bc60042.byethost13.com/ （无广告，体验良好）

http://gc.ihuan.me/  （无广告，体验良好）

http://www.nginxgoogle.com/ （无广告，体验良好）

https://julianhuang.cc/ （无广告，体验良好）

http://baidusb.tk/ （无广告，体验良好）

http://45.78.38.197/ （无广告，体验良好）

https://ggso.co/ （无广告，体验良好）

http://glgoo.jsylhs.com/ （有广告，体验一般）

https://g.hancel.net/ （无广告，体验良好）

https://g.v2fr.ml （无广告，搜索体验良好）

http://scholar.hntvchina.com/ （无广告，体验良好）

http://lujinjie.win/ （无广告，体验良好）

http://so.biochen.com/ （无广告，体验良好）

http://gg.huwaige.com/ （有弹窗广告，体验不是很好）

http://guge.ro.lt/ （有弹窗广告，体验不是很好）

http://free.8090st.com/ （有弹窗广告，体验不是很好）

https://fq.yanke.info （无广告，搜索体验良好）

https://g.glv.io/ （无广告，搜索体验良好）

http://www.jumper.site/ （无广告，体验良好）

http://3.zzq2.com/ （无广告，体验良好）

https://ggso.me/ （无广告，体验良好）

http://fuckbaidu.bid  （已被污染，建议访问对应ip地址：http://198.12.101.102/）

http://forkgil.com/ （无广告，体验良好）

https://aigg.info （无广告，体验良好）

https://g.wxbit.com/ （无广告，搜索体验良好）

http://g.fity.cn/ （无广告，体验良好）

https://ykooh.com/ （无广告，搜索体验良好）

https://www.onk6.com （无广告，体验良好）
> 谷歌学术

https://xueshu.glgoo.org/ 

http://gg.qwghq.com/scholar/

http://xueshu.51google.pw/

https://ggso.co/scholar

https://www.scholar.live/ 

https://g.glv.io/extdomains/scholar.google.com/
> 变种网页：

http://www.gfsousou.cn/ （谷粉搜搜）

http://www.51google.pw/ （无广告，搜索体验良好）

https://www.dcrss.com/ （google学术和网页）

http://www.ibying.com/ （无广告，搜索体验良好）

http://www.ssdao.com/ （无广告，搜索体验良好）

https://gfsoso.8090st.com/ （无广告，搜索体验良好）

http://lige.aclstack.com/ （无广告，体验良好）

http://g.sjswdh.com/ （无广告，体验良好）

http://search.twcc.com/  （无广告，体验良好）

http://wangye.wallpai.com/ （无广告，体验良好）

https://www.tlss.space/ （无广告，体验良好）

http://google.aiorai.com/ （有广告，体验一般）

http://mob.wallpai.com/ （有广告，体验还行）

http://www.gfss.cc/ （有广告，体验还行）

https://www.googto.com/ （https://www.googto.net）

http://fcczp3.huwaige.com/ （有广告，体验一般）

http://www.gfsswy.com/ （无广告，体验良好）

http://www.gycc.com/ （无广告，体验良好）

http://fcczp6.huwaige.com/ （有广告，体验一般） 

