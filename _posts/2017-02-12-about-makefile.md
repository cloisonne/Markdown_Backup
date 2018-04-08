---
title: 一个简单的makefile
categories:
  - Codes
tags:
  - Linux
  - Makefile
permalink: about-makefile
abbrlink: 30483
date: 2017-02-12 22:48:44
---

<h2 id="intro">前言</h2>学习了一下makefile的写法，写了一个比较简单的makefile。


<!-- more -->

---------------

学习参考<http://www.cnblogs.com/OpenShiFt/p/4313351.html/>

`makefile`

``` makefile
CC = gcc
CFLAGS +=
LDFLAGS +=
EXEC = main
OBJS = main.c function.c

all: $(EXEC)

.PHONY: all

main: $(OBJS)
	$(CC) $(CFLAGS) $(LDFLAGS) $^ -o $@

clean:
	-rm -f $(EXEC) *.elf *.gdb *.o *.a

.PHONY: clean
```

`function.c`

``` c
#include <stdio.h>

#define MAX 5

int array[MAX] = {2,7,6,4,8};

int sum(){

	int i;
	int n=0;

	for(i=0;i<MAX;i++)
		n+=array[i];
	return n;
}

int max(){
	int i=0;
	int max;

	max=array[i];
	for(i=0;i<MAX;i++){
		if(array[i] > max)
			max = array[i];
	}

	return max;
}
```

`include.h`

``` c
#include <stdio.h>

#define len 5

void print(int array[]){
	int i;
	
	for(i=0;i<len;i++)
		printf("array[%d] : %d\n",i,array[i]);
}
```

`main.c`

``` c
#include <stdio.h>
#include "include.h"
//#include "function.c"

extern int array[];
extern int sum();
extern int max();

int main(void){
	int sum_m,max_m;
	sum_m = sum();
	max_m = max();
	//int len = sizeof(array);
	print(array);
	printf("sum: %d , max: %d\n",sum_m,max_m);

	return 0;
}

```
