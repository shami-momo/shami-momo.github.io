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

## 지금까지 총 <span id="anime-count">-</span>를 봤어요.
<div class="chart-wrap">
  <canvas id="anime-watch-chart"></canvas>
</div>

<section class="anime-archive" aria-labelledby="anime-archive-title">
  <div class="anime-filters" role="group" aria-label="작품 유형 필터">
    <button type="button" class="anime-filter" data-filter="all" aria-pressed="true">
      전체
    </button>
    <button type="button" class="anime-filter" data-filter="series" aria-pressed="false">
      TV 시리즈
    </button>
    <button type="button" class="anime-filter" data-filter="movie" aria-pressed="false">
      극장판
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
  (() => {
    const anime = JSON.parse(
      document.querySelector("#anime-data").textContent
    ).map((item) => ({
      ...item,
      type: item.season === "극장판" ? "movie" : "series"
    }));

    const animeList = document.querySelector("#anime-list");
    const animeCount = document.querySelector("#anime-count");
    const animeFilters = [...document.querySelectorAll("[data-filter]")];
    const chartCanvas = document.querySelector("#anime-watch-chart");

    let animeWatchChart;
    let redrawFrame;

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

      return value === "2008년 이전"
        ? value
        : `${value.match(/\d{4}/)?.[0] ?? value}년`;
    }

    function cssVar(name) {
      return getComputedStyle(document.documentElement)
        .getPropertyValue(name)
        .trim();
    }

    function createCard(item) {
      const movieClass = item.type === "movie" ? " is-movie" : "";
      const rating = Number(item.rating);
      const perfectClass = rating === 5 ? " is-perfect" : "";

      const ratingMarkup = Number.isFinite(rating)
        ? `
          <span class="anime-rating${perfectClass}" aria-label="평점 ${rating}점">${rating}</span>
        `
        : "";

      return `
        <div class="anime-card${perfectClass}">
          <span class="season${movieClass}">${escapeHtml(item.season)}</span>
          <span class="anime-title">${escapeHtml(item.title)}</span>
          ${ratingMarkup}
        </div>
      `;
    }

    function renderList(filter = "all") {
      const visible = anime.filter(
        (item) => filter === "all" || item.type === filter
      );

      const groups = new Map();

      visible.forEach((item) => {
        const items = groups.get(item.year) ?? [];
        items.push(item);
        groups.set(item.year, items);
      });

      animeList.innerHTML = [...groups]
        .map(
          ([year, items]) => `
            <section class="year-section">
              <span class="year">${formatYear(year)}</span>
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
        (item) => formatYear(item.year) === "2008년 이전"
      );

      const years = anime
        .map((item) => Number(String(item.year).match(/\d{4}/)?.[0]))
        .filter(Number.isFinite);

      const firstYear = Math.min(...years);
      const lastYear = Math.max(...years);

      return [
        ...(hasOlderWorks ? ["2008년 이전"] : []),
        ...Array.from(
          { length: lastYear - firstYear + 1 },
          (_, index) => `${firstYear + index}년`
        )
      ];
    }

    function renderChart() {
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

      animeWatchChart?.destroy();

      animeWatchChart = new Chart(chartCanvas, {
        type: "line",
        data: {
          labels,
          datasets: [
            {
              label: "백합 없음",
              data: nonYuriData,
              fill: true,
              backgroundColor: cssVar("--highlight-soft"),
              borderColor: cssVar("--main"),
              borderWidth: 3,
              tension: 0.35,
              pointRadius: 2,
              pointHoverRadius: 5,
              pointBackgroundColor: cssVar("--main"),
              pointBorderWidth: 2
            },
            {
              label: "백합",
              data: yuriData,
              fill: true,
              backgroundColor: cssVar("--highlight-sub-soft"),
              borderColor: cssVar("--sub"),
              borderWidth: 3,
              tension: 0.35,
              pointRadius: 2,
              pointHoverRadius: 5,
              pointBackgroundColor: cssVar("--sub"),
              pointBorderWidth: 2
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
              display: false
            },
            tooltip: {
              titleFont: { family: font },
              bodyFont: { family: font },
              boxPadding: 6,
              callbacks: {
                label(context) {
                  return `${context.dataset.label}: ${context.raw}편`;
                }
              }
            }
          },
          scales: {
            x: {
              stacked: true,
              grid: { display: false },
              ticks: { color: cssVar("--muted"), font: { family: font, weight: "bold" } },
            },
            y: {
              stacked: true,
              grid: { display: false },
              ticks: { color: cssVar("--muted"), stepSize: 10, font: { family: font } },
            }
          }
        }
      });
    }

    animeCount.textContent = `${anime.length}개`;

    animeFilters.forEach((button) => {
      button.addEventListener("click", () => {
        animeFilters.forEach((item) => {
          item.setAttribute("aria-pressed", "false");
        });

        button.setAttribute("aria-pressed", "true");
        renderList(button.dataset.filter);
      });
    });

    function queueChartRedraw() {
      cancelAnimationFrame(redrawFrame);
      redrawFrame = requestAnimationFrame(renderChart);
    }

    new MutationObserver(queueChartRedraw).observe(document.documentElement, {
      attributes: true,
      attributeFilter: ["class", "data-theme"]
    });

    new MutationObserver(queueChartRedraw).observe(document.body, {
      attributes: true,
      attributeFilter: ["class", "data-theme"]
    });

    renderList();
    renderChart();
  })();
</script>