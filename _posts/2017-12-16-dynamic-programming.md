---
title: 动态规划的三个例子
categories:
  - Codes
tags:
  - 算法
  - Java
permalink: dynamic-programming
date: 2017-12-16 20:28:52
---

<h2 id="intro">前言</h2>走楼梯和挖金矿两个例子，解析动态规划的由来和算法结构。然后是剑指offer的剪绳子问题。------其实就是斐波那契和一个2^n-1，用python两行就写完了，但是违背了练习算法的初衷，尽量都用java完成。

<!-- more -->

---------------


## 走楼梯

**题目**：

> 有一座高度是10级台阶的楼梯，从下往上走，每跨一步只能向上1级或者2级台阶。要求用程序来求出一共有多少种走法。

### 贪婪

排列组合，枚举.   时间复杂度:指数级。

### 倒推分析

先看动态规划的三个概念：

- 最优子结构
- 边界
- 转移公式

最优子结构为F(9)+F(8)=F(10)，边界为F(1) F(2)，转移公式为：`F(n)=F(n-1)+F(n-2)`。

### 递归法

时间复杂度： 类似二叉树  `O(2^n-1)`

``` java
int getClimbingWays(int n){
	if(n<1)
		return 0;
	else if(n==1)
		return 1;
	else if(n==2)
		return 2;
	return getClimbingWays(n-1)+getClimbingWays(n-2);
}	
```



### 备忘录算法

一个hashmap储存计算过的，时间复杂度O(n)，空间复杂度O(n)。

``` java
int getClimbingWays(int n, HashMap<Integer,Integer> map){
	if(n<1)
		return 0;
	else if(n==1)
		return 1;
	else if(n==2)
		return 2;
	if(map.contains(n)
		return map.get(n);
	else{
		int value = getClimbingWays(n-1) + getClimbingWays(n-2);
		map.put(n,value);
		return value;
	}
}
```

### 动态规划

保留前两个状态，迭代：

时间复杂度：O(n)  空间复杂度: O(1)

``` java
int getClimbingWays(int n){
	if(n<1)
		return 0;
	else if(n==1)
		return 1;
	else if(n==2)
		return 2;
		
	int a=1;
	int b=2;
	int temp=0;
	
	for(int i=3;i<=n;i++){
		temp = a+b;
		a=b;
		b=temp;
	}
	return temp;
}
```
---


## 挖金矿问题


> 有一个国家发现了5座金矿，每座金矿的黄金储量不同，需要参与挖掘的工人数也不同。参与挖矿工人的总数是10人。每座金矿要么全挖，要么不挖，不能派出一半人挖取一半金矿。要求用程序求解出，要想得到尽可能多的黄金，应该选择挖取哪几座金矿？


### 暴力枚举

pass

---

### 三要素分析

![金矿分析1](http://o7myibfc7.bkt.clouddn.com/%E9%87%91%E7%9F%BF1.jpg)


![金矿分析2](http://o7myibfc7.bkt.clouddn.com/%E9%87%91%E7%9F%BF2.jpg)

- **最优子结构**：挑出一个金矿，挖与不挖，两种子结构
- **边界**：只剩一个金矿，挖与不挖
- **转移公式**： n金矿数   w工人数 g[]金矿的产量  p[]需要的人数 

```
F(n,w) = 0    (n<=1, w<p[0]);
F(n,w) = g[0]     (n==1, w>=p[0]);
F(n,w) = F(n-1,w)    (n>1, w<p[n-1])  
F(n,w) = max(F(n-1,w),  F(n-1,w-p[n-1])+g[n-1])    (n>1, w>=p[n-1])
```

### 动态规划

``` java
int getMostGold(int n, int w, int[] g,int[] p){
	int[] preRusults = new int[p.length];
	int[] results = new int[p.length];
	
	// loop for n of workers
	for(int i=1;i<=w;i++){
		if(i<p[0])
			preRusults[i]=0;
		else
			preRusults[i]=g[0];
	}
	
	// loop for n of mines
	for(int i=0;i<n;i++){
		// loop for n of workers
		for(int j=1;j<=w;j++){
			if(j<p[i])
				results[j]=preRusults[j];
			else
				results[j]=Math.max(preRusults[j],preRusults[j-p[i]]+g[i]);
		}
	preRusults=results;
	}
	return results[w];
}

```


**注意**: 时间复杂度O(n*m),空间复杂度o(m)。  而递归法的时间复杂度O(2^n)，n为二叉树的高度，`当w特别大时，递归的时间复杂度要小于动态规划`。
	
---


## 剪绳子（剑指offer）

给你一根长度为n的绳子，请把绳子剪成m段 (m和n都是整数，n>1并且m>1)每段绳子的长度记为k[0],k[1],...,k[m].请问k[0]*k[1]*...*k[m]可能的最大乘积是多少？

例如，当绳子的长度为8时，我们把它剪成长度分别为2,3,3的三段，此时得到的最大乘积是18.

``` java
public static int maxAfterCutting(int length){
	if(length<2)
		return 0;
	else if(length==2)
		return 1;
	else if(length==3)
		return 2;
		
	int[] f = new int[length+1];
	f[0]=0;  // 初始化动态规划的初始值
	f[1]=1;
	f[2]=2;
	f[3]=3;
	int result =0;
	for(int i=4;i<=length;i++){
	int max=0;
		for(int j=1;j<i/2;j++){
			int num = f[j]*f[i-j];
			if(max<num)
				max = num;
			f[i]=max;
		}
	}
	result=f[length];
	return result;
}
```