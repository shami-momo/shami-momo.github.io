---
layout: default
---

<section class="intro">
  <div class="intro-text">
    <h1>Hojun Yoo</h1>

    <p class="lead">
      <b><a href="{{ '/snu/' | relative_url }}">항공우주공학</a></b>을 공부하는 학생입니다.
    </p>

    <p>
      공부하며 정리한 글과 진행한 프로젝트를 기록합니다. 여행하며 찍은 사진과 개인적인 아카이브도 함께 모아둡니다.
    </p>

    <p class="contact">
      <strong>이메일</strong>
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
  <a class="archive-link" href="{{ '/snu/' | relative_url }}">
    <span>
      <strong>소개</strong>
      <span>경력과 관심사</span>
    </span>
    <span aria-hidden="true">→</span>
  </a>

  <a class="archive-link" href="{{ '/travel/' | relative_url }}">
    <span>
      <strong>여행</strong>
      <span>지도와 목록</span>
    </span>
    <span aria-hidden="true">→</span>
  </a>

  <a class="archive-link" href="https://www.instagram.com/yyuuu.sei" aria-label="사진 Instagram 열기">
    <span>
      <strong>사진</strong>
      <span>인스타그램</span>
    </span>
    <span aria-hidden="true">→</span>
  </a>
</section>


<section class="section">
  <div class="section-header">
    <h2>대표 글</h2>
  </div>

  <a class="featured-project" href="{{ '/posts/2002AA29/' | relative_url }}">
    <img
      src="{{ '/assets/images/posts/2026-09-07-2002AA29/aa29_approach.webp' | relative_url }}"
      alt="지구와 2002 AA29의 장기 상대 거리 변화 그래프"
      width="1246"
      height="1229"
      loading="lazy"
      decoding="async"
    >
    <span class="featured-project-body">
      <span class="featured-project-kicker">궤도역학</span>
      <strong>2002 AA29 분석 및 탐사 궤적 설계</strong>
      <span>장기 동역학적 거동 분석・지구 출발 – 소행성 도착 임무 궤적 설계</span>
      <span class="featured-project-more">읽기 →</span>
    </span>
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