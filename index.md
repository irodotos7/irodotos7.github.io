---
layout: default
title: Home
---

# Blog Posts

<ul class="post-list">
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> <small>{{ post.date | date: "%b %-d, %Y" }}</small>
    </li>
  {% endfor %}
</ul>

<!-- To enable the custom CSS, add the following line to your _includes/head.html (for minima theme): -->
<!-- <link rel="stylesheet" href="/assets/css/custom.css"> -->
