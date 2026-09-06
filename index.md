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
    공부하면서 개인적으로 정리한 내용들을 씁니다. <b><a href="{{ '/travel/' | relative_url }}">여행</a></b> 다니며 <b><a href="https://www.instagram.com/yyuuu.sei">사진</a></b> 찍는 것을 좋아하기에 관련된 것들을 모아놓기도 합니다.
  </p>

  <p class="contact">
    <b>Contact via</b> <a href="mailto:jayyoo2002@snu.ac.kr">jayyoo2002@snu.ac.kr</a>
  </p>
  </div>

  <img
    class="profile-photo"
    src="{{ '/assets/images/profile.webp' | relative_url }}"
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