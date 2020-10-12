---
layout: page
title: python
permalink: /python/
order: 4
---

New Updates:

{% for staff_member in python.python %}
  <p>{{ staff_member.content | markdownify }}</p>
{% endfor %}
