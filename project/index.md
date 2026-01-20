---
layout: page
title: Project
description: >
  프로젝트 경험 및 개발기 (Work & Toy)
slug: project
lastmod: 2026-01-20 12:52:00 +0900
sitemap:
  changefreq: weekly
---

## Work Experience

{% for post in site.categories.work %}
* [{{ post.title }}]({{ post.url }})
{% endfor %}

## Toy Project

{% for post in site.categories.project %}
* [{{ post.title }}]({{ post.url }})
{% endfor %}
