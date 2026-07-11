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
    <a href="{{ '/travel/' | relative_url }}">여행</a> 다니며 <a href="https://www.instagram.com/yyuuu.sei/">사진</a> 찍는 것을 좋아합니다. 이곳은 공부하면서 정리한 내용을 모아두거나 이러저러 하고 싶은 말들을 적는 곳입니다.
  </p>

  <p>
    <b>Contact via</b> <a href="mailto:jayyoo2002@snu.ac.kr">jayyoo2002@snu.ac.kr</a>
  </p>

  <div class="links">
    <a href="{{ '/snu/' | relative_url }}">SNU</a>
    <a href="{{ '/posts/' | relative_url }}">Posts</a>
    <a href="{{ '/travel/' | relative_url }}">Travel</a>
  </div>
  </div>

  <img
    class="profile-photo"
    src="{{ '/assets/images/profile.webp' | relative_url }}"
  >
</section>

<section class="section">
  <h2>Recent posts</h2>

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