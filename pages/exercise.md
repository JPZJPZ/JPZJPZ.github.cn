---
layout: page
title: Exercise
description: 人越学越觉得自己无知
keywords: exercise
comments: false
menu: exercise
permalink: /exercise/
---

> Q & A

<ul class="listing">
{% for exercise in site.exercise %}
{% if exercise.title != "Exercise Template" %}
<li class="listing-item"><a href="{{ exercise.url }}">{{ exercise.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
