---
layout: page
title: About
description: 虽然只是223，但是可以冻住你
keywords: 碎雪, Su1_Xu3
comments: true
menu: 关于
permalink: /about/
---

我是碎雪，Su1_Xu3。

虽然只是223，但是可以冻住你。

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
