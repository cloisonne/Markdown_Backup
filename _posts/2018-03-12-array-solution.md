---
title: leetcode数组思路总结
categories:
  - Codes
tags:
  - Python
  - Leetcode
permalink: array-solution
abbrlink: 47967
date: 2018-03-12 16:46:29
---

<h2 id="intro">前言</h2>暂时没什么前言。


<!-- more -->

---------------

## N-sum类型题解

### 2-sum

两种解法：
- 排序，双指针首尾搜索，逐渐逼近
- 固定一个数，寻找(target-n)，使用了hashtable方法

``` py
def twoSum(self,nums,target):
	lookup = []
	for i,num in enumerate(nums):
		if target-num in lookup:
			return [lookup[target-num], i]
		lookup[num] = i
```

---

## 

### 

---

## 

### 

---
