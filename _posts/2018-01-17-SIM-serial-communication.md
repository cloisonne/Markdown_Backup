---
title: SIM模块的串口通信例程
categories:
  - Codes
tags:
  - LTE
  - Linux
  - C
  - GSM
permalink: SIM-serial-communication
abbrlink: 42320
date: 2018-01-17 17:19:06
---

<h2 id="intro">前言</h2>项目里写的SIM模块串口通信例程，模块使用SIM7100，支持2G/3G/4G，交互使用串口中的AT命令实现，采用非阻塞，提高交互效率。


<!-- more -->

---------------

### 代码

详见gayhub：
<div class="github-widget" data-repo="cloisonne/SIM7100-Serial-Communication"></div>


---


### 函数分析
得到的数据使用结构体保存，方便丢给主程序。
实现的功能函数如下，主要包括：

1. 串口的设置和打开

1. 串口的发送和接受函数

1. 获取模块的信号质量

1. 获取模块的当前网络制式

1. 获取模块的运营商

1. 选择模块的网络模式

1. 对模块的离线控制

``` c
typedef struct{
	int csq;   // Signal Quality
	int ber;   // Bit Error Rate
	char provider_name[30]; // Provider Name
	char sim_mode[30];
}StateInfo;

int open_port(int port);
int set_opt(int fd, int nSpeed, int nBits, char nEvent, int nStop);
static int serial_send(int file_descriptor, char *buffer, size_t data_len);

int get_operator(int fd,StateInfo *stateinfo);
int get_csq(int fd,StateInfo *stateinfo);
int get_mod(int fd,StateInfo *stateinfo);
int mode_selection(int fd,int mode);
int offline_control(int fd,int isornot);

static int csq_detected(int csq);
static int mod_detected(int mod,StateInfo *stateinfo);
```



---


### 获取信号质量为例

主要是通过发送AT命令（非阻塞），并处理接受的数据，来和模块交互。
``` c
int get_csq(int fd,StateInfo *stateinfo)
{
	int nread;
	char buff[SIZE];

	char sbuffer[]="AT+CSQ\r";
	serial_send(fd,sbuffer,sizeof(sbuffer));

	int flag = 0;

	while(!flag)
	{
		while((nread=read(fd,buff,SIZE))>0)
		{
			//printf("\nLen %d\n",nread);
			buff[nread]='\0';
			//printf("\n%s",buff);

			if(strstr(buff,"CSQ")!=NULL)
			{
				stateinfo->ber=0;
				stateinfo->csq=0;
				int i = 0;

				while(buff[i]!=',')
				{
					if(buff[i]>='0'&&buff[i]<='9')
					{
						stateinfo->csq=(stateinfo->csq)*10+(buff[i]-'0');
					}
					i++;
				}
				while(buff[i]!='\0')
				{
					if(buff[i]>='0'&&buff[i]<='9')
					{
						stateinfo->ber=(stateinfo->ber)*10+(buff[i]-'0');
					}
					i++;
				}
				flag = 1;
				stateinfo->csq=csq_detected(stateinfo->csq);
			}
		}
		//usleep(5000);
	}
	printf("\n%d + %d\n",stateinfo->csq,stateinfo->ber);
	printf("\n--get csq&ber end--\n");
	return 0;
}
```

---
