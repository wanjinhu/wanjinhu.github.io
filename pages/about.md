---
layout: page
title: About
description: 承担责任，追求价值，寻找人生意义。
keywords: Wanjin Hu, 胡万金
comments: true
menu: 关于
permalink: /about/
---

My name is Wanjin Hu (胡万金) and I am currently working as a researcher at Dipro MIM, and my research direction is the relationship between human health and microorganisms, especially focusing on the microorganisms in the gut. I used to work at Majorbio as a bioinformatics engineer, focus on the development of bioinformatics technology in microbial research, such as metagenomics, bacterial genomics and prokaryotic transcriptomics, etc.

我是一个存在主义者，喜欢思考人生，喜欢思考世界，喜欢思考未来。

如有任何问题可以联系我，或者在下面留言。

## 关于我

<ul>
{% for website in site.data.social %}
<li>{{website.sitename }}：<a href="{{ website.url }}" target="_blank">@{{ website.name }}</a></li>
{% endfor %}
</ul>
