---
layout: page
title: "Travel"
subtitle: "지금까지 갔던 곳들을 한눈에 돌아봅니다."
permalink: /travel/
---

자세한 여행기는 <a href="https://sei-and-the-world.tistory.com/">티스토리</a>를 방문해주세요.

## 세계
<figure>
  <div class="map-wrap">
    {% include world-map.svg %}
  </div>

  <figcaption>
    Map adapted from
    <a href="https://simplemaps.com/resources/svg-world">Simplemaps</a>.
  </figcaption>
</figure>

## 일본
<figure>
  <div class="map-wrap">
    {% include japan-map.svg %}
  </div>

  <figcaption>
    Map adapted from
    <a href="https://github.com/Snack-X/keikenchi">Snack-X/keikenchi</a>.
  </figcaption>
</figure>

<script>
  const visitedCountries = [
    {% for country in site.data.visited.world.countries %}
      "{{ country }}"{% unless forloop.last %},{% endunless %}
    {% endfor %}
  ];

  const visitedPrefectures = [
    {% for pref in site.data.visited.japan.prefectures %}
      "{{ pref }}"{% unless forloop.last %},{% endunless %}
    {% endfor %}
  ];

  for (const country of visitedCountries) {
    const els = document.querySelectorAll(`.world-map [id="${country}"]`);

    if (els.length === 0) {
        console.warn("Country not found:", country);
    }

    els.forEach((el) => {
        el.classList.add("visited");
    });
    }

    for (const pref of visitedPrefectures) {
    const els = document.querySelectorAll(`.japan-map .prefecture[data-name="${pref}"]`);

    if (els.length === 0) {
        console.warn("Prefecture not found:", pref);
    }

    els.forEach((el) => {
        el.classList.add("visited");
    });
  }
</script>