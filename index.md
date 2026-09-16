---
layout: default
---

<section class="intro">
  <div class="intro-text">
    <h1>Hojun Yoo</h1>

    <p class="lead">
      반갑습니다, 항공우주공학을 공부하는 <b><a href="{{ '/introduction/' | relative_url }}">유호준</a></b>입니다.
    </p>

    <p>
      공부한 내용들과 진행한 프로젝트, 그리고 여행과 사진을 좋아하여 관련 아카이브도 함께 모아두었습니다.
    </p>

    <p class="contact">
      <strong>Contact</strong>
      jayyoo2002 [at] snu.ac.kr
    </p>
  </div>

  <img
    class="profile-photo"
    src="{{ '/assets/images/profilepic.webp' | relative_url }}"
    alt="유호준 프로필 사진"
    width="180"
    height="180"
    decoding="async"
  >
</section>

<section class="section">
  <div class="section-header">
    <h2>최근 글</h2>

    <a class="section-more" href="{{ '/posts/' | relative_url }}">
      더보기 →
    </a>
  </div>

  <ol class="post-list">
    {% for post in site.posts limit:3 %}
      <li>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        <time datetime="{{ post.date | date_to_xmlschema }}">
          {{ post.date | date: "%Y.%m.%d" }}
        </time>
      </li>
    {% endfor %}
  </ol>
</section>