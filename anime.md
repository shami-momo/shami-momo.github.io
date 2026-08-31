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
    <button type="button" class="anime-filter" data-filter="perfect" aria-pressed="false">
      👍
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
  let redrawFrame;

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
    const perfectClass = rating === 5 ? " is-perfect" : "";

    const ratingMarkup = Number.isFinite(rating)
      ? `
        <span class="anime-rating${perfectClass}" aria-label="평점 ${rating}점">★${rating}</span>
      `
      : "";

    return `
      <div class="anime-card${perfectClass}">
        <span class="anime-season${movieClass}">${escapeHtml(item.season)}</span>
        ${ratingMarkup}
        <span class="anime-title">${escapeHtml(item.title)}</span>
      </div>
    `;
  }

  function renderList(filter = "all") {
    const visible = anime.filter((item) => {
      if (filter === "all") return true;
      if (filter === "perfect") return item.perfect;

      return item.type === filter;
    });

    const groups = new Map();

    visible.forEach((item) => {
      const items = groups.get(item.year) ?? [];
      items.push(item);
      groups.set(item.year, items);
    });

    animeList.innerHTML = [...groups]
      .map(
        ([year, items]) => `
          <section class="anime-year-section">
            <span class="anime-year">${formatYear(year)}</span>
            <div class="anime-list">
              ${items.map(createCard).join("")}
            </div>
          </section>
        `
      )
      .join("") || '<p class="empty">해당하는 작품이 없어.</p>';
  }

  function getChartLabels() {
    const hasOlderWorks = anime.some(
      (item) => formatYear(item.year) === "2008 이전"
    );

    const numericYears = anime
      .map((item) => {
        const yearText = String(item.year).trim();

        if (formatYear(item.year) === "2008 이전") {
          return null;
        }

        return Number(yearText.match(/\d{4}/)?.[0]);
      })
      .filter((year) => Number.isFinite(year));

    if (numericYears.length === 0) {
      return hasOlderWorks ? ["2008 이전"] : [];
    }

    const firstYear = Math.min(...numericYears);
    const lastYear = Math.max(...numericYears);

    return [
      ...(hasOlderWorks ? ["2008 이전"] : []),
      ...Array.from(
        { length: lastYear - firstYear + 1 },
        (_, index) => `${firstYear + index}`
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

    let yuriCount = 0;
    let nonYuriCount = 0;

    const yuriData = [];
    const nonYuriData = [];

    labels.forEach((year) => {
      (itemsByYear.get(year) ?? []).forEach((item) => {
        if (item.yuri) yuriCount += 1;
        else nonYuriCount += 1;
      });

      yuriData.push(yuriCount);
      nonYuriData.push(nonYuriCount);
    });

    animeWatchChart = createLineChart({
      canvas: chartCanvas,
      chart: animeWatchChart,
      labels,
      stacked: true,
      datasets: [
        createLineDataset({
          label: "백합 없음",
          data: nonYuriData,
          color: "--sub",
          fillColor: "--highlight-sub-soft"
        }),
        createLineDataset({
          label: "백합",
          data: yuriData,
          color: "--main",
          fillColor: "--highlight-soft"
        })
      ],
      formatTooltip: (context) => `${context.dataset.label}: ${context.raw}편`
    });
  }

  animeCount.textContent = `${anime.length}편`;

  animeFilters.forEach((button) => {
    button.addEventListener("click", () => {
      animeFilters.forEach((item) => {
        item.setAttribute("aria-pressed", "false");
      });

      button.setAttribute("aria-pressed", "true");
      renderList(button.dataset.filter);
    });
  });

  renderList();
  renderChart();
</script>