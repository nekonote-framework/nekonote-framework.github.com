---
permalink: /blog/index
title: Blog
layout: article
---
<dl class="blog-list">
  {% for post in site.posts %}
    <dt>{{ post.date | date: "%a, %b %d, %Y" }}</dt>
    <dd>
      <a href="{{ post.url | append: '.html' }}">{{ post.title }}</a>
    </dd>
    <hr />
  {% endfor %}
</dl>
