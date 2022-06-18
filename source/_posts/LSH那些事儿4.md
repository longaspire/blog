---
layout: post
title: "LSH那些事儿 (IV): $p$-stable LSH"
categories:
  - "LSH那些事儿"
tags:
  - "Hash"
  - "Index"
  - "LSH"
  - "距离度量"
draft: false
id: 172
date: 2013-05-06 11:41:46
toc_number_disable: true
comments: false
permalink:
description:
cover_img:
---

> [上一篇](../LSH那些事儿3/)对LSH的哈希家族进行了介绍，本篇来介绍一下$p$-stable LSH。本篇内容摘抄自[^1]。

### 1. 背景介绍

LSH是用局部敏感的方法解决近似最近邻搜索的问题。在原始的LSH方法中，通过将原始空间嵌入到Hamming空间中，将$d$维空间转换成${d}' = Cd$维的Hamming空间 (C是指原始空间中点的坐标的最大值)，使用$(r,(1+e)r,1-r/{d}',1-(1+e)r/{d}')$-sensetive 哈希函数来解决$(r,e)$-Neighbor问题。

而后来提出的$p$-stable LSH算法中，不需要将原始空间嵌入到Hamming空间中，可以直接在欧氏空间下进行局部敏感哈希运算。

$p$-stable LSH应用在d维$L_p$-norm下的欧氏空间中，$0 < p \leq 2$。

$p$-stable LSH使用的$(r,cr,p_1,p_2)$-sensetive哈希中，$c=1+e$，下面的工作主要是确定在1 (即$r$) 和 c (即$cr$) 下的$p_1$与$p_2$。

### 2. 概念解释

$p$-稳定分布的概念：
一个分布$D$如果称为$p$-稳定分布，则对于任意$n$个实数$v_1, v_2, \ldots, v_n$和符合$D$分布的$n$个独立同分布随机变量$X_1, X_2, \ldots, X_n$，都存在一个$p \geq 0$，使得变量$\sum_i v_i X_i$和$\sum_{i} (|v_i|^p)^{1/p}X$ (i.e.,$||v||_p X$) 具有相同的分布，此处$X$是一个符合$D$分布的随机变量。

$p$-稳定分布不是具体的分布，而是满足某条件的分布族。
- Cauchy Distribution, pdf为$c(x) = \frac{1}{\pi} \frac{1}{1+x^2}$, 1-stable.
- Gaussian Distribution, pdf为$g(x) = \frac{1}{\sqrt{2\pi}} e^{-x^2/2}$, 2-stable.

$p$-stable分布有一个重要的应用，就是可以估计给定向量$v$在欧氏空间$p$-norm下长度，记为$||v||_p$。

方法是对于取定的d维向量$v$，从$p$-稳定分布中抽取d个随机变量组成d维向量$a$，计算$a$与$v$的点积$a \cdot v$。

根据$p$-stable的定义，由于$a \cdot v = \sum_{i} (|v_i|^p)^{\frac{1}{p}}X$，具有相同的分布，此处$X$是一个符合$D$分布的随机变量。选取若干个向量$a$，计算多个$a \cdot v$的值，称为向量$v$的概略 (sketch) ，利用$v$的“sketch”可以用来估算$||v||_p$的值。

### 3. 局部敏感哈希函数

在$p$-stable LSH中，$a$与$v$的点积$a \cdot v$不用来估计$||v||_p$的值，**而是用来生成哈希函数族**，且该哈希函数族是局部敏感的 (即空间中距离较近的点映射后发生冲突的概率高，空间中距离较远的点映射后发生冲突的概率低) 。

大体方法是将一条直线分成等长且长度为$r$的若干段，给映射到同一段的点赋予相同的hash值，映射到不同段的点赋予不同的hash值。($a \cdot v_1 - a \cdot v_2$)是映射后的距离，而其值与$||v_1 - v_2||_p X$同分布，原始距离$||v_1 - v_2||_p$较小时，映射后的距离也小，因此使用点积来生成哈希函数族可以保持局部敏感性。

哈希函数族的形式为：

$$
h_{a,b} = \left \lfloor \frac{a \cdot v + b}{r} \right \rfloor
$$

其中$b$是$(0,r)$里的随机数，$r$为直线上分段的段长。哈希族中的函数根据$a$和$b$的不同建立函数索引。

从哈希函数族中随机选取一个哈希函数，现在估计两个向量$v_1$和$v_2$在该哈希函数下映射后发生冲突的概率。

定义符合$p$-stable分布的随机变量绝对值的概率密度函数为$f_p(t)$。设$c=||v_1 - v_2||_p$，则$a \cdot v_1 - a \cdot v_2$与$cX$同分布，$X$为$p$-stable分布下的随机变量。给出概率的计算公式如下，之后会有详细分析。

$$
p(c) = \mathsf{Pr}_{a,b}(h_{a,b}(v_1) = h_{a,b}(v_2))
= \int_0^r \frac{1}{c}f_p(\frac{t}{c})(1-\frac{t}{r})\mathsf{d} t
$$

其中$|a \cdot v_1 - a \cdot v_2| = ||v_1 - v_2||_p|X| = c|X|$，$X$为$p$-stable分布下的随机变量，$|X|$的概率密度函数为$f_p(t)$。

若要向量$v_1$和$v_2$映射后发生冲突，需要满足如下条件：$v_1$和$v_2$通过与$a$进行点积运算分别映射到一段长度为r线段后，再通过加b运算，能使映射后的点在同一条线段上。

### 4. 结语

$p$-stable LSH通过稳定分布和点积的概念，实现了LSH算法在欧氏空间下的直接应用，而不需要嵌入Hamming空间。$p$-stable LSH中，度量是欧氏空间下的$L_p$准则，即向量$v_1$与$v_2$的距离定义为$||v_1 - v_2||_p$，然后通过设定的哈希函数将原始点映射到直线的等长线段上，每条线段便相当于一个哈希桶，与LSH方法类似，距离较近的点映射到同一哈希桶 (线段) 中的概率大，距离较远的点映射到同一哈希桶中的概率小，正好符合局部敏感的定义。

### 参考

[^1]: [https://blog.csdn.net/lskyne/article/details/8654455](https://blog.csdn.net/lskyne/article/details/8654455)
