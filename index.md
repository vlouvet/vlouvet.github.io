---
layout: default
title: Dynamic Links Example
---

# Dynamic Links to Posts

Here are some dynamically generated links to posts:

![Let it flow!](/docs/assets/flow.jpg "Flow")

<ul>
{% for post in site.posts %}
  <li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
