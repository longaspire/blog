---
layout: post
title: Latex书写误区
categories:
  - "操作备忘"
tags:
  - "Latex"
toc_number_disable: true
draft: false
comments: false
date: 2018-05-14 19:29:49
cover_img: /blog/post_cover_images/drops.jpg
cover_img_from_root: true
permalink:
description:
---

> 在Latex的书写过程中，往往想当然地输入一些代码，咋看起来似乎没有错，不影响大局。事实上，在不同的样式下可能存在潜在的隐患。为了保持严谨科学的态度，在这里仅对一些Latex书写上的误区进行列举，以防犯下类似的错误。

部分内容引用自[^1]。

### 1. 引号

正确：
```latex
``yes''
```

错误：
```latex
''no''
```

### 2. 微分符号

一般微分符号，例如dx，和前面的被积表达式要有个小间距(`\,`)，而且一般要求要**正写**微分符号d，即（`\mathrm{d}`）

正确：
```latex
\int_a^b f(x)\,\mathrm{d}x
```

错误：
```latex
\int_a^b f(x) dx
```

### 3. 多字母函数名

常见的错误就是log、cos、sin这些函数直接用字母输入。

正确应该如`\log`、`\cos`等。

### 4. 省略点

常见的错误即使用

```latex
x_1, ..., x_n
```

正确的写法应该为

```latex
x_1, \ldots, x_n
```

### 5. 竖线分隔符

例如，在条件概率中，`|`应该写为`\mid`。

而 `||` 也应该写为`\parallel`。

### 6. 积分符号

积分符号过短，如

> $\int_a^b f(x)\,\mathrm{d}x$

这种情况下，在`int`前面加上`\displaystyle`即可变成很长的积分符号，如

> $\displaystyle\int_a^b f(x)\,\mathrm{d}x$

### 引用

[^1]: [Top four LaTeX mistakes](https://www.johndcook.com/blog/2010/02/15/top-latex-mistakes/)
