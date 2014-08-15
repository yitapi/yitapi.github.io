---
layout: page
title: 博客测试
tagline: Supporting tagline
---
{% include JB/setup %}

{% highlight julia %}
function hypot(x,y)
  x = abs(x)
  y = abs(y)
  if x > y
    r = y/x
    return x*sqrt(1+r*r)
  end
  if y == 0
    return zero(x)
  end
  r = x/y
  return y*sqrt(1+r*r)
end
{% endhighlight %}

