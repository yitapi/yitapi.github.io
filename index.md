---
layout: page
title: YAYB
tagline: Yet another y blog
---
{% include JB/setup %}

### Recent Posts

<ul>
  {% for post in site.posts limit: 5 %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

