---
layout: default
title: Blog - Shellcodex
permalink: /blog/
---

# Posts

<ul class="posts-list">
{% for post in site.posts %}
  <li class="post-item">
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a> - <span class="muted">{{ post.date | date: "%d-%m-%Y" }}</span> - <em>{{ post.categories | join: ", " }}</em>
  </li>
{% endfor %}
</ul>
