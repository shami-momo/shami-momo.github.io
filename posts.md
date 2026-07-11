---
layout: page
title: "Posts"
subtitle: "공부하면서 정리한 글들을 모아둡니다."
eyebrow: "Study Archive"
permalink: /Posts/
---

<ol class="post-list large">
{% for post in site.posts %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <time datetime="{{ post.date | date_to_xmlschema }}">
      {{ post.date | date: "%Y.%m.%d" }}
    </time>
  </li>
{% endfor %}
</ol>