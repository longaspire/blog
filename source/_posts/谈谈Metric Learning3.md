---
layout: post
title: '谈谈Metric Learning (III): ITML'
categories:
  - "谈谈Metric Learning"
tags:
  - "信息论"
  - "ITML"
  - "KL散度"
  - "LogDet"
  - "Metric Learning"
  - "距离度量"
draft: false
id: 380
date: 2015-06-03 15:41:19
comments: false
toc_number_disable: true
cover_img: /blog/post_cover_images/firenze.png
cover_img_from_root: true
permalink:
description:
---

### 1. 引言

这一篇我们来谈谈metric learning中，更具体而言，是Mahalanobis distance learning中的经典算法ITML，也称作**Information-Theoretic Metric Learning**，顾名思义，就是借助信息学理论知识对Mahalanobis distance进行优化。

[这篇文章](https://scholar.google.com/scholar?hl=zh-CN&amp;q=information+theoretic+metric+learning+&amp;btnG=&amp;lr=)发表在2007年机器学习会议ICML上，随后取得了巨大成功，后来的工作很多都得到了作者Jason Davis对于LogDet divergence在metric learning中使用的启发，进行了一系列理论和实验优化。

### 2. 问题定义

#### 2.1 距离

对于一个$n$个点构成的集合${x\_1,...,x\_n}$, 其中$x\_i \in \mathbb{R}^d$，我们可以得到马氏距离的定义：

> $d\_A(x\_i, x\_j) = (x\_i - x\_j)^T A (x\_i - x\_j)$

这里我们稍微采取了一点处理，首先为了避免平方根，我们将马氏距离取平方；其次，我们用一个symmetric PSD矩阵$A$来替代之前使用的$M$。

#### 2.2 限制集合

之前我们将集合中的items分为must-link和must-not-link集合，而在此处的限制集合（Constraints Set）对应的是一个similar set $\mathit{S}$和一个dissimilar set $\mathit{D}$。

这里，我们将其称之为Interpoint Distance Constraints，其中对于两个相似（不相似）的items有

> $ d\_A(x\_i,x\_j) \leq u $
> $ d\_A(x\_i,x\_j) \geq l $

$u$($l$)是一个值很小(大)的upper（lower）bound。

#### 2.3 问题核心

除了这些side information，对于一个半监督或者全监督问题，我们往往会获取到一些关于使用怎么的度量更容易得到好的accuracy的指导，这些知识我们称之为先验的 (prior)。

例如，对于一个数据是Gaussian分布的问题，我们往往期望
> parameterizing the distance function by the inverse of the sample covariance.

同样，对于一些欧式空间的距离度量，我们往往希望distance function是接近欧式距离的，因此，我们需要对我们的PSD matrix也就是$A$采取优化，具体而言就是，当我的先验知识告诉我$A$应该要逼近一个由$A\_0$定义的度量时，我们往往需要在满足限制条件的情况下，使得$A$尽可能地接近我们选择的$A\_0$。

这儿，就是ITML的核心思想，如何去选择合适的$A\_0$并使得我们learn的$A$尽可能逼近它。

#### 2.4 使用KL散度

又一次使用散度的概念，这在我们metric learning系列的[第一篇](谈谈Metric Learning1)已经提到，散度用于分析随机变量在两个分布下的相似度。这里的两个分布自然是由$A\_0$和$A$来度量的，这是因为$A\_0$和$A$是两个分布的协方差矩阵的逆。

我们需要来定义一个，$x\_i$在$A$下的Gaussian分布：

> $p(x;A) = \frac{1}{Z} \cdot \exp \{ -\frac{1}{2} d\_A(x,\mu) \}$

其中，$Z$是一个用于正规化处理的常数，而$A$是分布的协方差covariance，$\mu$是mean，那么我们可以定义出$A$与$A\_0$这两个分布的KL散度。

> $\mathit{KL}(p(x; A\_0) || p(x; A)) = \displaystyle\int p(x ; A\_0) \log{\frac{p(x; A\_0)}{p(x; A)}} \rm{d}x$

#### 2.5 问题形式化

那么，对于给定的constraints set $\mathit{S}$和$\mathit{D}$，我们将问题形式化为：

> $\min\limits\_{A} \mathit{KL} (p(x; A\_0) || p(x; A))$
>
> $\text{s.t.}$
>   $d\_A(x\_i, x\_j) \leq u, (i,j) \in \mathit{S}$
>   $d\_A(x\_i, x\_j) \geq l, (i,j) \in \mathit{D}$

### 3. 算法

#### 3.1 LogDet优化

先看一个凸函数

> $\Phi(X) = -\log\det X$

这个函数是定义在正定矩阵的cone上的，基于这个函数，我们可以把它的[Bregman matrix divergence](http://en.wikipedia.org/wiki/Bregman\_divergence)做成一个LogDet divergence。事实上，LogDet divergence是用于来描述两个矩阵的差异性。

上述divergence，我们提到是对于两个矩阵，即$A$和$A\_0$的差异性的度量，可以这么写：

> $D\_{ld}(A, A\_0) = tr(A A^{-1}) - \log\det(A A\_0^{-1}) - n$

联系我们在上一节中介绍过的KL散度，两个metric定义中的矩阵$A$和$A\_0$的“closeness”就可以通过散度，也就是LogDet divergence来一起定义，那么写成

> $ \mathit{KL}(p(x; A\_0) || p(x;A)) = \frac{1}{2} \cdot D\_{ld}(A\_0^{-1},A^{-1}) = \frac{1}{2} \cdot D\_{ld}(A,A\_0)$

事实上，这个等价推导过程是非常巧妙的，它借鉴了微分相对熵的一些知识，这在[Davis2006](http://machinelearning.wustl.edu/mlpapers/paper\_files/NIPS2006\_147.pdf)中有很详细的介绍。

最后，在这里我们可知，2.5给出的问题形式化，从最小化KL散度最后变成了一个最小化LogDet的问题。但是，我们给出更加严格化的问题描述:

1.  给出$c(i,j)$，表示第$(i,j)$-th个constraint；
2.  给出trade-off parameter $\gamma$；
3.  给出松弛变量slack variables，并将其初始化为$\vec{\xi}\_0$，注意这是一个vector，其中等于$u$的部分为similar constraints，等于$l$的部分为dissimilar constraints；
4.  那么对于一个Mahalonbios距离的学习问题，我们需要保证$A$是对称半正定的，形式化就可以写成$A \succeq 0$，现在我们要最小化$A$和$\vec{\xi}$，并保证两个矩阵相似。

终于，我们可以来重新定义一个严格的问题描述：

> $\min\limits\_{A \succeq 0, \vec{\xi}} D\_{ld}(A,A\_0) + \gamma \cdot D\_{ld}(diag(\vec{\xi}), diag(\vec{\xi\_0}))$
>
> $\text{s.t.}$
> $tr(A(x\_i,x\_j)(x\_i,x\_j)^T) \leq \vec{\xi}\_{c(i,j)}, (i,j) \in \mathit{S} $
> $tr(A(x\_i,x\_j)(x\_i,x\_j)^T) \geq \vec{\xi}\_{c(i,j)}, (i,j) \in \mathit{D} $

那么最终，ITML的距离度量，从一堆constraints中对于$A$的优化实际上变成了一个LogDet的优化问题。

这个函数，我们可以概括为：

1.  希望$A$和$A\_0$尽量靠近；
2.  希望对应的松弛变量$\vec{\xi}$和$\vec{\xi}\_0$尽可能地靠近；
3.  优化参数$A$为半正定。

#### 3.2 算法解释

我们先把算法的伪代码贴出看看

```C
Input:  X: input d*n matrix;
        S: set of similar pairs;
        D: set of dissimilar pairs;
        u,l: distance thresholds;
        A_0: input Mahalanobis matrix;
        r: slack parameter;
        c: constraint index mapping

Output: A: output Mahalanobis matrix

//initialization
A = A_0;
forall i,j:
   lambda_ij = 0;
forall i,j:
   idx = c(i,j);
   if (i,j) is in S:
     xi_idx = u;
   else:
     xi_idx = l;

//iteration
while(!convergence):
   pick a constraint (i,j); idx = c(i,j);
   p = dot(dot((x_i - x_j).T, A), (x_i - x_j));
   if (i,j) is in S:
      delta = 1;
   else:
      delta = -1;
   alpha = min(lambda_ij, 1/2*(1/p - r/xi_idx));
   beta = delta * alpha / (1 - delta * alpha * p);
   xi_idx = r * xi_idx / (r + delta * alpha * xi_idx);
   lambda_ij = lambda_ij - alpha;
   A = A + beta * dot(dot(dot(A, (x_i - x_j)), (x_i - x_j).T), A)))

return A

```

我们可以看到，上述是一个projected gradient descent的过程。循环中的34行实际上是一次projection，保证$A$依然在convex set中，这事实上是一个Bregman projection过程：

> $A\_{t+1} = A\_{t} + \beta A\_{t}(x\_i, x\_j)(x\_i, x\_j)^TA\_{t} $

其中，$\beta$是projection parameter，一个与constraint相关的拉格朗日乘子。一次projection的时间复杂度是$O(d^2)$，那么对于有$c$个constraint的一次iteration，则时间复杂度为$O(cd^2)$。

### 结语

> 写到这里，也许你已经大概清楚了ITML在做什么，简单的实现是怎样的。然而，我们还没有完全谈到ITML的精髓，[下一篇](/blog/谈谈Metric Learning4)，我们会介绍引入kernel learning的方法来解决参数优化的问题，希望有更多篇幅来分享这一算法。
