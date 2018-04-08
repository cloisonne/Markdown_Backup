---
title: 记录蜂窝网络链路聚合项目的零碎
categories:
  - Notes
tags:
  - LTE
  - GSM
  - VPN
  - IPsec
permalink: SIM-multi-aggregation-project
abbrlink: 50343
date: 2016-10-11 22:22:34
---

<h2 id="intro">前言</h2>项目是要保密的，所以只是记录一些自己项目过程里的零零碎碎。

<!-- more -->

---------------

### 对开源IPsec VPN的研究

#### StrongSwan vs OpenSwan vs Libreswan

Libreswan是OpenSwan开发者因为商标问题新开发的开源VPN工具。

- StrongSwan has much more comprehensive and developed documentation than Libreswan.
- StrongSwan has support for EAP authentication methods, which make it easier to integrate into heterogeneous environments (such as authenticating to Active Directory). These are less well developed or even missing from Libreswan.
- StrongSwan can be clustered and load balanced. Libreswan does not seem to have any support to do either.
- Libreswan supports more hardware crypto accelerators than StrongSwan, but requires kernel patches to do so.


#### OpenSwan roadwarrior and net2net

```
config setup
    dumpdir=/var/run/pluto/
    nat_traversal=yes
    virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12,%v6:fd00::/8,%v6:fe80::/10
    protostack=netkey
    
conn L2TP-PSK-NAT
    rightsubnet=vhost:%priv
    also=L2TP-PSK-noNAT

conn L2TP-PSK-noNAT
    authby=secret
    pfs=no
    auto=add
    keyingtries=3
    ikelifetime=8h
    keylife=1h
    type=transport
    left=%SERVERIP%
    leftprotoport=17/1701
    right=%any
    rightprotoport=17/%any
```

```
conn net-to-net 
    left=192.168.1.10              # LServer外网IP地址 
    leftsubnet=10.10.10.0/24       # LServer内网IP段
    leftrsasigkey=0sAQOBIJFmj…   # LServer的公钥 
    leftnexthop=%defaultroute      # LServer的下一跳指定为默认路由地址 
    right=192.168.1.20             # RServer外网IP地址 
    rightsubnet=100.10.20.0/24      # RServer内网IP段
    rightrsasigkey=0sAQNZZZjj…   # Rserver的公钥 
    rightnexthop=%defaultroute     # RServer的下一跳指定为默认路由地址 
    auto=start                     # 添加这个链接，启动时自动连接
```

#### StrongSwan的HA模式

The roadwarriors carol and dave set up a connection each to the virtual gateway mars implemented by the two real gateways alice and moon in a High Availability (HA) setup based on ClusterIP. Depending on the hash of the IP addresses of the peers and the SPIs, the inbound and outbound CHILD_SAs are either assigned to segment 1 managed by alice or segment 2 handled by moon. The IKEv2 protocol is managed by moon exclusively with passive IKE_SAs installed on the backup gateway alice. 

![StrongSwan的HA模式](http://o7myibfc7.bkt.clouddn.com/strongswan.png)


#### VPN链路聚合的两种方案

- 主要实现还是利用了`Netfilter HOOK`

![《支持多路负载平衡的IPsec VPN 系统的设计与实现》](http://o7myibfc7.bkt.clouddn.com/vpn1.png)

![《高可用IPsec虚拟专用网研究》](http://o7myibfc7.bkt.clouddn.com/vpn2.png)


-----
### 硬件问题

- 常见天线接头类型:

![常见天线接头类型](http://o7myibfc7.bkt.clouddn.com/TB29215.jpg?imageView2/3/w/300/h/150/q/90)

---------------
### SIM7100拨号脚本

#### 代码
详见gayhub：

<div class="github-widget" data-repo="cloisonne/SIM7100-dial-script"></div>

#### 上网设置APN,拨号号码，帐号和密码

|运营商（ISP）|APN|拨号号码|帐号|密码|
|----|----|----|----|----|
|中国联通WCDMA（China Unicom）|3GNET|*99#|空|空|
|中国电信CDMA2000（China Telecom）EVDO网络|空|#777|ctnet@mycdma.cn|vnet.mobi|
|1X网络|空|#777|card (CARD)|card（CARD）|
|中国移动 TD-SCDMA（China Mobile）|CMNET|*98*1#|空|空|
|中国移动 GPRS（China Mobile）|CMNET|*99***1#|空|空|
---------------

### OpenSwan源码解析

后面没有使用开源VPN，这里没有继续接触。

