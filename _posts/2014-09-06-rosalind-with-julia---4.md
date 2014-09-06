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

Rosalind上的一个问题就是计算这种可能的perfect matching的数目。一看到这个我立马就开搜，结果搜到一个我见过的最炫酷的算法能够计算[平面图](http://en.wikipedia.org/wiki/Planar_graph)中所有perfect matching的数目。这个炫酷的算法叫做[FKT algorithm](http://en.wikipedia.org/wiki/FKT_algorithm)。和所有炫酷的算法一样，这个FKT算法非常难懂而且看起来非常难实现。我纳闷了一晚上之后跑去看别人的答案，结果发现根本不是那么回事，RNA中碱基分布乱七八糟的，把他们都配对连接成的图根本不是一个平面图。。所以根本用不着这个复杂的算法。。其实这个问题最后只是一个简单的组合问题-- 也就是数出有多少个A，多少个G，算算他们和U、C的配对一共有多少种-- 结果就是$(#A)!(#G)!$这么简单- -。

上面考虑的所有的perfect matching有一个问题，就是这些matching中会有交叉的情况，这些是不可能的。所以接下来一个很自然的题目就是计算noncrossing perfect matching的数目。对于一个完全图来说，这个数目和所谓[Catalan number](http://en.wikipedia.org/wiki/Catalan_number)有关。这个数可以和Fibonacci数一样，用一个递归的方法来定义：
{% highlight julia %}
@memoize function my_catalan(n)
    if (n == 0 || n == 1)
        return 1
    end
    res = 0
    for i = 1:n
        res += my_catalan(i-1)*my_catalan(n-i)
    end
    res
end
{% endhighlight %}
如上所示。这里的函数定义前面出现了本次水文的主角 -- `@memoize` -- 一个Julia的宏，能够实现函数的[memoization](http://en.wikipedia.org/wiki/memoization)。这个东西看名字好像和记忆有点关系，实际上也是这么回事，就是弄个数据结构，把已经算过的函数变量及其对应的结果给记录下来。不然每次运行上面这个`my_catalan`函数的时候，`my_catalan(i)`($i < n$)都会被运行很多次。有了memoization，这就不是个问题了。可以用Julia的`macroexpand`看看`memoize`这个宏到底干了啥：

{% highlight julia %}
julia> macroexpand (:(@memoize function my_catalan(n)
           if (n == 0 || n == 1)
               return 1
           end
           res = 0
           for i = 1:n
               res += my_catalan(i-1)*my_catalan(n-i)
           end
           res
       end))
quote  # /home/filius/.julia/Memoize/src/Memoize.jl, line 92:
    function my_catalan_unmemoized(n) # none, line 2:
        if n == 0 || n == 1 # line 3:
            return 1
        end # line 5:
        res = 0 # line 6:
        for i = 1:n # line 7:
            res += my_catalan(i - 1) * my_catalan(n - i)
        end # line 9:
        res
    end # line 93:
    #128#isdef = false # line 94:
    try  # line 95:
        my_catalan # line 96:
        #128#isdef = true
    end # line 98:
    if #128#isdef # line 99:
        for #129#i = 1 # line 100:
            begin  # /home/filius/.julia/Memoize/src/Memoize.jl, line 101:
                local fcache # line 102:
                const fcache = ObjectIdDict() # line 103:
                my_catalan(n) = begin  # /home/filius/.julia/Memoize/src/Memoize.jl, line 103:
                        if haskey(fcache,(n,))
                            fcache[(n,)]
                        else
                            fcache[(n,)] = my_catalan_unmemoized(n)
                        end
                    end
            end
        end
    else  # line 109:
        begin  # /home/filius/.julia/Memoize/src/Memoize.jl, line 110:
            const my_catalan = let  # line 111:
                        local fcache, my_catalan # line 112:
                        const fcache = ObjectIdDict() # line 113:
                        my_catalan(n) = begin  # /home/filius/.julia/Memoize/src/Memoize.jl, line 113:
                                if haskey(fcache,(n,))
                                    fcache[(n,)]
                                else
                                    fcache[(n,)] = my_catalan_unmemoized(n)
                                end
                            end
                    end
        end
    end
end
{% end highlight %}
看起来好复杂。。不过仔细看一下，其实他也就是用了个`ObjectIdDict`存储了已经运算过的结果而已（。。虽然说起来简单不过我还是写不出来。。）

相比之下，计算一段RNA中所有可能的noncrossing perfect matching的数目要更复杂一些，这个是我的十分不优美的实现。。
{% gist yitapi/2e533765691c69f44737 %}

-------------------------------------------------------------------------------

这回的经验教训：

1.  学而不思则罔- -，光搜不想则囧。
2.  memoization是个好东西，很多dynamic programming问题有了它，实现起来都变得方便多了。不知道以前怎么没注意到，现在才发现很多的语言都有memoization的库。

另外今天又折腾了半天emacs的中文输入，结果还是没搞定。。真悲伤。
