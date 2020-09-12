---
layout: post
title: '谈谈Metric Learning (I)'
categories:
  - "谈谈Metric Learning"
tags:
  - "Metric Learning"
  - "距离度量"
  - "KL散度"
draft: false
id: 347
date: 2015-06-03 10:41:01
comments: false
toc_number_disable: true
cover_img: /blog/post_cover_images/firenze.png
cover_img_from_root: true
permalink:
description:
---

> 最近在学metric learning，主要解决在Indoor Sensor Network中对各类传感数据特征的提取和转换的问题，metric learning自从02年以来得到了很多关注，虽然有人诟病它是**子空间学习**换汤不换药的产物，但是也许，换个说法它的确会受到更多关注，从而在不断发展中显出价值。

### 1. 关于Metric

* pairwise metrics往往是用于度量两个对象相似度（similarity or distance）的；
* metrics在machine learning中无处不在，选择一个有效的metric，往往能够简化问题，提高解决问题的效率和效果；
* metric的应用场景包括分类、聚类、检索和排序、高维数据可视化等。

### 2. Metric基础

#### 2.1 距离函数定义

> A distance over a set $\mathit{X}$ is a pairwise function $d: \mathit{X} \times \mathit{X} \rightarrow \mathbb{R}$ which satisfies the following properties $\forall x, x', x'' \in \mathit{X}$:

1.  非负性 Nonnegativity: $d(x,x') \geq 0$
2.  同一性 Identity of Indiscernibles: $d(x,x') = 0$ if and only if $x = x'$
3.  对称性 Symmetry : $d(x,x') = d(x',x)$
4.  三角不等性 Triangle Inequality : $d(x,x'') \leq d(x,x') + d(x',x'')$

#### 2.2 相似函数定义

> a (dis)similarity function is a pairwise function $\mathit{K}: \mathit{X} \times \mathit{X} \rightarrow \mathbb{R}$.
>
> $\mathit{K}$ is symmetric if $\forall x,x' \in \mathit{X}, \mathit{K}(x,x') = \mathit{K}(x',x)$.

#### 2.3 Minkowski Distance

> $d_p(x,x') = ||x - x'||_p = (\sum\limits_{i=1}^{d}|(x_i - x'_i)^{p}|)^{\frac{1}{p}}$

$p=1$：Manhattan distance 曼哈顿距离
$p =2$：Euclidean distance 欧氏距离
$p \rightarrow \infty$：Chebyshev distance 切比雪夫距离，相当于取各维度差的最大值

#### 2.4 Manhalanobis Distance

> $d_M(x,x') = \sqrt{(x-x')^TM(x-x')}$.

其中，$M$是一个对称半正定矩阵（symmetric PSD matrix），对于$M$的解释，可以这样认为，假设$x,x'$是随机向量，符合同样的分布，且其协方差矩阵（covariance matrix）是$\Sigma$，那么，我们可得到$M = \Sigma^{-1}$.

#### 2.5 Cosine distance

在数据挖掘和信息检索中一个常用的metric是cosine distance，在bag-of-words和sparse vectors中都有很好的应用，是这样定义的：

> $K_{cos}(x,x') = \frac{x^Tx'}{||x||_2||x'||_2}$

类似于计算两个vector的夹角，即方向上有多靠近，下标用的是二范数。

#### 2.6 Bilinear similarity

与2.4写出的马氏距离Manhalanobis distance类似，是由一个矩阵$M \in \mathbb{R}^{d \times d}$来parameterize的，但不要求为半正定或者对称

> $K_M(x,x') = x^TMx'$

#### 2.7 KL散度

又称KL距离，KL-divergence，常用来衡量两个概率分布的距离。

先从熵（entropy）开始说起：

给定一个字符集的概率分布$\mathit{X}$，可设计一种编码，使得表示该字符集组成的字符串平均需要的比特数最少。对$x \in \mathit{X}$，设其出现概率为$P(x)$，那么其最优编码平均需要的比特数等于这个字符集的熵为

> $H(x) = \sum\limits_{x \in \mathit{X}} P(x) {log}{\frac{1}{P(x)}}$

如此，同样的字符集上，假设存在另一个概率分布$Q(X)$。如果用概率分布$P(X)$的最优编码（即字符x的编码长度等于$log(\frac{1}{P(x)})$，来为符合分布$Q(X)$的字符编码，那么表示这些字符就会比理想情况多用一些比特数。

KL-divergence就是用来衡量这种情况下平均每个字符多用的比特数，因此可以用来衡量两个分布的距离。表达公式为：

> $D_{KL}(Q \parallel P) = \sum\limits_{x \in \mathit{X}} Q(x) {log}{\frac{1}{P(x)}} - \sum\limits_{x \in \mathit{X}} Q(x) {log}{\frac{1}{Q(x)}} = \sum\limits_{x \in \mathit{X}} Q(x) {log}{\frac{Q(x)}{P(x)}}$

KL散度是不对称的，且KL散度始终是大于零的， 简单的证明[在此](http://blog.csdn.net/caohao2008/article/details/6910794)。

### 3. 凸优化

凸优化（Convex Optimization）实在是太过于重要，这里应该有很多篇幅来讲，这里只讲对于后续有用的一些重要性质：

1.  function $ f : \mathbb{R}^n \rightarrow \mathbb{R}$ is convex if $x_1, x_2 \in \mathbb{R}^n, 0 \leq a \leq 1 \Rightarrow f(ax_1 + (1-a)x_2) \leq af(x_1) + (1-a)f(x_2)$
2.  function $ f : \mathbb{R}^n \rightarrow \mathbb{R}$ is convex iff its Hessian matrix $\triangledown^2f(x)$ is PSD
3.  if function $ f : \mathbb{R}^n \rightarrow \mathbb{R}$ is convex, then any local minimum of function $ f $ is also a global minimum of $ f $

在凸优化中常用的投影梯度下降算法请看[这里](http://goo.gl/7Q46EA)。

### 结语

> 这些都准备好了，[下一篇](/blog/谈谈Metric Learning2)我们开始讲讲metric learning的主要思想和一些分支。
