---
layout: page
permalink: /Publications/
title: Publications
description: publications in reversed chronological order. They say it's good to have clear goals, one of mine is to start filling this page up with some good quality research!
years: [2022, 2021]
nav: true
---

<div class="publications">
{% for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}
</div>
