---
layout: page
title: Links
description: 没有链接的博客是孤独的
keywords: 友情链接
comments: true
menu: 链接
permalink: /links/
---

没有链接的博客是孤独的，

如果你也有一个有趣的博客，不妨在评论区留下你的链接，一起分享彼此的创作。

> 友情站点（不分先后）：

<ul>
{% for link in site.data.links %}
  {% if link.src == 'blog' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>
