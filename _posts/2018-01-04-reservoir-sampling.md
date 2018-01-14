---
title: 蓄水池抽样证明以及例题
categories:
  - Codes
tags:
  - 算法
  - Java
permalink: reservoir-sampling
date: 2018-01-04 21:44:51
---

<h2 id="intro">前言</h2>蓄水池抽样在kmeans算法中有所应用，诸如Cloudera ML的分布式蓄水池抽样和加权分布式蓄水池抽样。


<!-- more -->

---------------

### 算法描述

1. 先选取数据流中的前k个元素，保存在集合A中；
1. 从第j（k + 1 <= j <= n）个元素开始，每次先以概率p = k/j选择是否让第j个元素留下。若j被选中，则从A中随机选择一个元素并用该元素j替换它；否则直接淘汰该元素；
1. 重复步骤2直到结束，最后集合A中剩下的就是保证随机抽取的k个元素。

---

### 两个leetcode例题

转载自[这里](https://www.jianshu.com/p/63f6cf19923d)

- Linked List Random Node

Given a singly linked list, return a random node's value from the linked list. Each node must have the same probability of being chosen.

Follow up:
What if the linked list is extremely large and its length is unknown to you? Could you solve this efficiently without using extra space?

Example:

``` java
// Init a singly linked list [1,2,3].
ListNode head = new ListNode(1);
head.next = new ListNode(2);
head.next.next = new ListNode(3);
Solution solution = new Solution(head);

// getRandom() should return either 1, 2, or 3 randomly. Each element should have equal probability of returning.
solution.getRandom();
```

Solution:

``` java
public class Solution {
    Random r;
    ListNode head;

    public Solution(ListNode head) {
        this.r = new Random();
        this.head = head;
    }

    public int getRandom() {
        int count = 1;
        ListNode nodeVal = head;
        ListNode curr = head;
        while (curr != null) {
            if (r.nextInt(count++) == 0) {
                nodeVal = curr;
            }
            curr = curr.next;
        }
        return nodeVal.val;
    }
}
```

- Random Pick Index

Given an array of integers with possible duplicates, randomly output the index of a given target number. You can assume that the given target number must exist in the array.

Note:
The array size can be very large. Solution that uses too much extra space will not pass the judge.

Example:

``` java
int[] nums = new int[] {1,2,3,3,3};
Solution solution = new Solution(nums);

// pick(3) should return either index 2, 3, or 4 randomly. Each index should have equal probability of returning.
solution.pick(3);

// pick(1) should return 0. Since in the array only nums[0] is equal to 1.
solution.pick(1);
```

Solution:

``` java
public class Solution {
    int[] nums;

    public Solution(int[] nums) {
        this.nums = nums;
    }

    public int pick(int target) {
        int index = -1;
        int count = 1;
        Random random = new Random();
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) {
                if (random.nextInt(count++) == 0) {
                    index = i;
                }
            }
        }
        return index;
    }
}
```

---

### kmeans中的蓄水池抽样

转载自[这里](http://blog.jobbole.com/42550/)

**Cloudera ML**： 基于hadoop的ML

分布式蓄水池抽样是Greg讨论的第一种算法。可以从前面的讨论中发现，基本的蓄水池抽样要求对数据流进行顺序读取。要进行容量为k的分布式蓄水池抽样（前面讨论的容量都为1）我们使用mapreduce 模拟sql中的ORDER BY RAND (随机抽取)。对于集合中的每一个元素，都产生一个0-1的随机数，之后选取随机值最大的前k个元素。这种方法在对大数据集进行分层抽样的时候非常管用。这里每一个分层都都是一些分类变量如性别，年龄，地理信息等的组合。注意如果输入的数据集分布极端的不均匀，那么抽样可能不能覆盖到所有的层级。为了对每种分类的组合进行抽样，cloudera ML 提供了sample命令，它可以操作纯文本或者hive中的表。

第二个算法更加好玩：加权分布式蓄水池抽样。这里集合中的数据是有权重的，算法希望数据被抽样选中的概率和该数据的权重成比例。实际上这个问题之前并不一定有解，直到2005年pavlos efraimidis和paul spirakis的论文《weighted random sampling with a reservoir》。他们的解法既简单又优雅，基本思想和上面的分布式蓄水池抽样一致：`对于每个数据计算一个0-1的值R，并求r的n次方根作为该数据的新的R值。`这里的n就是该数据的权重。最终算法返回前k个R值最高的数据然后返回。根据计算规则，权重越大的数据计算所得的R值越接近1，所以越有可能被返回。

在cloudera ML项目中，为了更好地使用k-means++算法（K-均值++算法），我们会首先使用加权的蓄水池抽样算法对输入数据进行抽样。ksketch命令会为k-means++算法进行初始化–在输入数据上进行迭代操作，选择样本抽样。每次选取过程，数据被选入样本的概率和该数据与当前样本中最短距离节点的距离成比例。通过使用加权的蓄水池抽样算法，只需扫描数据一遍就能决定样本组成（一般方法需要首先遍历一次以计算出聚类的总代价，之后第二次遍历根据第一次的计算结果进行样本选择）。