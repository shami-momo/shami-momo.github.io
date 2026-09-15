---
layout: default
---

<section class="intro">
  <div class="intro-text">
    <h1>Hojun Yoo</h1>

    <p class="lead">
      <b><a href="{{ '/introduction/' | relative_url }}">항공우주공학</a></b>을 공부하는 학생입니다.
    </p>

    <p>
      공부하며 정리한 글과 진행한 프로젝트를 기록합니다. 여행과 사진을 좋아하여 관련 아카이브도 함께 모아둡니다.
    </p>

    <p class="contact">
      <strong>Contact via</strong>
      <a href="mailto:jayyoo2002@snu.ac.kr">jayyoo2002@snu.ac.kr</a>
    </p>
  </div>

  <img
    class="profile-photo"
    src="{{ '/assets/images/profile.webp' | relative_url }}"
    alt="유호준 프로필 사진"
    width="180"
    height="180"
    decoding="async"
  >
</section>

<section class="archive">
  <a class="archive-link" href="{{ '/introduction/' | relative_url }}">
    <span>
      <strong>소개</strong>
    </span>
    <span aria-hidden="true">→</span>
  </a>

  <a class="archive-link" href="{{ '/posts/' | relative_url }}">
    <span>
      <strong>글</strong>
    </span>
    <span aria-hidden="true">→</span>
  </a>

  <a class="archive-link" href="{{ '/travel/' | relative_url }}">
    <span>
      <strong>여행</strong>
    </span>
    <span aria-hidden="true">→</span>
  </a>

  <a class="archive-link" href="{{ '/photos/' | relative_url }}">
    <span>
      <strong>사진</strong>
    </span>
    <span aria-hidden="true">→</span>
  </a>
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