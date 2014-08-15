---
layout: page
title: YAYB
tagline: Yet another y blog
---
{% include JB/setup %}


{% for post in site.posts %}
{% include JB/posts_collate %}
{% endfor %}

