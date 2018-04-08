---
title: leetcode位运算思路总结
categories:
  - Codes
tags:
  - Leetcode
  - Python
permalink: bit-manipulation-solution
abbrlink: 24951
date: 2018-03-12 15:07:20
---

<h2 id="intro">前言</h2>leetcode位运算题解和总结，使用Python完成，未来选择性的加入lintcode题解。


<!-- more -->

---------------

Python3题解及刷题完成度详见gayhub：
<div class="github-widget" data-repo="cloisonne/LeetCode"></div>

---
### Single Number：

对数组中的元素一一异或，相同的数异或=0。

实现异或的两种方式：
``` py
def _xor(self,a,b):
	return a^b
# or
import operator
from functools import reduce

return reduce(operator.xor, A)
```

---

### Single Number Ⅱ

其他元素出现3次，使用统计方法或者位运算。
位运算解法和其他元素出现4次时的解法：

``` py
# one 出现一次  two 出现两次  three 出现三次
one, two = 0
one, two = (~x & one) | (x & ~one & ~two), (~x & two) | (x & one)
one, two, three = (~x & one) | (x & ~one & ~two & ~three), (~x & two) | (x & one), (~x & three) | (x & two)
```
使用counter计算：
``` py
import collections
return (collections.Counter(list(set(nums))*3) - collections.Counter(nums)).keys()[0]
```

---

### Single Number Ⅲ

两个元素出现一次，其他元素出现两次
全部异或，得到xor，获得bit：

	bit = xor & ~xor
	
bit是两个数第一个不相同的位

	result[bool(i&bit)] ^= i

其他数异或下来都是0 ， 这两个数异或下来的bool值一个是1 一个是0，成功分到了一个数组中。

---

### Reverse Bits

二进制翻转，原数右移一位，赋给result，result左移一位：

``` py
result = 0
for i in range(32):
	result <<= 1
	result |= n&1 # 取右边第一位
	n >>= 1
```

使用zfill（返回指定长度的字符串--右对齐）的一行解：

	return int(bin(n)[2:].zfill(32)[::-1], 2) # bin()开头是0b

Q:`如果该方法被大量调用，或者用于处理超大数据（Bulk data）时有什么优化方法？`

A:将数字分成8份，每份只能是0-15，记录0-15的翻转值，array或者map：
``` java
private int[] reverseHex = new int[] {0, 8, 4, 12, 2, 10, 6, 14, 1, 9, 5, 13, 3, 11, 7, 15};

// you need treat n as an unsigned value
public int reverseBits(int n) {
    int rev = 0;
    while (n != 0) {
        rev = (rev << 4) + reverseHex[n & 0xF];
        n >>>= 4;
    }
    return rev;
}
```
---

### Number of 1 Bits

求出二进制数中1的个数

``` py
def hammingWeight(self, n):
	result = 0
	while n:
		n &= n - 1
		result += 1
	return result
```

`n&(n-1)的妙用`:

	n与n-1的区别在于,对于n,从右向左数的第一个"1"开始一直到右,和n-1,完全相反
	n&(n-1)作用：将n的二进制表示中的最低位为1的改为0


---
 

### Bitwise AND of Numbers Range

---
