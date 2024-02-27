---
layout: default
title: Vlouvet3 Technical Blog
---

<link rel="shortcut icon" type="image/png" href="/docs/assets/favicon.ico">

# Dynamic Links to Posts

Here are some dynamically generated links to posts:

<ul>
{% for post in site.posts %}
  <li><a href="{{ post.url }}">{{post.date | date_to_string}}-{{ post.title }}</a></li>
{% endfor %}
</ul>
