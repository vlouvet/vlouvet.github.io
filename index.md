---
layout: default
title: Dynamic Links Example
---

<link rel="shortcut icon" type="image/png" href="/docs/assets/favicon.ico">

# Dynamic Links to Posts

Here are some dynamically generated links to posts:

<ul>
{% for post in site.posts %}
  <li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
