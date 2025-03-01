---
layout: page
title: Projects
permalink: /projects/
description: A growing collection of projects I've carried out.
nav: true
---

<div class="projects grid">
  {% assign sorted_projects = site.projects | sort: "importance" %}
  {% for project in sorted_projects %}
    {% include projects.html %}
  {% endfor %}
</div>
