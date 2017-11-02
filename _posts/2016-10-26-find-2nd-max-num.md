---
title: 无序数组中找到第二大的数
categories:
  - Codes
tags:
  - 算法
permalink: find-2nd-max-num
date: 2016-10-26 20:26:38
---

<h2 id="intro">前言</h2>这是一道简单的面试题，要求时间复杂度为O(n)，很顺利的想到用两个值存储，以空间换时间，进阶可以使用快速排序的分治思想取巧。


<!-- more -->

---------------

### 写在前面

首先，引用一段话：

> 好了，让我们来看一个示例（这个示例是昨天在微博上的一个讨论），这个题是——“找出无序数组中第2大的数”，几乎所有的人都用了O(n)的算法，我相信对于我们这些应试教育出来的人来说，不用排序用O(n)算法是很正常的事，连我都不由自主地认为O(n)算法是这个题的标准答案。我们太习惯于标准答案了，这是我国教育最悲哀的地方。（广义的洗脑就是让你的意识依赖于某个标准答案，然后通过给你标准答案让你不会思考而控制你）

题为[为什么我反对纯算法面试题](http://coolshell.cn/articles/8138.html/)，作者是陈皓，写于2012年。

这个“学院派”和“工程师”的争论的问题在下面的评论区里已经展示的淋漓尽致，同样一个题还会出现在2016年秋季校招上，为什么我一点也不奇怪呢。

不谈支持与否，作者关于工程的观点还是十分精辟的：

> 根据上面的需求分析，有软件工程经验的人的解法通常会这样：

>   1）把数组排序，从大到小。

>   2）于是你要第k大的数，就直接访问 array[k]。

>   排序只需要一次，O(n*log(n))，然后，接下来的m次对FindKthMaxNum()的调用全是O(1)的，整体复杂度反而成了线性的。

>   其实，上述的还不是工程式的最好的解法，因为，在业务中，那数组中的数据可能会是会变化的，所以，如果是用数组排序的话，有数据的改动会让我重新排序，这个太耗性能了，如果实际情况中会有很多的插入或删除操作，那么可以考虑使用B+树。

可见，实际工程中不止要考虑算法的时间复杂度这么简单，对于`在无序数组中取出第i个值`这个问题，工程中使用先排序后取值是最便捷和常用的方式。

使用如下的方式虽然复杂度为O(n)，但在大数m次访问的时候，复杂度将变成`o(n*m)`，而排序后取值的访问时复杂度为`O(1)`。

---

### C语言代码

**最简单的用两个值存储**：

``` C
int findthesecond(int *arr,int size)
{
    int max=0; // max
    int second=0; // second
    int i=0;
    for(i=1;i<size;i++){
        if(arr[i]>max){
            second=max;
            max=arr[i];
        }
        else if(arr[i]<max&&arr[i]>second)
            second=arr[i];
    }
    return second;
}
```

**使用快速排序的思想**

``` C
#include <stdio.h>

// from the QuickSort
int AdjustArray(int *arr,int left, int right) {
	int i = left,j=right;
	int x = arr[left];
	while (i<j) {
		while (i<j&&arr[j]>=x)
			j--;
		if (i<j) 
			arr[i++]=arr[j];
		while (i<j&&arr[i]<x)
			i++;
		if (i<j)
			arr[j--]=arr[i];
	}
	arr[i]=x;
	return i;// 返回基准数位置
}

// find the n'nd min number
int FindnMinNum(int *arr,int left,int right,int n){
	int index = AdjustArray(arr,left,right); // the index to divide
	if (index-left+1 == n)
		return arr[index];
	if (index-left+1 > n)
		return FindnMinNum(arr,left,index-1,n);
	else
		return FindnMinNum(arr,index+1,right,n-(index-left+1));
}

// find the n'nd max number
int FindnMaxNum(int *arr,int left,int right,int n){
	int index = AdjustArray(arr,left,right);
	if (right-index+1 == n)
		return arr[index];
	if (right-index+1 < n)
		return FindnMaxNum(arr,index+1,right,n);
	else
		return FindnMaxNum(arr,left,index-1,n-(right-index+1));
}

```


---

### 延伸

各种排序的复杂度图表：

![各种排序复杂度](http://o7myibfc7.bkt.clouddn.com/21457204_1326898064RUxx.jpg?imageView2/2/h/400/)
