---
layout: post
title: '谈谈Metric Learning (II)'
categories:
  - "谈谈Metric Learning"
tags:
  - "Metric Learning"
  - "距离度量"
  - "优化"
draft: false
id: 443
date: 2015-06-03 12:04:04
comments: false
toc_number_disable: true
cover_img: /blog/post_cover_images/firenze.png
cover_img_from_root: true
permalink:
description:
---

### 1. 引子

对于一组给定的特征向量或者是结构化数据，我们总是希望能够快速且准确地在分类或者聚类问题中，将他们按照所需要的target进行区分。

然而，由于这些数据在对于特征的描述上存在着差异化，有时候并不能很好地完成任务。在传统的机器学习领域，面对不同问题时，大家总是会采取对数据进行一定的预处理来达到他们想要的效果，有时候使得accuracy提高，有时候使得问题解决方案简化。

就比如说，在图像处理和模式识别的领域中，人脸识别和表情提取用的raw data都是同样的图片数据集，使用的vector也一致，但是面对具体问题时，往往需要对提取的特征进行一定的手工转换来达到解决问题的目的。

这些“手工”的方式不能一直行之有效，往往在新问题上，不可能快速地准确找到问题的症结去改变特征提取的过程，而metric learning的出现，就是依靠一些问题提供的真实的额外信息（side information），来对距离和特征进行学习，到达解决特定问题自动化的一个研究问题。

### 2. 定义

说了这么多，我们来对这个问题进行形式化的定义，首先我们来说一下数据提供的“先验知识”，即side information。分为两种：

* Must-link / Cannot-link Constraints
  > $S = \{ (x\_i, x\_j) : x\_i\text{ and }x\_j\text{ should be similar} \}$
  > $D = \{ (x\_i, x\_j) : x\_i\text{ and }x\_j\text{ should be dissimilar} \}$

* Relative constraints
  > $R = \{ (x\_i, x\_j, x\_k) : x\_i\text{ should be more similar to }x\_j\text{ than to }x\_k \}$

给定$n$个$m$维的向量， 满足$X \in \mathbb{R}^{n \times m}$，metric learning的目标是找到一个$ m \times r$的矩阵$M$使得变换后的投影子空间能够更好地满足上述side information。具体而言，可以认为是一个参数优化的loss最小化问题。

接下来利用公式来定义一下：

给定一个metric，metric learning试图找到以下解

> $ M^{\*} = \arg\min\limits\_{M}[l(M,S,D,R) + \lambda R(M)] $

其中，$l(M,S,D,R)$是一个loss function，用于惩罚那些不满足constraint的数据，$R(M)$是对于$M$的正则项，$\lambda$是一个正则化参数。以上是优化问题的基本形式。

### 3. 分类

* Learning Paradigm
  * fully supervised;
  * weakly supervised;
  * semi-supervised;
* Form of Metric
  * linear;
  * nonlinear;
  * local;
* Scalability
  * number of examples;
  * dimension;
* Optimality of the Solution
  * local;
  * global;
* Dimensionality Reduction
  * yes;
  * no;

### 结语

> 通过以上，对于metric learning的问题已经窥其一角，[下一篇](/blog/谈谈metric learning3)我们将具体列举一些经典的算法和文章，来剖析一下metric learning的核心思想。
