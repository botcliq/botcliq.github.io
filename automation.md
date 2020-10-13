---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: page
title: Automation
---

New Updates:

{% for themes in site.automation %}

<h2><a href="{{ themes.url }}">
  {{ themes.title }}</a></h2>

<p class="post-excerpt">{{ themes.description | truncate: 160 }}</p>

{% endfor %}
