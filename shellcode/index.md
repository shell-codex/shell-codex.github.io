---
layout: default
title: Shellcode - Shellcodex
permalink: /shellcode/
---

# Shellcode

> **Important:** This index contains examples of Assembly and its corresponding shellcode. It is strictly for **Educational Purposes ONLY**!

{% assign shellcodes = site.shellcode %}
{% assign grouped_by_os = shellcodes | group_by: "os" %}

{% for os_group in grouped_by_os %}
  <h2>{{ os_group.name }}</h2>

  {% assign os_shellcodes = os_group.items %}
  {% assign grouped_by_arch = os_shellcodes | group_by: "arch" %}

  {% for arch_group in grouped_by_arch %}
  <h3>{{ arch_group.name }}</h3>
  <ul>
  {% for sc in arch_group.items %}
  <li><a href="{{ sc.url | relative_url }}">{{ sc.title }}</a> <span class="muted">[{{ sc.length }} bytes]</span></li>
  {% endfor %}
  </ul>
  {% endfor %}
{% endfor %}