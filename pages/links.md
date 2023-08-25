---
layout: page
title: Links
description: 没有链接的博客是孤独的
keywords: 友情链接
comments: true
menu: 链接
permalink: /links/
---

没有链接的博客是孤独的。

> Blog搭建，由衷的感谢

<ul>
{% for link in site.data.links %}
  {% if link.src == 'blog' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>
