---
layout: page
title: "오타쿠"
subtitle: "쓸데없는 내용"
permalink: /anime/
---

<!--
## 디맥 성과
<div id="djmax" class="djmax-result" aria-live="polite">
  성과 정보를 불러오는 중…
</div>
-->

## 지금까지 총 <span id="anime-count">-</span>의 작품을 봤어요.
<div class="chart-wrap">
  <canvas id="anime-watch-chart"></canvas>
</div>

<section class="anime-archive" aria-labelledby="anime-archive-title">
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
      👍
    </button>
    <button type="button" class="anime-filter" data-filter="yuri" aria-pressed="false">
      🌸✋
    </button>
  </div>

  <div id="anime-list" class="anime-groups"></div>
</section>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script id="anime-data" type="application/json">
  {{ site.data.anime | jsonify }}
</script>

<!--
<script>
  (() => {
    const nickname = "saika";
    const button = "4";
    const apiBase = "https://v-archive.net/api/archive";
    const target = document.querySelector("#djmax");

    function escapeHtml(value) {
      return String(value ?? "—")
        .replaceAll("&", "&amp;")
        .replaceAll("<", "&lt;")
        .replaceAll(">", "&gt;")
        .replaceAll('"', "&quot;")
        .replaceAll("'", "&#039;");
    }

    async function fetchJson(path) {
      const response = await fetch(`${apiBase}/${path}`, {
        headers: { Accept: "application/json" },
      });

      if (!response.ok) {
        throw new Error(`API request failed: ${response.status}`);
      }

      return response.json();
    }

    async function loadDjmaxProfile() {
      const user = encodeURIComponent(nickname);

      const [djClassData, tierData] = await Promise.all([
        fetchJson(`${user}/djClass/${button}`),
        fetchJson(`${user}/tier/${button}`),
      ]);

      const djClass = djClassData.djClass ?? "—";
      const tier = tierData.tier?.name ?? "—";
      const songs = (tierData.topList ?? []).slice(0, 3);

      target.innerHTML = `
        <div class="djmax-summary">
          <div class="djmax-stat">
            <span class="djmax-label">DJ CLASS</span>
            <span class="djmax-value">${escapeHtml(djClass)}</span>
          </div>

          <div class="djmax-stat">
            <span class="djmax-label">TIER</span>
            <span class="djmax-value">${escapeHtml(tier)}</span>
          </div>
        </div>

        <div class="djmax-top-songs">
          <span class="djmax-label">TIER TOP 3</span>

          <div class="djmax-song-list">
            ${songs.map((song, index) => `
              <div class="djmax-song">
                <span class="djmax-song-rank">${index + 1}</span>

                <span class="djmax-song-title">${escapeHtml(song.name)}</span>
                
                <span class="djmax-song-meta">
                  ${escapeHtml(song.pattern)}${escapeHtml(song.level)}
                </span>

                <span class="djmax-song-score">
                  ${Number(song.score).toFixed(2)}%
                </span>
              </div>
            `).join("") || "<div>등록된 티어 곡이 없어.</div>"}
          </ol>
        </div>
      `;
    }

    loadDjmaxProfile().catch((error) => {
      console.error(error);
      target.textContent = "성과 정보를 불러오지 못했어.";
      target.classList.add("is-error");
    });
  })();
</script>
-->

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script id="anime-data" type="application/json">
  {{ site.data.anime | jsonify }}
</script>

