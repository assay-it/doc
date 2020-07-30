---
layout: default
title: blog
permalink: /blog
search_exclude: true
nav_order: 1
---

# Engineering Blog 

<ul class="posts">
   {% for post in site.posts %}
      <li>
        <span>{{ post.date | date_to_string }}</span> &raquo;
        <a href="{{ post.url }}">{{ post.title }}</a></li>
   {% endfor %}
</ul>