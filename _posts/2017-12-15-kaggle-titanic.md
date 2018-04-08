---
title: 针对Titanic问题的各种分类器stacking和XGBoost解法
categories:
  - Kaggle
tags:
  - Python
  - MachineLearning
  - XGBoost
permalink: kaggle-titanic
abbrlink: 32063
date: 2017-12-15 22:28:52
---

<h2 id="intro">前言</h2>在典型feature engineering的情况下，使用xgboost和堆叠（仍然有一层xgboost），达到的Public Score都是0.79，相对比较优秀。另有遗传算法，不知道参数如何获得，score 0.88。


<!-- more -->

---------------

使用jupyter notebook，Python3.5，WIN10下Pycharm搭建环境。

---

## feature engineering

主要包括数据读入，特征选择，数据清洗，特征处理。

### reading using Pandas

pandas读入的是DataFrame结构：

```
train_df = pd.read_csv('data\\train.csv') # path from windows
test_df = pd.read_csv('data\\test.csv')
combine = [train_df, test_df] # list
```
可以直接对其操作：
```
train_df.head()
train_df.tail()
train_df.info()
train_df.describe()
train_df.describe(include=['O']) # object
```

### feature select

使用matplotlib和seaborn进行visualized analyze，包括：







