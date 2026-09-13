---
layout: page
title: "애니메이션"
subtitle: "백합이 좋아요"
permalink: /aburibinninaruyo/
styles: [anime]
---

## 지금까지 총 <span id="anime-count">-</span>의 작품을 봤어요.
<div class="anime-chart-wrap">
  <canvas
    id="anime-watch-chart"
    role="img"
    aria-label="연도별 시청 작품 수를 백합과 그 외 작품으로 나눈 막대그래프"
    aria-describedby="animeChartSummary"
  ></canvas>
</div>
<div id="animeChartSummary" class="visually-hidden"></div>

## 이런 작품들을 봤어요.
<div class="anime-controls">
  <div class="anime-search-row">
    <label class="visually-hidden" for="animeSearch">작품 제목 검색</label>
    <input id="animeSearch" class="anime-search" type="search" placeholder="작품 제목 검색" autocomplete="off">
    <button id="animeReset" class="anime-reset" type="button" hidden>초기화</button>
  </div>

  <div class="anime-filters" role="group" aria-label="작품 필터">
    <button type="button" class="anime-filter" data-filter="all" aria-pressed="true">전체</button>
    <button type="button" class="anime-filter" data-filter="series" aria-pressed="false">TVA</button>
    <button type="button" class="anime-filter" data-filter="movie" aria-pressed="false">극장판</button>
    <span class="anime-filter-divider" aria-hidden="true"></span>
    <button type="button" class="anime-filter" data-filter="perfect" aria-pressed="false">인생작</button>
    <button type="button" class="anime-filter" data-filter="yuri" aria-pressed="false">백합</button>
  </div>

  <p id="animeFilterStatus" class="anime-filter-status" aria-live="polite"></p>
</div>

<div id="anime-list" class="anime-groups"></div>

<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.7/dist/chart.umd.min.js"></script>

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
    filters: $$("[data-filter]"),
    search: $("#animeSearch"),
    reset: $("#animeReset"),
    status: $("#animeFilterStatus"),
    chartSummary: $("#animeChartSummary")
  };

  const cssVar = (name) =>
    getComputedStyle(document.documentElement).getPropertyValue(name).trim();

  // 데이터
  const anime = loadAnimeData(elements.data);

  // 상태
  const filterState = {
    type: "all",
    tags: new Set(),
    query: ""
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

  function getYearId(year) {
    return `anime-year-${String(year).trim().replace(/\s+/g, "-")}`;
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
      const matchesQuery = !filterState.query
        || String(item.title ?? "").toLocaleLowerCase("ko")
          .includes(filterState.query);

      return matchesType && matchesTags && matchesQuery;
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

  function readFilterStateFromUrl() {
    const params = new URLSearchParams(location.search);
    const type = params.get("type");
    const tags = params.get("tags")?.split(",") ?? [];

    if (TYPE_FILTERS.has(type)) filterState.type = type;
    tags.filter((tag) => ["perfect", "yuri"].includes(tag))
      .forEach((tag) => filterState.tags.add(tag));
    filterState.query = (params.get("q") ?? "").trim().toLocaleLowerCase("ko");
    if (elements.search) elements.search.value = params.get("q") ?? "";
  }

  function writeFilterStateToUrl() {
    const params = new URLSearchParams();
    if (filterState.type !== "all") params.set("type", filterState.type);
    if (filterState.tags.size) params.set("tags", [...filterState.tags].join(","));
    if (elements.search?.value.trim()) params.set("q", elements.search.value.trim());
    const query = params.toString();
    history.replaceState(null, "", `${location.pathname}${query ? `?${query}` : ""}${location.hash}`);
  }

  function updateFilterStatus(count) {
    if (elements.status) {
      elements.status.textContent = `전체 ${anime.length}편 중 ${count}편`;
    }
    if (elements.reset) {
      elements.reset.hidden = filterState.type === "all"
        && filterState.tags.size === 0
        && !filterState.query;
    }
  }

  function setupFilters() {
    elements.filters.forEach((button) => {
      button.addEventListener("click", () => {
        toggleFilter(button.dataset.filter);
        updateFilterButtons();
        renderAnimeList();
        writeFilterStateToUrl();
      });
    });

    elements.search?.addEventListener("input", () => {
      filterState.query = elements.search.value.trim().toLocaleLowerCase("ko");
      renderAnimeList();
      writeFilterStateToUrl();
    });

    elements.reset?.addEventListener("click", () => {
      filterState.type = "all";
      filterState.tags.clear();
      filterState.query = "";
      elements.search.value = "";
      updateFilterButtons();
      renderAnimeList();
      writeFilterStateToUrl();
      elements.search.focus();
    });
  }

  // 작품 카드와 목록
  function createAnimeCard(item) {
    const perfectClass = item.perfect ? " is-perfect" : "";
    const ratingMarkup = item.rating === null
      ? ""
      : `<span class="anime-rating${perfectClass}">★${item.rating}</span>`;

    return `
      <li class="anime-card${perfectClass}">
        <span class="anime-season">${escapeHtml(item.seasonLabel)}</span>
        ${ratingMarkup}
        <span class="anime-title">${escapeHtml(item.title ?? "")}</span>
      </li>
    `;
  }

  function renderAnimeCount() {
    if (elements.count) {
      elements.count.textContent = `${anime.length}편`;
    }
  }

  function renderAnimeList() {
    if (!elements.list) return;

    const filteredAnime = getFilteredAnime();
    const groups = groupAnimeByYear(filteredAnime);
    const markup = [...groups]
      .map(([year, items]) => {
        const yearId = getYearId(year);
        return `
          <section id="${yearId}" class="anime-year-section" aria-labelledby="${yearId}-title">
            <h3 id="${yearId}-title" class="anime-year">${escapeHtml(year)}</h3>
            <ul class="anime-list">
              ${items.map(createAnimeCard).join("")}
            </ul>
          </section>
        `;
      })
      .join("");

    elements.list.innerHTML =
      markup || '<p class="anime-empty">해당하는 작품이 없어요. 필터나 검색어를 바꿔봐요.</p>';
    updateFilterStatus(filteredAnime.length);
  }

  // 시청 작품 차트
  function renderChart() {
    const chartCanvas = $("#anime-watch-chart");
    if (!chartCanvas || !window.Chart) return;

    const font = getComputedStyle(document.body).fontFamily;

    if (elements.chartSummary) {
      elements.chartSummary.innerHTML = `
        <table>
          <caption>연도별 시청 작품 수</caption>
          <thead><tr><th>연도</th><th>백합</th><th>그 외</th></tr></thead>
          <tbody>
            ${chartLabels.map((year, index) => `
              <tr><th>${escapeHtml(year)}</th><td>${chartSeries.yuri[index]}편</td><td>${chartSeries.normal[index]}편</td></tr>
            `).join("")}
          </tbody>
        </table>
      `;
    }

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
        onClick: (_, chartElements) => {
          const element = chartElements[0];
          if (!element) return;
          document.querySelector(`#${CSS.escape(getYearId(chartLabels[element.index]))}`)
            ?.scrollIntoView({ behavior: "smooth", block: "start" });
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
              autoSkip: true,
              maxTicksLimit: matchMedia("(max-width: 640px)").matches ? 10 : 20,
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
    readFilterStateFromUrl();
    renderAnimeCount();
    updateFilterButtons();
    renderAnimeList();
    renderChart();
    setupFilters();
  }

  init();
  window.addEventListener("themechange", renderChart);
</script>
