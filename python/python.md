---
layout: page
title: python
permalink: /python/
---

{% for post in site.categories[python] %}
    <a href="{{ post.url | absolute_url }}">
      {{ post.title }}
    </a>
{% endfor %}
