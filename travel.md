---
layout: page
title: "Travel"
subtitle: "지금까지 갔던 곳들. 여행기는 다른 곳에 있어요."
permalink: /travel/
---

## 세계
<div class="map-wrap">
  {% include world-map.svg %}
</div>

## 일본
<div class="map-wrap">
  {% include japan-map.svg %}
</div>

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