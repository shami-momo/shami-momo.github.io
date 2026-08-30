---
layout: page
title: "Travel"
subtitle: "지금까지 갔던 곳들을 한눈에 돌아봅니다."
permalink: /travel/
---

## 지금까지 <span id="totalTravelDays">-</span> 동안 여행했어요.

<div class="chart-wrap">
  <canvas id="travelDaysChart"></canvas>
</div>

## 세계의 <span id="visitedCountryCount">-</span>를 여행했어요.
<figure>
  <div class="map-wrap">
    {% include world-map.svg %}
  </div>

  <figcaption>
    Map adapted from
    <a href="https://simplemaps.com/resources/svg-world">Simplemaps</a>.
  </figcaption>
</figure>

## 일본의 <span id="visitedPrefectureCount">-</span>을 여행했어요.

<figure>
  <div class="map-wrap">
    {% include japan-map.svg %}
  </div>

  <figcaption>
    <div class="keikenchi-summary">
      <span class="keikenchi-label">경현치・</span>
      <span class="keikenchi-value" id="keikenchi">-</span>
    </div>
    Map adapted from
    <a href="https://github.com/Snack-X/keikenchi">Snack-X/keikenchi</a>.
  </figcaption>
</figure>

## 이런 여행들을 했어요.
<div id="travelTimelineShell" class="travel-timeline-shell">
  <div id="travelTimeline" class="travel-timeline"></div>

  <div class="travel-timeline-overlay">
    <button id="travelTimelineToggle" class="travel-timeline-toggle" type="button">
      전체 여행 연표 펼치기
    </button>
  </div>
</div>

