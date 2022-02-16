---
layout: post
title: "Data Sketching笔记 - Quantiles"
categories:
  - "Data Sketching笔记"
tags:
  - "概率"
  - "Sketch"
  - "分位数"
draft: false
id: 209
date: 2021-06-02 10:35:47
toc_number_disable: true
comments: false
permalink:
description:
cover_img:
---

> 本文是对于分位数（Quantiles）计算方法的总结，主要基于Data Sketching技术。

## 计算分位数

Quantiles的计算非常基础，被认为是和sum、max、top-k、count-distinct一样十分基础的数据操作。Quantiles是分布的一种近似表达，许多分布的相似度可以通过对比它们的quantiles进行对比，这就是Kolmogorov-Smirnov Divergence（和KL散度相比，KS散度可以被视为一种对称距离，一些讨论可见[^1]）。

给出Quantiles的一般定义：

> 给定一个包含$n$个元素的有序的multi(set) $S$，$\phi$-quantile ($0 < \phi < 1$) 是在$S$中排名为第 $\left \lfloor \phi n \right \rfloor$ 小的元素。

由于实际上输入数据本身容易存在误差，因此在计算Quantiles往往允许一定的误差率$\varepsilon$，我们定义

> 一个$\varepsilon$-approximate $\phi$-quantile的排名在$(\phi - \varepsilon)n$和$(\phi + \varepsilon)n$之间。

## 算法分类

文献[^2]对算法分类从三个方面进行了叙述：
1. turnstile vs. cash register：前者允许插入和删除一个元素，而后者只允许插入；
2. comparison vs. fixed universe：对于前者，算法只能维护一组它已经观测到的元素并从中计算quantiles不能“创造”一个新的元素作为quantiles。对于后者，元素可能认为是1到$u$之间的所有可枚举整数。comparison算法可被运用到fixed universe，但反之则不行。comparison算法可以在很多场景下适用，例如变长的字符串、用户自定义类型等等；
3. deterministic vs. randomized：在randomized的算法中，允许以一定概率出现那些超过了误差率$\varepsilon$的分位数计算结果。通常，这个概率值会被设置成一个常量。

## Cash Register算法

### Greenwald-Khanna算法

GK算法的sketch可以记为$S(n)$，它是一个排序元组对应所有观测数据的一个有代表的子集。其当中的每个元素$v \in S(n)$，我们需要能获知其对应的implicit bound，即其对于所有观测点而言的最大可能和最小可能的rank，记为$rmin(v)$和$rmax(v)$。实际上，$S(n)$中的每个元素的存储是一个三元组$(v_i, g_i, \Delta_i)$，包含：
1. $v_i$是数据流中实际出现的值；
2. $g_i = rmin(v_i) - rmin(v_{i-1})$；
3. $\Delta_i = rmax(v_i) - rmin(v_i)$.

其中，$g_i$和排列的上一个元素相关，$\Delta_i$只和当前元素$v_i$相关。

上述数据结构保证我们能够恢复$S(n)$中的每个元素的最大和最小可能rank，比如
1. $rmin(v_i) = \sum_{j \leq i} g_j$ （相对于第一个元素的最小可能排名）
2. $rmax(v_i) = \sum_{j \leq i} g_j + \Delta_i = \sum_{j \leq i} (g_j + \Delta_j)$

$S(n)$在构建中需要满足以下两个条件：
1. $\sum_{j \leq i} g_j \leq r(v_i) + 1 \leq \sum_{j \leq i} g_j + \Delta_i $ （该条件定义了排名的上界和下界）
2. 对任意元素$v_i$而言，$g_i + \Delta_i \leq \left \lfloor 2\varepsilon n \right \rfloor$ （证明参见[^4]）

对于当前最大的元素和最小的元素（当$i = 0$或者$i = n-1$的时候），他们的最大排名和最小排名是一样的，即$\Delta_i = 0$。

此外，$g_i + \Delta_i - 1$是$v_{i-1}$和$v_i$之间最多可能出现的元素的个数，要求$g_i + \Delta_i \leq \left \lfloor 2\varepsilon n \right \rfloor$ （上述条件2）是为了保证从$\varepsilon n$到任意一个$\phi n$之间至少有一个元素。

关于GK算法的insert、delete、compress和get rank的算法，可以参考[^3][^4]的解读。

### Q-Digest算法

Q-Digest （Quantile Digest）可以看成是一个叶结点是值本身的二叉树。其通过设置压缩比将fixed universe的数值在index上进行索引。

## 扩展阅读

[^1]: [https://stats.stackexchange.com/questions/9311/kullback-leibler-vs-kolmogorov-smirnov-distance.](https://stats.stackexchange.com/questions/9311/kullback-leibler-vs-kolmogorov-smirnov-distance)
[^2]: [Quantiles over data streams: An experimental study.](https://dl.acm.org/doi/abs/10.1145/2463676.2465312)
[^3]: [https://papercruncher.wordpress.com/2010/03/02/stream-algorithms-order-statistics/.](https://papercruncher.wordpress.com/2010/03/02/stream-algorithms-order-statistics/)
[^4]: [https://blog.csdn.net/matrix_zzl/article/details/78641264.](https://blog.csdn.net/matrix_zzl/article/details/78641264)
