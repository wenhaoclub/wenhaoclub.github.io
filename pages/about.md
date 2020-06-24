---
layout: page
title: About
description: 互联网IT界的搬运工
keywords: wenhao,fuwenhao.club,文浩
comments: true
menu: 关于
permalink: /about/
---

我是文浩，他注定是个低调的大侠，所以什么都没写……🤣

互联网IT界的搬运工。


## 联系
<ul>
{% for website in site.data.social %}
<li>{{website.sitename }}：<a href="{{ website.url }}" target="_blank">@{{ website.name }}</a></li>
{% endfor %}
<li>
微信公众号：<a href="https://s1.ax1x.com/2020/06/04/tBkyU1.jpg" target="_blank">似水似流年</a><br />
<img style="height:192px;width:192px;border:1px solid lightgrey;" src="https://s1.ax1x.com/2020/06/04/tBkyU1.jpg" alt="似水似流年" />
</li>
</ul>


## Skill Keywords

{% for skill in site.data.skills %}
### {{ skill.name }}
<div class="btn-inline">
{% for keyword in skill.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
