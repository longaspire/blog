---
layout: post
title: "LSH那些事儿 (III): 哈希家族"
categories:
  - "LSH那些事儿"
tags:
  - "Hash"
  - "Index"
  - "LSH"
  - "距离度量"
draft: false
id: 126
date: 2013-04-26 13:00:43
toc_number_disable: true
comments: false
permalink:
description:
cover_img:
---

> [上一篇](../LSH那些事儿2/)对LSH的形式化进行了说明，接下来我们对LSH的哈希家族进行阐述。

### 1. 关于距离

两点之间的距离可以用两种方式来衡量，一是几何距离，二是非几何距离。

几何距离的定义满足下面的条件：
1. $d(\mathbf{x},\mathbf{y}) \geq 0$
2. $d(\mathbf{x},\mathbf{y}) = 0 ~\text{iff}~ x = y$
3. $d(\mathbf{x},\mathbf{y}) = d(y,x)$
4. $d(\mathbf{x},\mathbf{y}) \leq d(x,z) + d(z,y)$
几何距离包括各类Norm等等。

非几何距离也用的就比较多:
1.  Jaccard距离：1减去Jaccad相似度
2.  余弦距离：两个向量之间的角度
3.  编辑距离 (Edit distance)：将一个串变为另一个串需要的插入或删除次数
4.  海明距离 (Hamming Distance)：0-1向量中不同位置的个数

这里有一个重要的公理：三角形公理 (Triangle Inequality) ，也就是几何距离的第四个性质。

编辑距离$d(\mathbf{x},\mathbf{y}) = |x| + |y| - 2|LCS(\mathbf{x},\mathbf{y})|$，其中LCS是longest common subsequence，称为最长公共子序列。

### 2. LSH的核心思想

LSH的一个核心思想就是两个元素哈希值相等的概率等于两个元素之间的相似度。

下面介绍一个重要概念：Hash Family，它指的是能够判断两个元素是否相等的Hash函数集合。
$$h(\mathbf{x}) = h(\mathbf{y})$$

LS Hash Family，局部敏感哈希家族的定义是满足下面两个条件的哈希函数家族：

1.  如果$d(\mathbf{x},\mathbf{y}) < d_1$，那么哈希家族$H$中的哈希函数$h$满足$h(\mathbf{x}) = h(\mathbf{y})$的概率至少是$p_1$.
2.  如果$d(\mathbf{x},\mathbf{y}) > d_2$，那么哈希家族$H$中的哈希函数$h$满足$h(\mathbf{x}) = h(\mathbf{y})$的概率至多是$p_2$.

通俗来解释，就是如果$\mathbf{x}$和$\mathbf{y}$离得越近，$\mathsf{Pr}(h(\mathbf{p})=h(\mathbf{q}))$就越大。如果$\mathbf{x}$和$\mathbf{y}$离得越远，$\mathsf{Pr}(h(\mathbf{p})=h(\mathbf{q}))$就越小。我们把局部敏感哈希家族记为$(d_1,d_2,p_1,p_2)$-sensitive。

那什么样的函数满足呢？Jaccard就是。我们令$S$为一个集合，$d$是Jaccard距离，有$\mathsf{Pr}(h(\mathbf{x})=h(\mathbf{y})) = 1-d(\mathbf{x},\mathbf{y})$，我们就可以得到一个局部敏感哈希家族：

> $(1/3, 2/3, 2/3, 1/3)$-sensitive

事实上，只要满足$d_1 < d_2$, 就可以得到一个局部敏感的哈希家族：

> $(d_1,d_2,(1-d_1),(1-d_2))$-sensitive

$(d_1,d_2,p_1,p_2)$-sensitive将整个概率空间分成了三部分：

> $\leftarrow p_1$，$p_1 \rightarrow p_2$，$\rightarrow p_2$

为了有更好的区分度，我们想让$p_1 \rightarrow p_2$的空间尽可能小，让$d_2-d_1$尽可能大。选择合适的参数，有类似于下面的S曲线：

![](15144243_L3JA.png)

### 3. 两种哈希函数操作

#### AND操作

在局部敏感哈希家族$H$中选出$r$个哈希哈数，构成哈希家族$H'$。

对于$H'$家族中的$h = [h_1, \ldots,  h_r]$，$h(\mathbf{x}) = h(\mathbf{y})$当且仅当对所有的$i$都满足$h_i(x)=h_i(y)$。这样得到的$H'$同样也是一个局部敏感哈希家族。

并且若原有的哈希家族是$(d_1,d_2,p_1,p_2)$-sensitive，新哈希家族$H'$是$(d_1,d_2,{p_1}^r,{p_2}^r)$-sensitive的。

#### OR操作

在局部敏感哈希家族$H$中选出$b$个哈希哈数，构成哈希家族$H'$。

对于$H'$家族中的$h = [h_1, \ldots,  h_b]$，$h(\mathbf{x}) = h(\mathbf{y})$当且仅当存在一个$i$满足$h_i(x)=h_i(y)$。这样得到的$H'$同样也是一个局部敏感哈希家族。

并且若原有的哈希家族是$(d_1,d_2,p_1,p_2)$-sensitive，新哈希家族$H'$是$(d_1,d_2,1-(1-p_1)^b,1-(1-p_2)^b)$-sensitive的。

#### 对比

可以看出AND操作是降低了概率，但如果选取一个合适的$r$，可以使下限概率接近0，而上限概率没有多大影响。

类似的，OR操作是增加了概率，但如果选择一个合适的$b$，可以使上限概率接近1，而下限概率没有多大影响。

我们对AND操作和OR操作做级联：
- AND-OR，$1-(1-p^r)^b$
- OR-AND，$(1-(1-p)^b)^r$

### 4. 调参

现在我们选定$r = b = 4$，那么概率函数可以这么写

<div class="center">
<style>
table {
margin: auto;
border: 1px solid red
}
</style>
<table>
<thead>
<tr>
  <th>p</th>
  <th>1-(1-p^4)^4</th>
</tr>
</thead>
<tbody>
<tr>
  <td>0.2</td>
  <td> .0064</td>
</tr>
<tr>
  <td>0.3</td>
  <td> .0320</td>
</tr>
<tr>
  <td>0.4</td>
  <td> .0985</td>
</tr>
<tr>
  <td>0.5</td>
  <td> .2275</td>
</tr>
<tr>
  <td>0.6</td>
  <td> .4260</td>
</tr>
<tr>
  <td>0.7</td>
  <td> .6666</td>
</tr>
<tr>
  <td>0.8</td>
  <td> .8785</td>
</tr>
<tr>
  <td>0.9</td>
  <td> .9860</td>
</tr>
</tbody>
</table>
</div>

得到这样的S曲线之后，可以找到一个点t，满足$1-(1-t^r)^b = t$。在t点之后，概率快速上升，在t点之前，概率快速下降。根据需要的灵敏度，可以选择合适的上限概率和下限概率来满足应用需求。

`继续阅读系列`[下一篇](../LSH那些事儿4/)，将会对$p$-stable LSH进行说明。
