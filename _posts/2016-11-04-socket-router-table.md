---
title: 利用socket实现路由表和二层帧转发
categories:
  - Codes
tags:
  - Linux
  - Socket
permalink: socket-router-table
abbrlink: 33500
date: 2016-11-04 22:29:08
---

<h2 id="intro">前言</h2>最近研究LInux下的socket编程，这里做一个基础总结和对两份代码的分析。一个是路由表转发，一个是二层帧转发。


<!-- more -->

---------------

[太长不看，直接看代码的传送门](/posts/33500/#路由表MAC转发代码分析)

### 一些基础知识



![封装过程](http://o7myibfc7.bkt.clouddn.com/0_1301456076yzy7.gif.jpeg?imageView2/2/h/400/)

常用的头结构，这里没包括ICMP头。


**MAC头结构**

``` C
/*位于/usr/include/net/ethernet.h
数据帧定义，头14个字节，尾4个字节*/
typedef struct _MAC_FRAME_HEADER
{
 char m_cDstMacAddress[6];    //目的mac地址
 char m_cSrcMacAddress[6];    //源mac地址
 short m_cType;       　　　　　//上一层协议类型，如0x0800代表上一层是IP协议，0x0806为arp
}__attribute__((packed))MAC_FRAME_HEADER,*PMAC_FRAME_HEADER;


typedef struct _MAC_FRAME_TAIL
{
 unsigned int m_sCheckSum;    //数据帧尾校验和
}__attribute__((packed))MAC_FRAME_TAIL, *PMAC_FRAME_TAIL;
```

**IP头结构**
```C
// 自定义 /netinet/ip.h
typedef struct _IP_HEADER 
{
 char m_cVersionAndHeaderLen;     　　//版本信息(前4位)，头长度(后4位)
 char m_cTypeOfService;      　　　　　 // 服务类型8位
 short m_sTotalLenOfPacket;    　　　　//数据包长度
 short m_sPacketID;      　　　　　　　 //数据包标识
 short m_sSliceinfo;      　　　　　　　  //分片使用
 char m_cTTL;        　　　　　　　　　　//存活时间
 char m_cTypeOfProtocol;    　　　　　 //协议类型
 short m_sCheckSum;      　　　　　　 //校验和
 unsigned int m_uiSourIp;     　　　　　//源ip
 unsigned int m_uiDestIp;     　　　　　//目的ip
} __attribute__((packed))IP_HEADER, *PIP_HEADER ;
```

![IP头结构](http://o7myibfc7.bkt.clouddn.com/IPheader.jpg)



**tcp头结构**

``` C
typedef struct _TCP_HEADER 
{
 short m_sSourPort;        　　　　　　// 源端口号16bit
 short m_sDestPort;       　　　　　　 // 目的端口号16bit
 unsigned int m_uiSequNum;       　　// 序列号32bit
 unsigned int m_uiAcknowledgeNum;  // 确认号32bit
 short m_sHeaderLenAndFlag;      　　// 前4位：TCP头长度；中6位：保留；后6位：标志位
 short m_sWindowSize;       　　　　　// 窗口大小16bit
 short m_sCheckSum;        　　　　　 // 检验和16bit
 short m_surgentPointer;      　　　　 // 紧急数据偏移量16bit
}__attribute__((packed))TCP_HEADER, *PTCP_HEADER;


typedef struct _TCP_OPTIONS
{
 char m_ckind;
 char m_cLength;
 char m_cContext[32];
}__attribute__((packed))TCP_OPTIONS, *PTCP_OPTIONS;
```

![TCP头](http://o7myibfc7.bkt.clouddn.com/TCPheader.jpg)

**UDP头结构**

``` C
typedef struct _UDP_HEADER 
{
 unsigned short m_usSourPort;    　　　// 源端口号16bit
 unsigned short m_usDestPort;    　　　// 目的端口号16bit
 unsigned short m_usLength;    　　　　// 数据包长度16bit
 unsigned short m_usCheckSum;    　　// 校验和16bit
}__attribute__((packed))UDP_HEADER, *PUDP_HEADER;
```

![UDP头](http://o7myibfc7.bkt.clouddn.com/UDPheader.jpg)

**原始套接字编程**

原始套接字编程主要是涉及到packet，这里给出packet的man文件：

[PACKET](http://man7.org/linux/man-pages/man7/packet.7.html)

---

### 路由表MAC转发代码分析

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <linux/if_ether.h>
#include <sys/time.h>
#include <netpacket/packet.h>
#include <net/ethernet.h>
#include "define.h"
#include <errno.h>
#include <net/if.h>
#include <ctype.h>
#include <arpa/inet.h>
//#include <arpa/inet.h>
//#include <linux/in.h>

//extern int errno;
#ifndef	errno
extern int errno;
#endif

int sockfd;
char recvbuf[2048];
int R_table_id =-1;
int R_table_sum=0;
struct RTable mytable[10];

void ReadFile(); // 读取配置文件
int FindMe(); // 查找IP对应的MAC地址
void sends(); // 发送给MAC地址
int getvol(char g); // 返回ASCII码

int main(int argc, char **argv) {
	ReadFile();

	if((sockfd=socket(PF_PACKET,SOCK_RAW,htons(ETH_P_IP)))<0){
		printf("Can Not Create Raw Socket\n");
		return -1;
	}

	while(1){
		int n_read=recvfrom(sockfd,recvbuf,2048,0,NULL,NULL);
		if(n_read<42){
			printf("Error When Receive Message\n");
			return -1;
		}
		char*p =recvbuf;
		int n=0xff;
		printf("MAC address:	%.2x:%02x:%02x:%02x:%02x:%02x	====>	%.2x:%02x:%02x:%02x:%02x:%02x\n",p[6]&n,p[7]&n,p[8]&n,p[9]&n,p[10]&n,p[11]&n,p[0]&n,p[1]&n,p[2]&n,p[3]&n,p[4]&n,p[5]&n);
		p=recvbuf+26;
		printf("IP:	%d.%d.%d.%d	  ===>	%d.%d.%d.%d\n",p[0]&n,p[1]&n,p[2]&n,p[3]&n,p[4]&n,p[5]&n,p[6]&n,p[7]&n);
		R_table_id = FindMe(p+4);
		if(R_table_id == -1)
			printf("Not Find In Route\n");
		else{
			printf("Find In Route %s\n",mytable[R_table_id].eth);
			sends();
		}
	}
	return 0;
}

void ReadFile(){ // 读取本地配置文件
	FILE *fp = fopen("conf.file","r");
	if(fp == NULL){
		printf("ERROR WHNE READING!\n");
		return;
	}
	char *p;
	int k=0,i=0,Rt=0;
	int n=0xff;
	char temp[2048];
	unsigned int t[4];
	printf("NOW READING!\n");
	while(!feof(fp)){
	fscanf(fp,"%s %s %d.%d.%d.%d\n",mytable[Rt].eth,temp,&t[0],&t[1],&t[2],&t[3]);
	for(i=0,k=0;i<17;i+=3)
   		mytable[Rt].mac[k++] = (unsigned char)(16*getvol(temp[i]) + getvol(temp[i+1]));
	for(i=0;i<4;i++)
		memcpy(mytable[Rt].ip+i,&t[i],1);
	p = mytable[Rt].mac;
	printf("%d DST %s MAC: %.2x:%02x:%02x:%02x:%02x:%02x ",R_table_sum,mytable[Rt].eth,p[0]&n,p[1]&n,p[2]&n,p[3]&n,p[4]&n,p[5]&n);
	p = mytable[Rt].ip;
	printf("IP: %d.%d.%d.%d\n",p[0]&n,p[1]&n,p[2]&n,p[3]&n);
	R_table_sum++;
	Rt++;
	}
	printf("%d READING END!\n",R_table_sum);
}

int FindMe(char *mp){//get the index in router table
   int i=0;
   for(i=0;i<R_table_sum;i++)
   {
       if(memcmp(mp,mytable[i].ip,3) == 0)
       	return i;
   }
   return -1;
}

void sends()
{
	//int i=0,n=0xff,
	int len=0;
	struct sockaddr_ll sa;
	memset(&sa,0,sizeof(struct sockaddr_ll));
	sa.sll_family = AF_PACKET;
	sa.sll_halen = 6;
	sa.sll_ifindex = if_nametoindex(mytable[R_table_id].eth);
	sa.sll_protocol = 0x0800;
	memcpy(&sa.sll_addr,mytable[R_table_id].mac,6);
	memcpy(recvbuf,mytable[R_table_id].mac,6);
	struct iphead *iph = (struct iphead *)(recvbuf+14); // 去掉MAC头 得到IP头
	len = 16*iph->ip_len[0] + iph->ip_len[1]; // 数据报长度
	int retval = sendto(sockfd, recvbuf, 14+len, 0, (struct sockaddr*)&sa, sizeof(struct sockaddr_ll));
	if(retval == -1){
		perror("Sends Error");
	}
}

int getvol(char g)
{
	if(isalpha(g))
		return (10+g-'a');
	else
		return (g-'0');
}

```

这里的IP头结构为自定义：

```c
struct iphead{
unsigned char ip_hl:4,ip_v:4;
unsigned char ip_tos;
unsigned char ip_len[2];
unsigned short int ip_id;
unsigned short int ip_off;
unsigned char ip_ttl;
unsigned char ip_p;
unsigned short int ip_sum;
unsigned int ip_src;
unsigned int ip_dst;
};
```

程序接收完整的一个socket packet，进行包头的分解，然后根据配置文件的IP和MAC对应关系进行转发。

---

### socket二层帧转发代码分析

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/socket.h>
#include <linux/if_packet.h>
#include <linux/if_ether.h>
#include <sys/ioctl.h>	//for netdevice
#include <linux/if_arp.h>

#define _GNU_SOURCE 
#include <stddef.h>
#include <sys/types.h>
#include <dirent.h>
#include <string.h>

#include <signal.h>
#include <sys/time.h>
#include <sys/select.h>	//for select()

#define max(a,b) ((a)>(b)?(a):(b)) 

#define ETH_FRAME_LEN  500
#define NICNUM 2	//要接收的网卡数量为2，默认为eth0和eth1
#define RECVDEBUG
#define SENDDEBUG

typedef struct EthernetInfo {
	int dev;		//是否存在？
	int active;	//是否被激活；
	unsigned char mac_local[8];	//本地网卡的MAC地址
	unsigned char mac_next[8];	//下一跳的MAC地址
	} EthernetInfo;

struct ipheader {
 unsigned char ip_hl:4, ip_v:4; /* this means that each member is 4 bits */
 unsigned char ip_tos;
 unsigned short int ip_len;
 unsigned short int ip_id;
 unsigned short int ip_off;
 unsigned char ip_ttl;
 unsigned char ip_p;
 unsigned short int ip_sum;
 unsigned int ip_src;
 unsigned int ip_dst;
}; /* total ip header length: 20 bytes (=160 bits) */


struct itimerval monitor_interval; 		//系统定时器连续时间间隔

void monitor_timer();
int lookupethdev(EthernetInfo *eif);
 
int main(int argc, char *argv[]){
	int i, m, n, cnt;
	struct sockaddr_ll sockadr[2], adrfrom; //数据链路层使用的网络参数结构体
	struct ifreq ifst[2];
	int sock[2];	//两个网卡上接收，这几个参数也得增加
	char *devname[] = {"eth0","eth1"};
	EthernetInfo ethinfo[NICNUM];//存放本地网卡硬件地址

	unsigned char  *buffer;
	unsigned char *data;
	struct ipheader *iphdr;

	fd_set rset; //for select()
	int maxfd;

	lookupethdev(&ethinfo);
	m = 0;
	printf("In Main ---\n Eth%d's MAC  %x:%x:%x:%x:%x:%x\n", m, ethinfo[m].mac_local[0], ethinfo[m].mac_local[1], ethinfo[m].mac_local[2], ethinfo[m].mac_local[3], ethinfo[m].mac_local[4], ethinfo[m].mac_local[5]);
	buffer = (unsigned char*)malloc(ETH_FRAME_LEN); //mem for buffer
	data = buffer + 14; //data in the frame

	for(n=0; n<NICNUM; n++){ // 两个网卡分别socket和bind
		if ((sock[n]=socket(AF_PACKET,SOCK_RAW,htons(ETH_P_ALL)))==-1){
			perror("When creat raw socket: ");
			exit(EXIT_FAILURE);
		}

		strcpy(ifst[n].ifr_name,devname[n]);
		ioctl(sock[n],SIOCGIFINDEX,&ifst[n]);   //根据名字获取网卡的index号码
		printf(" Eth%d--> ifst.ifr_ifindex = %d \n",n,ifst[n].ifr_ifindex);

		sockadr[n].sll_family   = AF_PACKET;
		sockadr[n].sll_protocol = htons(ETH_P_ALL);
		sockadr[n].sll_ifindex  = ifst[n].ifr_ifindex;

		// socket bind
		i = bind(sock[n], &(sockadr[n]), sizeof(struct sockaddr_ll));
		if(i != 0){
			perror("Binding False: ");
			exit(0);
		}
		else
			printf("Binding Sock[%d] successfully\n", n);
		}//--for--

		monitor_interval.it_interval.tv_sec = 0;
		monitor_interval.it_interval.tv_usec = 0;
		monitor_interval.it_value.tv_sec = 2;
		monitor_interval.it_value.tv_usec = 0; //定时器周期
		signal(SIGALRM,monitor_timer); //将定时器到达信号连接到monitor_timer子程序
		setitimer(ITIMER_REAL,&monitor_interval,NULL); //启动连续的定时器中断
	
		while(1){
			cnt++;
			printf("---------------cnt = %d------------\n", cnt);
			FD_ZERO(&rset);
			maxfd = 0;
			for(n=0; n<NICNUM; n++){
				FD_SET(sock[n], &rset);
				maxfd = max(sock[n], maxfd);
			}
			maxfd = maxfd + 1;

			if(select(maxfd, &rset, NULL, NULL, NULL)  < 1)
				continue;//没有期望的接收事件发生，继续等待

			for (n = 0; n <NICNUM; n++){
				if (FD_ISSET(sock[n], &rset) == 0)
					continue;	//判断哪个套接字上发生了事件，没有，继续判断下一个

				recvfrom(sock[n], buffer, ETH_FRAME_LEN, 0, (struct sockaddr*)&adrfrom, sizeof(struct sockaddr_ll));

				#ifdef RECVDEBUG
				printf("Eth%d-----ProtocAtFrom ＝ %d DestMac: ", n, adrfrom.sll_protocol);
				for(i=0; i<6; i++)
					printf("%2x-", buffer[i]);
				printf("   SourceMac: ", adrfrom.sll_protocol);
				for(i=6; i<12; i++)
					printf("%2x-", buffer[i]);
				printf("  FrameType = %2x-%2x\n",  buffer[12],  buffer[13]);
				if(buffer[12] != 0x08 || buffer[13] != 0x00)
					continue; //不是IP帧，不再往下执行，跳回接收
				iphdr =  (struct ipheader *)  (&buffer[14]);	//确定IP首部地址
				data = (unsigned char *)&(iphdr->ip_dst);
				printf("S_IP = %8x  %d.%d.%d.%d\n", iphdr->ip_dst, data[0], data[1], data[2], data[3]);
				//注意：网络字节顺序（int==0x0b00a8c0 －> 192.168.0.11）
				#endif RECVDEBUG

				//开始转发－－目的MAC暂且全1－－>数据链路层广播方式－－仅仅对于双网卡方式！
				m = (n + 1) % 1; // m=2？？？？？
				sockadr[m].sll_family   = AF_PACKET;
				sockadr[m].sll_protocol = htons(ETH_P_IP);
				sockadr[m].sll_ifindex  = ifst[m].ifr_ifindex;
				sockadr[m].sll_hatype   = ARPHRD_ETHER;
				sockadr[m].sll_pkttype  = PACKET_OTHERHOST;
				sockadr[m].sll_halen    = ETH_ALEN;
				memcpy(sockadr[m].sll_addr, ethinfo[m].mac_local, ETH_ALEN);
				memset(buffer, 0xff, ETH_ALEN); // Mac全1
				memcpy(buffer+ETH_ALEN,  ethinfo[m].mac_local, ETH_ALEN);
				buffer[12] = 0x08;
				buffer[13] = 0x00;

				#ifdef SENDDEBUG
				printf("Sendto  Eth%d-----ProtocAtFrom ＝ %d DestMac: ", n, adrfrom.sll_protocol);
				for(i=0; i<6; i++)
					printf("%2x-", buffer[i]);
				printf("   SourceMac: ", adrfrom.sll_protocol);
				for(i=6; i<12; i++)
					printf("%2x-", buffer[i]);
				printf("  FrameType = %2x-%2x IPSize = 0x%x\n",  buffer[12],  buffer[13], iphdr->ip_len);
				#endif SENDDEBUG

				i = sendto(sock[m],buffer,iphdr->ip_len + 14,0,(struct sockaddr*)&sockadr[m],sizeof(struct sockaddr_ll));
				if(i ==-1){
					perror("Error --> When sendto: ");
				}
				else
					printf("Sendto Success eth%d --> eth%d Size = %d\n", n, m, iphdr->ip_len);
			} //---for---end---
		}//--while--
}

// 查询系统配置文件以获得网卡MAC地址 不适合Ubuntu 用ioctl重写
int lookupethdev(EthernetInfo *eif){
	return 1;
}

//----------------------------------定时器------------------------------------------
void monitor_timer(){
	int i,j,k,l,m,n;
	static int timer_counter;
	monitor_interval.it_interval.tv_sec = 0;
	monitor_interval.it_interval.tv_usec = 0;
	monitor_interval.it_value.tv_sec = 2;
	monitor_interval.it_value.tv_usec = 0; //定时器周期:100ms
	setitimer(ITIMER_REAL,&monitor_interval,NULL); 
	timer_counter++;	
	return;
}
```

