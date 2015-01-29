---
layout: page
title: Archives
---

  {% for post in site.posts %}
  <div class="archive">
    <h1 class="archive-title">
      <a href="{{ post.url }}">
        {{ post.title }}        
      </a>
      <p class="archive-date">{{ post.date | date_to_string }}</p>
    </h1>
  </div>

  {% endfor %}
