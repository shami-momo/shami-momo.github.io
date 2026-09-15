---
layout: page
title: "글"
permalink: /posts/
---

<ol class="post-list large">
{% for post in site.posts %}
  <li>
    <a class="post-list-entry" href="{{ post.url | relative_url }}">
      <span>{{ post.title }}</span>
      {% if post.tags %}
        <span class="post-list-tags">{{ post.tags | join: ' · ' }}</span>
      {% endif %}
    </a>
    <time datetime="{{ post.date | date_to_xmlschema }}">
      {{ post.date | date: "%Y.%m.%d" }}
    </time>
  </li>
{% endfor %}
</ol>
