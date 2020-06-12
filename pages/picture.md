---
layout: page
title: Picture
description: 人越学越觉得自己无知
keywords: picture
comments: false
menu: 相册
permalink: /picture/
---

> 记多少命令和快捷键会让脑袋爆炸呢？

<ul class="listing">
{% for picture in site.picture %}
{% if picture.title != "Picture Template" and picture.topmost == true %}
<li class="listing-item"><a href="{{ site.url }}{{ picture.url }}"><span class="top-most-flag"></span>{{ picture.title }}</a></li>
{% endif %}
{% endfor %}
{% for picture in site.picture %}
{% if picture.title != "Picture Template" and picture.topmost != true %}
<li class="listing-item"><a href="{{ site.url }}{{ picture.url }}">{{ picture.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
