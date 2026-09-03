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

<div id="travelList" class="travel-list-root"></div>

<div id="travelPopup" class="travel-popup" hidden>
  <div class="travel-popup-backdrop" data-popup-close></div>
  <section
    class="travel-popup-panel"
    role="dialog"
    aria-modal="true"
    aria-labelledby="travelPopupTitle"
  >
    <header class="travel-popup-header">
      <h2 id="travelPopupTitle">여행 목록</h2>
      <button
        id="travelPopupClose"
        class="travel-popup-close"
        type="button"
        aria-label="팝업 닫기"
        data-popup-close
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
          aria-hidden="true"
        >
          <path d="M18 6 6 18" />
          <path d="m6 6 12 12" />
        </svg>
      </button>
    </header>
    <div id="travelPopupList" class="travel-popup-list"></div>
  </section>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>

  // 데이터와 설정
  const trips = {{ site.data.travel.trips | jsonify }};
  const DAY = 24 * 60 * 60 * 1000;
  const PREFECTURE_LEVELS = [
    { key: "prefectures_passed", score: 1, mapClass: "is-passed" },
    { key: "prefectures_landed", score: 2, mapClass: "is-landed" },
    { key: "prefectures_walked", score: 3, mapClass: "is-walked" },
    { key: "prefectures", score: 4, mapClass: "is-stayed" },
    { key: "prefectures_lived", score: 5, mapClass: "is-stayed" }
  ];
  const PREFECTURE_KEYS = PREFECTURE_LEVELS.map(({ key }) => key);
  const AREA_TYPES = {
    country: {
      tripKeys: ["countries"],
      selector: (value) =>
        `.world-map [data-country="${CSS.escape(value)}"]`
    },
    prefecture: {
      tripKeys: PREFECTURE_KEYS,
      selector: (value) =>
        `.japan-map .prefecture[data-name="${CSS.escape(value)}"]`
    }
  };

  // 상태
  let travelChart = null;

  // DOM 선택
  const $ = (selector) => document.querySelector(selector);
  const $$ = (selector) => [...document.querySelectorAll(selector)];

  // 공통 유틸

  function parseDate(dateString) {
    const [year, month, day] = dateString.split("-").map(Number);
    return new Date(Date.UTC(year, month - 1, day));
  }

  function sortByStart(items) {
    return [...items].sort((a, b) => parseDate(b.start) - parseDate(a.start));
  }

  function getDaysBetween(start, end) {
    return Math.round((parseDate(end) - parseDate(start)) / DAY) + 1;
  }

  function escapeHtml(value) {
    return String(value)
      .replaceAll("&", "&amp;")
      .replaceAll("<", "&lt;")
      .replaceAll(">", "&gt;")
      .replaceAll('"', "&quot;")
      .replaceAll("'", "&#039;");
  }

  // 여행 데이터 계산

  function getUniqueValues(key) {
    return [...new Set(trips.flatMap((trip) => trip[key] ?? []))];
  }

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

  function getYearlyDays() {
    const yearly = {};
    trips.forEach((trip) => {
      const isJapan = trip.countries?.includes("JP");
      const start = parseDate(trip.start);
      const end = parseDate(trip.end);
      for (
        let current = start;
        current <= end;
        current = new Date(+current + DAY)
      ) {
        const year = current.getUTCFullYear();
        const days = yearly[year] ?? { japan: 0, nonJapan: 0 };
        if (isJapan) days.japan += 1;
        else days.nonJapan += 1;
        yearly[year] = days;
      }
    });
    return Object.entries(yearly)
      .map(([year, days]) => ({ year: Number(year), ...days }))
      .sort((a, b) => a.year - b.year);
  }

  const sortedTrips = sortByStart(trips);
  const linkedTrips = sortedTrips.filter(
    (trip) => typeof trip.url === "string" && trip.url.trim()
  );
  const countries = getUniqueValues("countries");
  const prefectureLevels = getPrefectureLevels();
  const visitedPrefectures = [...prefectureLevels]
    .filter(([, level]) => level.score >= 3)
    .map(([prefecture]) => prefecture);

  // 여행 목록

  function formatMonthDay(dateString) {
    const [, month, day] = dateString.split("-").map(Number);

    return `${String(month).padStart(2, "0")}.${String(day).padStart(2, "0")}`;
  }

  function createTripPeriod(trip) {
    const start = formatMonthDay(trip.start);
    const isDayTrip = trip.start === trip.end;

    const secondRow = isDayTrip
      ? '<span class="travel-card-period-value is-note">당일</span>'
      : `
        <span class="travel-card-period-arrow" aria-hidden="true">↓</span>
        <time
          class="travel-card-period-value"
          datetime="${escapeHtml(trip.end)}"
        >${formatMonthDay(trip.end)}</time>
      `;

    return `
      <span class="travel-card-period">
        <time
          class="travel-card-period-value"
          datetime="${escapeHtml(trip.start)}"
        >${start}</time>
        ${secondRow}
      </span>
    `;
  }

  function createTravelCard(trip) {
    const description = trip.description ?? "";
    const descriptionMarkup = description
      ? `<span class="travel-card-description">${escapeHtml(description)}</span>`
      : "";
    const arrowMarkup = trip.url
      ? `
        <svg class="travel-card-arrow" aria-hidden="true"
          viewBox="0 0 24 24" fill="none" stroke="currentColor"
          stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="m9 18 6-6-6-6"/>
        </svg>
      `
      : "";
    const content = `
      ${createTripPeriod(trip)}
      <span class="travel-card-content">
        <span class="travel-card-title">
          ${escapeHtml(trip.title ?? "")}
          <span class="travel-card-icon">${escapeHtml(trip.emoji ?? "✈️")}</span>
        </span>
        ${descriptionMarkup}
      </span>
      ${arrowMarkup}
    `;
    return trip.url
      ? `<a class="travel-card" href="${escapeHtml(trip.url)}" role="listitem">${content}</a>`
      : `<article class="travel-card is-unlinked" role="listitem">${content}</article>`;
  }

  function groupTripsByYear(items) {
    const groups = new Map();
    items.forEach((trip) => {
      const year = trip.start.split("-")[0];
      const yearTrips = groups.get(year) ?? [];
      yearTrips.push(trip);
      groups.set(year, yearTrips);
    });
    return groups;
  }

  function createTravelListMarkup(items) {
    return [...groupTripsByYear(items)]
      .map(
        ([year, yearTrips]) => `
          <section class="travel-year-section" aria-label="${escapeHtml(year)}년 여행">
            <span class="travel-year">${escapeHtml(year)}</span>
            <div class="travel-year-list" role="list">
              ${yearTrips.map(createTravelCard).join("")}
            </div>
          </section>
        `
      )
      .join("");
  }

  // 통계

  function renderVisitedStats() {
    const countryEl = $("#visitedCountryCount");
    const prefectureEl = $("#visitedPrefectureCount");
    if (countryEl) countryEl.textContent = `${countries.length}개 나라`;
    if (prefectureEl) {
      prefectureEl.textContent = `${visitedPrefectures.length}개 현`;
    }
  }

  function renderKeikenchi() {
    const total = [...prefectureLevels.values()]
      .reduce((sum, level) => sum + level.score, 0);
    const target = $("#keikenchi");
    if (target) target.textContent = `${total}점`;
  }

  // 여행 일수 차트

  function renderChart() {
    const chartCanvas = $("#travelDaysChart");
    if (!chartCanvas || !window.Chart) return;
    const yearly = getYearlyDays();
    const labels = yearly.map(({ year }) => year);
    const japanData = yearly.map(({ japan }) => japan);
    const nonJapanData = yearly.map(({ nonJapan }) => nonJapan);
    const total = yearly.reduce(
      (sum, { japan, nonJapan }) => sum + japan + nonJapan,
      0
    );
    const font = getComputedStyle(document.body).fontFamily;
    const totalEl = $("#totalTravelDays");
    if (totalEl) totalEl.textContent = `${total}일`;
    travelChart?.destroy();
    travelChart = new Chart(chartCanvas, {
      type: "bar",
      data: {
        labels,
        datasets: [
          {
            label: "일본",
            data: japanData,
            backgroundColor: cssVar("--main-soft")
          },
          {
            label: "일본 외",
            data: nonJapanData,
            backgroundColor: cssVar("--highlight")
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        interaction: {
          mode: "index",
          intersect: false
        },
        plugins: {
          legend: {
            display: true,
            labels: {
              color: cssVar("--muted"),
              font: {
                family: font,
                size: 13,
                weight: "bold"
              }
            }
          },
          tooltip: {
            titleFont: { family: font },
            bodyFont: { family: font },
            boxPadding: 6,
            callbacks: {
              label: (context) => `${context.dataset.label}: ${context.raw}일`
            }
          }
        },
        scales: {
          x: {
            stacked: true,
            grid: { display: false },
            ticks: {
              color: cssVar("--muted"),
              font: {
                family: font,
                weight: "bold"
              }
            }
          },
          y: {
            beginAtZero: true,
            stacked: true,
            grid: { display: false },
            ticks: {
              color: cssVar("--muted"),
              stepSize: 10,
              font: { family: font }
            }
          }
        }
      }
    });
  }

  // 여행 목록

  function renderTravelList() {
    const root = $("#travelList");
    if (!root) return;

    root.innerHTML = createTravelListMarkup(linkedTrips);
  }

  // 지역별 여행 팝업

  function getTripsByArea(type, value) {
    const { tripKeys } = AREA_TYPES[type];
    return sortedTrips.filter((trip) =>
      tripKeys.some((key) => trip[key]?.includes(value))
    );
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
    list.innerHTML = createTravelListMarkup(matchedTrips);
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
      if (event.target.closest("[data-popup-close]")) {
        closeTravelPopup();
      }
    });
    document.addEventListener("keydown", (event) => {
      if (event.key === "Escape" && !popup.hidden) {
        closeTravelPopup();
      }
    });
  }

  // 지도

  function getAreaLabel(value, element) {
    return element.dataset.name
      || element.getAttribute("name")
      || element.getAttribute("aria-label")
      || element.querySelector("title")?.textContent?.trim()
      || value;
  }

  function getMapAreas(type, value) {
    const elements = $$(AREA_TYPES[type].selector(value));
    if (!elements.length) {
      console.warn("Map area not found:", type, value);
    }
    return elements;
  }

  function getMapAreaSize(element) {
    try {
      const { width, height } = element.getBBox();
      return width * height;
    } catch {
      return 0;
    }
  }

  function paintCountryMap() {
    countries.forEach((country) => {
      getMapAreas("country", country).forEach((element) => {
        element.classList.add("visited");
      });
    });
  }

  function paintPrefectureMap() {
    prefectureLevels.forEach((level, prefecture) => {
      getMapAreas("prefecture", prefecture).forEach((element) => {
        element.classList.add(level.mapClass);
        element.classList.toggle("visited", level.score >= 3);
      });
    });
  }

  function setupMapAreaInteractions(type, values) {
    values.forEach((value) => {
      const elements = getMapAreas(type, value);
      if (!elements.length) return;
      const label = getAreaLabel(value, elements[0]);
      const focusTarget = elements.reduce((largest, element) =>
        getMapAreaSize(element) > getMapAreaSize(largest) ? element : largest
      );
      let isPointed = false;
      let isFocused = false;
      const updateActiveState = () => {
        const active = isPointed || isFocused;
        elements.forEach((element) => {
          element.classList.toggle("is-map-active", active);
        });
      };
      const open = () => openTravelPopup(type, value, label);
      elements.forEach((element) => {
        element.classList.add("is-clickable");
        element.addEventListener("pointerenter", () => {
          isPointed = true;
          updateActiveState();
        });
        element.addEventListener("pointerleave", (event) => {
          if (elements.includes(event.relatedTarget)) return;
          isPointed = false;
          updateActiveState();
        });
        element.addEventListener("click", open);
      });
      focusTarget.setAttribute("tabindex", "0");
      focusTarget.setAttribute("role", "button");
      focusTarget.setAttribute("aria-label", `${label} 여행 보기`);
      focusTarget.addEventListener("focus", () => {
        isFocused = true;
        updateActiveState();
      });
      focusTarget.addEventListener("blur", () => {
        isFocused = false;
        updateActiveState();
      });
      focusTarget.addEventListener("keydown", (event) => {
        if (event.key === "Enter" || event.key === " ") {
          event.preventDefault();
          open();
        }
      });
    });
  }

  // 초기화

  function initMap() {
    paintCountryMap();
    paintPrefectureMap();
    setupMapAreaInteractions("country", countries);
    setupMapAreaInteractions("prefecture", visitedPrefectures);
  }

  function init() {
    renderVisitedStats();
    renderKeikenchi();
    renderChart();
    renderTravelList();
    setupTravelPopup();
    initMap();
  }

  init();
</script>