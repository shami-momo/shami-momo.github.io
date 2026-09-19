---
layout: default
---

<section class="intro">
  <div class="intro-text">
    <h1>hojun<span class="intro-dot">.</span>yoo</h1>
    <p>
      반갑습니다, 유호준입니다. <a href="{{ '/about/' | relative_url }}">항공우주공학</a>을 공부하고 있습니다.
    </p>

    <p>
      궤도역학과 우주추진에 관심이 있습니다. 이곳저곳 <a href="{{ '/travel/' | relative_url }}">여행</a> 다니며 <a href="{{ '/photos/' | relative_url }}">사진</a> 찍는 것을 좋아합니다. 라이브를 보러 가기도.
    </p>

    <p class="contact">
      <span class="contact-label">Contact</span>
      <a href="mailto:jayyoo2002@snu.ac.kr">jayyoo2002@snu.ac.kr</a>
    </p>
  </div>
  
  <img
    class="profile-photo"
    src="{{ '/assets/images/profile_pic.webp' | relative_url }}"
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
