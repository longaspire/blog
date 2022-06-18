---
layout: post
title: "LSH那些事儿 (II): 图说"
categories:
  - "LSH那些事儿"
tags:
  - "Hash"
  - "Index"
  - "LSH"
draft: false
id: 108
date: 2013-04-18 17:04:31
toc_number_disable: true
comments: false
permalink:
description:
cover_img:
---

> [上一篇](../LSH那些事儿1/)中我们大体介绍了LSH的应用场景和方法。接下来我们对LSH的形式化进行进一步说明。

### 1. 定义回顾

LSH旨在构造一种Hash函数集$\{ D \mid R^d \rightarrow U \}$，其中$d$是点的维数，使得对任意的点$\mathbf{p}$，$\mathbf{q}$有

1.  如果$|| \mathbf{p} - \mathbf{q} || \leq r$, 那么$\mathsf{Pr}(D(\mathbf{p}) = D(\mathbf{q}))$要很高
2.  如果$|| \mathbf{p} - \mathbf{q} || \geq cr$, 那么$\mathsf{Pr}(D(\mathbf{p}) = D(\mathbf{q}))$要很低

如下图：

![](201203141042366430.png)

### 2. 基于投影的LSH原理

![](201203141042388707.png)

如图有四个点：$A(x_a, y_a), B(x_b, y_b), C(x_c, y_c), D(x_d, y_d)$

如果取的Hash函数为$H(A(x_a,y_a)) = x_a$

即，在$X$轴上的投影，那么就有$A$,$B$,$C$,$D$在$X$轴上的投影分别是：$x_a$,$x_b$,$x_c$,$x_d$。

重点来了，空间相近的点在$X$轴上的投影也是相近的，这样我们可以利用这个特性来做临近点的查询，基于投影的LSH的基本原理就是这样的。

不过上面这种方法是能保证挨得近的点散列后得到的一维值也挨的很近，但是一些本来不近的点在散列后，得到的也是很近的值，如A与D点。

### 3. $(P_1,P_2,r,cr)$-sensitive LSH

定义如下

A family $H$ of functions $D: R^d \rightarrow U$ is called $(P_1,P_2,r,cr)$-sensitive, if for any $\mathbf{p}$，$\mathbf{q}$:
  1.  if $|| \mathbf{p} - \mathbf{q} || \leq r$, then $\mathsf{Pr}(g(p) = g(q)) > P_1$
  2.  if $|| \mathbf{p} - \mathbf{q} || \geq cr$, then $\mathsf{Pr}(g(p) = g(q)) < P_2$

第2节构造的$H(A(x_a,y_a)) = x_a$（即在$X$轴上的投影）是不能满足这个要求的，解决的办法是：

![](201203141042424690.png)

想法比较简单的，就是在空间多做几条线，这样本来很近的点，无论在哪条线上的映射都是很近的，而挨的不近的点可能在某个方向的的投影很近，但在其它方向就可能很远，这样，把每个方向的投影结果都利用，也就是说可以对这些结果再进行散列：
$$
h_1(a_1,a_2,\ldots,a_k) = ((\sum_{i=1}^{k}\hat{r}_i a_i) \mod \mathit{prime}) \mod \mathit{tableSize}
$$

就可以达到定义的要求。

现在的问题是：空间线怎么取？到底要取多少条这样的线？空间的线怎样取，在$E^2$LSH中，是根据标准正态分布取的。

`继续阅读系列`[下一篇](../LSH那些事儿3/)，将会对LSH的哈希家族进行阐述。
