---
layout: page
title: YAYB
tagline: Yet another y blog
---
{% include JB/setup %}

### Recent Posts

<ul>
  {% for post in site.posts limit: 5 %}
    <li><span>{{ post.date | date: "%-m/%-d/%y" }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
