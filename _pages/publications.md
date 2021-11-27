---
layout: page
permalink: /publications/
title: publications
description: publications in reversed chronological order. They say it's good to have clear goals, one of mine is to start filling this page up with some good quality research!
years: [2021]
nav: true
---

<div class="publications">
{% for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}
</div>
