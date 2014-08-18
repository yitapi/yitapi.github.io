---
layout: post
title: "Rosalind with Julia - 2"
description: ""
category: 技术
tags: [bioinformatics, julia, rosalind]
---
{% include JB/setup %}
这两天还是有好好学习的- -昨天吃到太晚所以来不及写了，今天倒是比较认真的做了几个稍微复杂一点的问题。下边从简单到复杂来说。

第一个问题是给两段等长的DNA序列，数出其中突变的碱基数目。这个问题等价于求两个`String`的[Hamming distance](http://en.wikipedia.org/wiki/Hamming_distance)。虽然[JuliaStats](https://github.com/JuliaStats)里面有个`Distance`包提供了`Hamming`函数。不过好像并不支持`String`类型的参数。
说了这么多，其实也就是一句话的问题。。
{% highlight julia %}
countnz([ x - y for (x,y) in zip(a, b)])
{% endhighlight %}
这里面`a`和`b`分别是两段DNA序列。不是太清楚这句里面`zip`函数是不是必要，不过这样能工作。。（有空要研究一下这个。。）
<br><br>
第二个问题也比较简单，就是在一段DNA里面找motif，输出所有motif在DNA中的起始位置。比如
	GATATATGCATATACTT
中，motif`ATAT`就在第2、4、10三处都出现了（中间有overlap）。Julia里面提供了`searchindex`函数，刚好就是搜motif的，只不过它只给出第一个，所以需要写一个循环。
{% highlight julia %}
no = Int32[]
a = searchindex(s, t)
while a != 0
  push!(no, a)
  a = searchindex(s, t, a+1)
end
repr(no)
{% endhighlight %}
这里`searchindex`的第三个参数是指开始搜索的位置，所以一旦搜索到一次motif，下一个就从该motif位置的下一处开始搜索。
另一个地方，也是今天才学到的，就是数组类型的指定。Julia允许在代码中指定变量的类型，而一个`Int32`类型的数组就可以用`Int32[]`表示，后面还用到了更复杂的数组类型。
<br><br>
下一个问题是在一个多序列的FASTA文件中，找出GC含量 [GC-content](http://en.wikipedia.org/wiki/GC-content)最高的那一个。基因组中GC含量高的区域，一般有可能有基因存在。这个问题需要先把FASTA文件读取出来，将序列和序列名称储存在一个恰当的数据结构里面，这里我用了`Dict`类型。
{% highlight julia %}
# read in DNAs
function readdna(fasta)
  x = open(fasta, "r")
  dnas = Dict()
  key = ""
  dna = ""
  while (!eof(x))
    a = chomp(readline(x))
    if (a[1]=='>')
      if (key != "")
        dnas[key] = dna
      end
      key = a[2:end]
      dna = ""
    else
      dna *= a
    end
  end
  dnas[key]=dna
  return dnas
end
{% endhighlight %}
这样得到的`dnas`是一个以序列名称为key，序列为value的字典。找到其中GC含量最高的序列就比较简单了。
{% highlight julia %}
dnas = readdna("dna4.txt")
# calculate GC contents
max = ""
gcm = 0.0
for dna in keys(dnas)
  gc = sum([(x == 'C' || x == 'G')? 1 : 0 for x in dnas[dna]]) / length(dnas[dna])
  if gc > gcm
    gcm = gc
    max = dna
  end
end

# output dna with max GC contents (in % unit)
println(max)
println(gcm*100)
{% endhighlight %}
这个实现可能比较低效。。因为我又用了list comprehension，而对于这个问题其实是不需要的。不过反正能work。
<br><br>
最后再说一个稍微复杂一些的，即找内切酶位点 [Ristriction site](http://en.wikipedia.org/wiki/Restriction_site)。最近稍微做了一点实验看了点分子克隆之后，发现内切酶这个东西很神奇，它们切割的位点都是所谓的回文序列（比如`ATGCAT`) [Palindromic sequence](http://en.wikipedia.org/wiki/Palindromic_sequence)。这个问题就是在一段DNA中找到所有长度为4-12的回文序列，输出它们的起始位置和编号。
这个问题应该是很经典的，我自己的算法估计比较低效，是在不同的地方把序列切开“对折”一下，然后看看切开的两边有无相同的序列。（回去要看看书上有没有高效实现）这个我就照旧贴gist了。
{% gist yitapi/af35993854165c0782ed %}
这里面用到了好几个之前已经出现过的函数。。比如`readdna`，又比如`rc`求一段DNA的反向互补序列。
<br><br>
OK，今天就这样，其实顺手解决了几个其他问题，不过还不是很系统，所以下次再来说= =。

P.S. 昨天在Rosalind上发现一个很不熟的本科同学，好厉害做了一百多道题。。然后手贱去搜了一下人家的publication。。然后。。然后就没有然后了。。
