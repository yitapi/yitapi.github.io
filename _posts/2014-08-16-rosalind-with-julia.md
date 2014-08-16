---
layout: post
title: "Rosalind with Julia"
description: ""
category: 技术 
tags: [julia, bioinformatics, rosalind]
---
{% include JB/setup %}

[Rosalind](http://rosalind.info/)是一个生物信息学的学习平台。和Project Euler, 4Clojure等网站一样，它也是一个Learning by doing的作业平台。不同的是上面都是一些生物信息学问题，相同的是需要你写个小程序来解决上面的问题。由于我

 - 将来工作有可能有很大一部分与生物信息学相关;
 - 最近开始接触[julia](http://julialang.org)，看上去是个很有希望的语言;
 - 最近闲的蛋疼;

我决定开始用julia做一下Rosalind上面的问题。今天是第一天= =，做了最简单的三道题，都是非常简单的序列操作。下面这些代码里面`dna`指的就是操作对象，一个存储为`String`类型的DNA序列。

第一个是数数，就是给一段DNA序列，数里面各种碱基(nucleotide)各有多少。
{% highlight julia %}
A = countnz([x == 'A' ? 1 : 0 for x in dna])
{% endhighlight %}
其中， `countnz`这个函数的作用是数出一个序列里面非零元素的个数。
<br>

第二个是DNA转RNA, 这个也很简单，就是把DNA里面的T（胸腺嘧啶）换成RNA里的U（尿嘧啶）
{% highlight julia %}
print(replace(dna, r"T", 'U'))
{% endhighlight %}
<br>

第三个是反向互补序列，这个会稍微复杂一点。（顺便测试一下gist。。）这里面dna序列文件是存储在`dna3.txt`里。
{% gist yitapi/788e83377965449cd328 %}
<br>

OK 就这样。。明天有空继续。


