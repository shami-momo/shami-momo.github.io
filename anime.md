---
layout: page
title: "애니메이션"
subtitle: "백합이 좋아요"
permalink: /aburibinninaruyo/
---

## 지금까지 총 <span id="anime-count">-</span>의 작품을 봤어요.
<div class="chart-wrap">
  <canvas id="anime-watch-chart"></canvas>
</div>

## 이런 작품들을 봤어요.
<div class="anime-filters" role="group" aria-label="작품 유형 필터">
  <button type="button" class="anime-filter" data-filter="all" aria-pressed="true">
    전체
  </button>
  <button type="button" class="anime-filter" data-filter="series" aria-pressed="false">
    TVA
  </button>
  <button type="button" class="anime-filter" data-filter="movie" aria-pressed="false">
    극장판
  </button>
  <span class="anime-filter-divider" aria-hidden="true"></span>
  <button type="button" class="anime-filter" data-filter="perfect" aria-pressed="false">
    인생작
  </button>
  <button type="button" class="anime-filter" data-filter="yuri" aria-pressed="false">
    백합
  </button>
</div>

<div id="anime-list" class="anime-groups"></div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script id="anime-data" type="application/json">
  {{ site.data.anime | jsonify }}
</script>

<script>
  // 설정
  const TYPE_FILTERS = new Set(["all", "series", "movie"]);

  // DOM 선택
  const $ = (selector) => document.querySelector(selector);
  const $$ = (selector) => [...document.querySelectorAll(selector)];

  const elements = {
    data: $("#anime-data"),
    list: $("#anime-list"),
    count: $("#anime-count"),
    filters: $$("[data-filter]")
  };

  // 데이터
  const anime = loadAnimeData(elements.data);

  // 상태
  const filterState = {
    type: "all",
    tags: new Set()
  };

  let animeWatchChart = null;

  // 텍스트와 데이터 유틸
  function escapeHtml(value) {
    return String(value)
      .replaceAll("&", "&amp;")
      .replaceAll("<", "&lt;")
      .replaceAll(">", "&gt;")
      .replaceAll('"', "&quot;")
      .replaceAll("'", "&#039;");
  }

  function formatYear(year) {
    const value = String(year ?? "").trim();

    return value === "2008 이전"
      ? value
      : value.match(/\d{4}/)?.[0] ?? value;
  }

  function normalizeAnime(item) {
    const rawRating =
      item.rating === null || item.rating === undefined || item.rating === ""
        ? null
        : Number(item.rating);

    const rating = Number.isFinite(rawRating) ? rawRating : null;
    const season = Number(item.season);
    const isValidSeason =
      Number.isInteger(season) && season >= 0 && season <= 4;

    return {
      ...item,
      yearLabel: formatYear(item.year),
      season: isValidSeason ? season : null,
      seasonLabel: season === 0
        ? "극장판"
        : season >= 1 && season <= 4
          ? `${season}분기`
          : "",
      type: season === 0 ? "movie" : "series",
      rating,
      perfect: rating === 5
    };
  }

  function loadAnimeData(source) {
    if (!source) {
      console.warn("Anime data element not found: #anime-data");
      return [];
    }

    try {
      const parsed = JSON.parse(source.textContent);

      if (!Array.isArray(parsed)) {
        throw new TypeError("Anime data must be an array.");
      }

      return parsed
        .filter((item) => item && typeof item === "object")
        .map(normalizeAnime);
    } catch (error) {
      console.error("Failed to parse anime data:", error);
      return [];
    }
  }

  function groupAnimeByYear(items) {
    const groups = new Map();

    items.forEach((item) => {
      const yearItems = groups.get(item.yearLabel) ?? [];

      yearItems.push(item);
      groups.set(item.yearLabel, yearItems);
    });

    return groups;
  }

  function getChartLabels(items) {
    const years = items.map(({ yearLabel }) => yearLabel);
    const hasOlderWorks = years.includes("2008 이전");
    const numericYears = years
      .filter((year) => year !== "2008 이전")
      .map(Number)
      .filter(Number.isFinite);

    if (!numericYears.length) {
      return hasOlderWorks ? ["2008 이전"] : [];
    }

    const firstYear = Math.min(...numericYears);
    const lastYear = Math.max(...numericYears);
    const yearRange = Array.from(
      { length: lastYear - firstYear + 1 },
      (_, index) => String(firstYear + index)
    );

    return hasOlderWorks ? ["2008 이전", ...yearRange] : yearRange;
  }

  function getChartSeries(labels, groups) {
    const counts = labels.map((year) => {
      const items = groups.get(year) ?? [];
      const yuri = items.filter((item) => item.yuri).length;

      return {
        yuri,
        normal: items.length - yuri
      };
    });

    return {
      yuri: counts.map(({ yuri }) => yuri),
      normal: counts.map(({ normal }) => normal)
    };
  }

  const animeByYear = groupAnimeByYear(anime);
  const chartLabels = getChartLabels(anime);
  const chartSeries = getChartSeries(chartLabels, animeByYear);

  // 필터
  function getFilteredAnime() {
    return anime.filter((item) => {
      const matchesType =
        filterState.type === "all" || item.type === filterState.type;
      const matchesTags = [...filterState.tags]
        .every((filter) => Boolean(item[filter]));

      return matchesType && matchesTags;
    });
  }

  function isFilterActive(filter) {
    return TYPE_FILTERS.has(filter)
      ? filter === filterState.type
      : filterState.tags.has(filter);
  }

  function toggleFilter(filter) {
    if (TYPE_FILTERS.has(filter)) {
      filterState.type = filter;
      return;
    }

    if (filterState.tags.has(filter)) {
      filterState.tags.delete(filter);
    } else {
      filterState.tags.add(filter);
    }
  }

  function updateFilterButtons() {
    elements.filters.forEach((button) => {
      const filter = button.dataset.filter;
      button.setAttribute("aria-pressed", String(isFilterActive(filter)));
    });
  }

  function setupFilters() {
    elements.filters.forEach((button) => {
      button.addEventListener("click", () => {
        toggleFilter(button.dataset.filter);
        updateFilterButtons();
        renderAnimeList();
      });
    });
  }

  // 작품 카드와 목록
  function createAnimeCard(item) {
    const perfectClass = item.perfect ? " is-perfect" : "";
    const movieClass = item.type === "movie" ? " is-movie" : "";
    const ratingMarkup = item.rating === null
      ? ""
      : `<span class="anime-rating${perfectClass}">★${item.rating}</span>`;

    return `
      <div class="anime-card${perfectClass}">
        <span class="anime-season${movieClass}">${escapeHtml(item.seasonLabel)}</span>
        ${ratingMarkup}
        <span class="anime-title">${escapeHtml(item.title ?? "")}</span>
      </div>
    `;
  }

  function renderAnimeCount() {
    if (elements.count) {
      elements.count.textContent = `${anime.length}편`;
    }
  }

  function renderAnimeList() {
    if (!elements.list) return;

    const groups = groupAnimeByYear(getFilteredAnime());
    const markup = [...groups]
      .map(
        ([year, items]) => `
          <section class="anime-year-section">
            <span class="anime-year">${escapeHtml(year)}</span>
            <div class="anime-list">
              ${items.map(createAnimeCard).join("")}
            </div>
          </section>
        `
      )
      .join("");

    elements.list.innerHTML =
      markup || '<p class="empty">해당하는 작품이 없어요.</p>';
  }

  // 시청 작품 차트
  function renderChart() {
    const chartCanvas = $("#anime-watch-chart");
    if (!chartCanvas || !window.Chart) return;

    const font = getComputedStyle(document.body).fontFamily;

    animeWatchChart?.destroy();

    animeWatchChart = new Chart(chartCanvas, {
      type: "bar",
      data: {
        labels: chartLabels,
        datasets: [
          {
            label: "백합",
            data: chartSeries.yuri,
            backgroundColor: cssVar("--main-soft")
          },
          {
            label: "노말",
            data: chartSeries.normal,
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
              label: (context) =>
                `${context.dataset.label}: ${context.raw}편`
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
              stepSize: 5,
              font: { family: font }
            }
          }
        }
      }
    });
  }

  // 초기화
  function init() {
    renderAnimeCount();
    updateFilterButtons();
    renderAnimeList();
    renderChart();
    setupFilters();
  }

  init();
</script>