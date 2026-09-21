---
layout: page
title: "글"
permalink: /posts/
---

## 목록

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

## 발표・교육 자료
- 지구과학II 별빛너머 모의고사 <a href="https://drive.google.com/file/d/1miJYRRXLxX9d23r0SudA1yvqeBO3qeKQ/view?usp=sharing">PDF</a>
- 하나고등학교 전공탐색의 날 발표자료 <a href="https://drive.google.com/file/d/1YxI8Qz3pJPKpNJxKAVjpwzv356rnGgwl/view?usp=sharing">PDF</a>