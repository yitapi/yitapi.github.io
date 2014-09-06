---
layout: post
title: "Rosalind with Julia - 4"
description: ""
category: 技术
tags: [bioinformatics, julia, rosalind]
---
{% include JB/setup %}

由于之前的认真刷题，现在我已经大踏步进入不到1000个人解决的问题堆里了。考虑到Rosalind用户数目本身的稀少，这也不是啥了不起的事情= =。不过问题的难度倒是有了明显的提高。下面说的这俩问题，都是让我想了半天的。。

两个问题都是关于RNA二级结构预测的。RNA和DNA一样，碱基也是要配对的，A和U，G和C。至于它为什么不能形成DNA那样的双链，Watson和Crick在搭建DNA结构模型的时候已经说清楚了（可是有没有人纳闷RNA的结构里面还是一堆loop--双链部分）。这些配对的部分是RNA二级结构的重要组成部分，叫做环（loop）-- 为啥叫这个我也是百思不得其解啊。。anyway，当一条RNA序列特别完美的时候--有相同数目的AU以及GC--所有的碱基就都有配对的机会。将这时候的RNA看作一个图(Graph)，那么这个图的一个perfect matching(不知道中文叫什么。。)就代表了理想情况下RNA的一种可能配对方式。（说到perfect matching，立马想到这个不知道为啥能发Nature的[文章](http://www.nature.com/nature/journal/v473/n7346/abs/nature10011.html)了。。）

Rosalind上的一个问题就是计算这种可能的perfect matching的数目。一看到这个我立马就开搜，结果搜到一个我见过的最炫酷的算法能够计算[平面图](http://en.wikipedia.org/wiki/Planar_graph)中所有perfect matching的数目。这个炫酷的算法叫做[FKT algorithm](http://en.wikipedia.org/wiki/FKT_algorithm)。和所有炫酷的算法一样，这个FKT算法非常难懂而且看起来非常难实现。我纳闷了一晚上之后跑去看别人的答案，结果发现根本不是那么回事，RNA中碱基分布乱七八糟的，把他们都配对连接成的图根本不是一个平面图。。所以根本用不着这个复杂的算法。。其实这个问题最后只是一个简单的组合问题-- 也就是数出有多少个A，多少个G，算算他们和U、C的配对一共有多少种-- 结果就是$$(#A)!(#G)!$$这么简单- -。