<script>
  // Configuration
  const anime = JSON.parse(
    document.querySelector("#anime-data").textContent
  ).map((item) => ({
    ...item,
    type: item.season === "극장판" ? "movie" : "series",
    perfect: Number(item.rating) === 5
  }));

  let animeWatchChart;

  const $ = (selector) => document.querySelector(selector);
  const $$ = (selector) => [...document.querySelectorAll(selector)];

  const animeList = $("#anime-list");
  const animeCount = $("#anime-count");
  const animeFilters = $$("[data-filter]");

  function escapeHtml(value) {
    return String(value)
      .replaceAll("&", "&amp;")
      .replaceAll("<", "&lt;")
      .replaceAll(">", "&gt;")
      .replaceAll('"', "&quot;")
      .replaceAll("'", "&#039;");
  }

  function formatYear(year) {
    const value = String(year).trim();

    return value === "2008 이전"
      ? value
      : `${value.match(/\d{4}/)?.[0] ?? value}`;
  }

  function createCard(item) {
    const movieClass = item.type === "movie" ? " is-movie" : "";
    const rating = Number(item.rating);
    const perfectClass = item.perfect ? " is-perfect" : "";

    const ratingMarkup = Number.isFinite(rating)
      ? `<span class="anime-rating${perfectClass}" aria-label="평점 ${rating}점">★${rating}</span>`
      : "";

    return `
      <div class="anime-card${perfectClass}">
        <span class="anime-season${movieClass}">${escapeHtml(item.season)}</span>
        ${ratingMarkup}
        <span class="anime-title">${escapeHtml(item.title)}</span>
      </div>
    `;
  }

  const typeFilters = new Set(["all", "series", "movie"]);

  let activeTypeFilter = "all";
  const activeTagFilters = new Set();

  function renderList() {
    const visible = anime.filter((item) => {
      const matchesType =
        activeTypeFilter === "all" || item.type === activeTypeFilter;

      const matchesPerfect =
        !activeTagFilters.has("perfect") || item.perfect;

      const matchesYuri =
        !activeTagFilters.has("yuri") || item.yuri;

      return matchesType && matchesPerfect && matchesYuri;
    });

    const groups = new Map();

    visible.forEach((item) => {
      const year = formatYear(item.year);
      const items = groups.get(year) ?? [];

      items.push(item);
      groups.set(year, items);
    });

    animeList.innerHTML = [...groups]
      .map(
        ([year, items]) => `
          <section class="anime-year-section">
            <span class="anime-year">${year}</span>
            <div class="anime-list">
              ${items.map(createCard).join("")}
            </div>
          </section>
        `
      )
      .join("") || '<p class="empty">해당하는 작품이 없어.</p>';
  }

  function updateFilterButtons() {
    animeFilters.forEach((button) => {
      const filter = button.dataset.filter;
      const isActive = typeFilters.has(filter)
        ? filter === activeTypeFilter
        : activeTagFilters.has(filter);

      button.setAttribute("aria-pressed", String(isActive));
    });
  }

  animeFilters.forEach((button) => {
    button.addEventListener("click", () => {
      const filter = button.dataset.filter;

      if (typeFilters.has(filter)) {
        activeTypeFilter = filter;
      } else if (activeTagFilters.has(filter)) {
        activeTagFilters.delete(filter);
      } else {
        activeTagFilters.add(filter);
      }

      updateFilterButtons();
      renderList();
    });
  });

  animeCount.textContent = `${anime.length}편`;

  updateFilterButtons();
  renderList();

  function getChartLabels() {
    const years = anime.map((item) => formatYear(item.year));
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

    return [
      ...(hasOlderWorks ? ["2008 이전"] : []),
      ...Array.from(
        { length: lastYear - firstYear + 1 },
        (_, index) => String(firstYear + index)
      )
    ];
  }

  function renderChart() {
    const chartCanvas = $("#anime-watch-chart");
    if (!chartCanvas || !window.Chart) return;

    const labels = getChartLabels();
    const itemsByYear = new Map();
    const font = getComputedStyle(document.body).fontFamily;

    anime.forEach((item) => {
      const year = formatYear(item.year);
      const items = itemsByYear.get(year) ?? [];

      items.push(item);
      itemsByYear.set(year, items);
    });

    const yuriData = [];
    const nonYuriData = [];

    labels.forEach((year) => {
      let yuriCount = 0;
      let nonYuriCount = 0;

      (itemsByYear.get(year) ?? []).forEach((item) => {
        if (item.yuri) yuriCount += 1;
        else nonYuriCount += 1;
      });

      yuriData.push(yuriCount);
      nonYuriData.push(nonYuriCount);
    });

    animeWatchChart = new Chart(chartCanvas, {
      type: "bar",
      data: {
        labels,
        datasets: [
          {
            label: "백합",
            data: yuriData,
            fill: true,
            backgroundColor: cssVar("--main-soft"),
          },
          {
            label: "노말",
            data: nonYuriData,
            fill: true,
            backgroundColor: cssVar("--highlight"),
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
            titleFont: {
              family: font
            },
            bodyFont: {
              family: font
            },
            boxPadding: 6,
            callbacks: {
              label: (context) => `${context.dataset.label}: ${context.raw}편`
            }
          }
        },
        scales: {
          x: {
            stacked: true,
            grid: {
              display: false
            },
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
            grid: {
              display: false
            },
            ticks: {
              color: cssVar("--muted"),
              stepSize: 5,
              font: {
                family: font
              }
            }
          }
        }
      }
    });
  }
  renderChart();
</script>