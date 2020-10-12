---
layout: page
title: python
---

New Updates:

{% for themes in site.python %}

<h2><a href="{{ themes.url }}">
  {{ themes.title }}</a></h2>

<p class="post-excerpt">{{ themes.description | truncate: 160 }}</p>

{% endfor %}