<div id="travelPopup" class="travel-popup" hidden>
  <div class="travel-popup-backdrop" data-popup-close></div>

  <section
    class="travel-popup-panel"
    role="dialog"
    aria-modal="true"
    aria-labelledby="travelPopupTitle"
  >
    <header class="travel-popup-header">
      <div>
        <h2 id="travelPopupTitle">여행 목록</h2>
      </div>

      <button id="travelPopupClose" class="travel-popup-close" type="button" aria-label="팝업 닫기" data-popup-close>
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-x-icon lucide-x"><path d="M18 6 6 18"/><path d="m6 6 12 12"/></svg>
      </button>
    </header>

    <div id="travelPopupList" class="travel-popup-list"></div>

  </section>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
  // Configuration
  const trips = {{ site.data.travel.trips | jsonify }};
  const DAY = 24 * 60 * 60 * 1000;
  const TIMELINE_INITIAL_COUNT = 5;

  let timelineExpanded = false;

  // Shared utilities
  const $ = (selector) => document.querySelector(selector);
  const $$ = (selector) => [...document.querySelectorAll(selector)];

  function cssVar(name) {
    return getComputedStyle(document.documentElement)
      .getPropertyValue(name)
      .trim();
  }

  function date(dateString) {
    const [y, m, d] = dateString.split("-").map(Number);
    return new Date(Date.UTC(y, m - 1, d));
  }

  function sortByStart(items) {
    return [...items].sort((a, b) => date(b.start) - date(a.start));
  }

  function uniqueFrom(key) {
    return [...new Set(trips.flatMap((trip) => trip[key] ?? []))];
  }

  // Prefecture & statistics
  const PREFECTURE_LEVELS = [
    { key: "prefectures_passed", score: 1, mapClass: "is-passed" },
    { key: "prefectures_landed", score: 2, mapClass: "is-landed" },
    { key: "prefectures_walked", score: 3, mapClass: "is-walked" },
    { key: "prefectures", score: 4, mapClass: "is-stayed" },
    { key: "prefectures_lived", score: 5, mapClass: "is-stayed" },
  ];

  function getPrefectureLevels() {
    const levels = new Map();

    trips.forEach((trip) => {
      PREFECTURE_LEVELS.forEach((level) => {
        (trip[level.key] ?? []).forEach((prefecture) => {
          const current = levels.get(prefecture);

          if (!current || level.score > current.score) {
            levels.set(prefecture, level);
          }
        });
      });
    });

    return levels;
  }

  function renderVisitedStats(countries, prefectures) {
    const countryEl = $("#visitedCountryCount");
    const prefectureEl = $("#visitedPrefectureCount");

    if (countryEl) countryEl.textContent = `${countries.length}개 나라`;
    if (prefectureEl) prefectureEl.textContent = `${prefectures.length}개 현`;
  }

  function renderKeikenchi(prefectureLevels) {
    const total = [...prefectureLevels.values()]
      .reduce((sum, level) => sum + level.score, 0);
    const target = $("#keikenchi");

    if (target) target.textContent = `${total}점`;
  }

  // Travel chart
  function getYearlyDays() {
    const yearly = {};

    trips.forEach((trip) => {
      const end = date(trip.end);

      for (let current = date(trip.start); current <= end; current = new Date(+current + DAY)) {
        const year = current.getUTCFullYear();
        yearly[year] = (yearly[year] ?? 0) + 1;
      }
    });

    return Object.entries(yearly)
      .map(([year, days]) => ({ year, days }))
      .sort((a, b) => Number(a.year) - Number(b.year));
  }

  function renderChart() {
    const canvas = $("#travelDaysChart");
    if (!canvas || typeof Chart === "undefined") return;

    const yearly = getYearlyDays();
    const labels = yearly.map((item) => item.year);
    const data = yearly.map((item) => item.days);
    const total = data.reduce((sum, value) => sum + value, 0);
    const font = getComputedStyle(document.body).fontFamily;
    const totalEl = $("#totalTravelDays");

    if (totalEl) totalEl.textContent = `${total}일`;

    new Chart(canvas, {
      type: "line",
      data: {
        labels,
        datasets: [{
          label: "여행일수",
          data,
          fill: true,
          backgroundColor: cssVar("--highlight-soft"),
          borderColor: cssVar("--main"),
          borderWidth: 3,
          tension: 0.35,
          pointRadius: 2,
          pointHoverRadius: 5,
          pointBackgroundColor: cssVar("--main"),
        }],
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { display: false },
          tooltip: {
            titleFont: { family: font },
            bodyFont: { family: font },
            callbacks: { label: (context) => `${context.raw}일` },
            boxPadding: 6,
          },
        },
        scales: {
          x: {
            grid: { display: false },
            ticks: { color: cssVar("--muted"), font: { family: font, weight: "bold" } },
          },
          y: {
            beginAtZero: true,
            grid: { display: false },
            ticks: { color: cssVar("--muted"), stepSize: 10, font: { family: font } },
          },
        },
      },
    });
  }

  // Travel timeline
  function daysBetween(start, end) {
    return Math.round((date(end) - date(start)) / DAY) + 1;
  }

  function formatTripMonth(trip) {
    const [year, month] = trip.start.split("-");
    return `${year}년 ${Number(month)}월`;
  }

  function formatTripCount(trip) {
    const days = daysBetween(trip.start, trip.end);
    return trip.count ?? `${days - 1}박 ${days}일`;
  }

  function createTravelCard(trip) {
    const content = `
      <div class="travel-card-body">
        <div class="travel-card-meta">
          <span>${formatTripMonth(trip)}</span>
          <span>・</span>
          <span class="travel-card-count">${formatTripCount(trip)}</span>
        </div>
        <h3>${trip.title} <span class="travel-card-icon">${trip.emoji ?? "✈️"}</span></h3>
        <p>${trip.description ?? ""}</p>
      </div>`;

    return trip.url
      ? `<a class="travel-card" href="${trip.url}">${content}</a>`
      : `<article class="travel-card is-unlinked">${content}</article>`;
  }

  function renderTimeline() {
    const root = $("#travelTimeline");
    const shell = $("#travelTimelineShell");
    const toggle = $("#travelTimelineToggle");
    if (!root) return;

    const sortedTrips = sortByStart(trips);
    root.innerHTML = sortedTrips.map(createTravelCard).join("");
    shell?.classList.toggle("is-expanded", timelineExpanded);
    if (!toggle) return;

    toggle.hidden = sortedTrips.length <= TIMELINE_INITIAL_COUNT;
    toggle.setAttribute("aria-label", `전체 여행 연표 ${timelineExpanded ? "접기" : "펼치기"}`);

    const path = timelineExpanded ? "m18 15-6-6-6 6" : "m6 9 6 6 6-6";
    toggle.innerHTML = `<svg aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="${path}"/></svg>`;
  }

  function setupTimelineToggle() {
    const toggle = $("#travelTimelineToggle");
    if (!toggle) return;

    toggle.addEventListener("click", () => {
      timelineExpanded = !timelineExpanded;
      renderTimeline();
      
      if (!timelineExpanded) { $("#travelTimeline")?.scrollIntoView({ behavior: "smooth", block: "start" }); }
    });
  }

  // Area popup
  function getAreaLabel(value, el) {
    return el.dataset.name || el.getAttribute("name") || el.getAttribute("aria-label") || el.querySelector("title")?.textContent?.trim() || value;
  }

  function getTripsByArea(type, value) {
    const keys = type === "country"
      ? ["countries"]
      : ["prefectures", "prefectures_walked"];

    return sortByStart(trips.filter((trip) => keys.some((key) => trip[key]?.includes(value))));
  }

  function openTravelPopup(type, value, label) {
    const popup = $("#travelPopup");
    const title = $("#travelPopupTitle");
    const list = $("#travelPopupList");
    const close = $("#travelPopupClose");
    if (!popup || !title || !list) return;

    const matchedTrips = getTripsByArea(type, value);
    if (!matchedTrips.length) return;

    title.textContent = label;
    list.innerHTML = matchedTrips.map(createTravelCard).join("");
    popup.hidden = false;
    document.body.classList.add("is-popup-open");
    close?.focus();
  }

  function closeTravelPopup() {
    const popup = $("#travelPopup");
    if (!popup) return;

    popup.hidden = true;
    document.body.classList.remove("is-popup-open");
  }

  function setupTravelPopup() {
    const popup = $("#travelPopup");
    if (!popup) return;

    popup.addEventListener("click", (event) => {
      if (event.target.closest("[data-popup-close]")) closeTravelPopup();
    });

    document.addEventListener("keydown", (event) => {
      if (event.key === "Escape" && !popup.hidden) closeTravelPopup();
    });
  }

  // Map rendering and interaction
  function forEachMapArea(values, selectorFactory, callback) {
    for (const value of values) {
      const els = $$(selectorFactory(value));

      if (!els.length) console.warn("Map area not found:", value);
      els.forEach((el) => callback(el, value));
    }
  }

  function paintMap(values, selectorFactory) {
    forEachMapArea(values, selectorFactory, (el) => el.classList.add("visited"));
  }

  function paintPrefectureMap(prefectureLevels) {
    forEachMapArea(
      prefectureLevels,
      ([prefecture]) => `.japan-map .prefecture[data-name="${prefecture}"]`,
      (el, [, level]) => {
        el.classList.add(level.mapClass);
        if (level.score >= 3) el.classList.add("visited");
      }
    );
  }

  function setupMapAreaClicks(values, selectorFactory, type) {
    forEachMapArea(values, selectorFactory, (el, value) => {
      const label = getAreaLabel(value, el);
      const open = () => openTravelPopup(type, value, label);

      el.classList.add("is-clickable");
      el.setAttribute("tabindex", "0");
      el.setAttribute("role", "button");
      el.setAttribute("aria-label", `${label} 여행 보기`);
      el.addEventListener("click", open);
      el.addEventListener("keydown", (event) => {
        if (event.key === "Enter" || event.key === " ") {
          event.preventDefault();
          open();
        }
      });
    });
  }

  // Initialization
  const countries = uniqueFrom("countries");
  const prefectureLevels = getPrefectureLevels();
  const clickablePrefectures = [...prefectureLevels]
    .filter(([, level]) => level.score >= 3)
    .map(([prefecture]) => prefecture);

  paintMap(countries, (country) => `.world-map [id="${country}"]`);
  paintPrefectureMap([...prefectureLevels]);

  setupMapAreaClicks(countries, (country) => `.world-map [id="${country}"]`, "country");
  setupMapAreaClicks(clickablePrefectures, (prefecture) => `.japan-map .prefecture[data-name="${prefecture}"]`, "prefecture");

  renderVisitedStats(countries, clickablePrefectures);
  renderKeikenchi(prefectureLevels);
  renderChart();
  renderTimeline();
  setupTimelineToggle();
  setupTravelPopup();
</script>