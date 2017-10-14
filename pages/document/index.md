---
title: Documentation
permalink: /document/index
layout: article
---

<div class="tiles">
  {% assign cnt = 1 %}
  {% assign total = site.data.document.contents.size %}
  {% assign point = 100 | divided_by: 20 %}
  {% for atcl in site.data.document.contents %}
    {% for post in atcl %}
      {% if post.title %}
        <article class="tile" itemscope itemtype="http://schema.org/Article">
          <a href="{{ post.path }}" class="post-teaser">
            {% assign opacity = point | times: cnt | divided_by: 100.0 %}
            <img src="/images/{{ post.image }}" alt="{{ post.title }}" itemprop="image" style="opacity: {{opacity}};" />
          </a>
          <h2 class="post-title" itemprop="name"><a href="{{ post.path }}">{{post.num}}. {{ post.title }}</a></h2>
        </article><!-- /.tile -->
      {% endif %}
    {% endfor %}
    {% assign cnt = cnt | plus: 1 %}
  {% endfor %}
</div><!-- /.tiles -->
