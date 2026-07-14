---
layout: page
title: "Travel"
subtitle: "지금까지 갔던 곳들을 한눈에 돌아봅니다."
permalink: /travel/
---

자세한 여행기는 <a href="https://sei-and-the-world.tistory.com/">티스토리</a>를 방문해주세요.

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
    Map adapted from
    <a href="https://github.com/Snack-X/keikenchi">Snack-X/keikenchi</a>.
  </figcaption>
</figure>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
  const trips = {{ site.data.travel.trips | jsonify }};
  const DAY = 24 * 60 * 60 * 1000;

  const $ = (selector) => document.querySelector(selector);

  function cssVar(name) {
    return getComputedStyle(document.documentElement)
      .getPropertyValue(name)
      .trim();
  }

  function date(dateString) {
    const [year, month, day] = dateString.split("-").map(Number);
    return new Date(Date.UTC(year, month - 1, day));
  }

  function uniqueFrom(key) {
    return [...new Set(trips.flatMap((trip) => trip[key] ?? []))];
  }

  function paintMap(values, selectorFactory) {
    values.forEach((value) => {
      const els = document.querySelectorAll(selectorFactory(value));

      if (els.length === 0) {
        console.warn("Map area not found:", value);
      }

      els.forEach((el) => {
        el.classList.add("visited");
      });
    });
  }

  function renderVisitedStats(countries, prefectures) {
    const countryEl = $("#visitedCountryCount");
    const prefectureEl = $("#visitedPrefectureCount");

    if (countryEl) {
      countryEl.textContent = `${countries.length}개 나라`;
    }

    if (prefectureEl) {
      prefectureEl.textContent = `${prefectures.length}개 현`;
    }
  }

  function getYearlyDays() {
    const yearly = {};

    trips.forEach((trip) => {
      let current = date(trip.start);
      const end = date(trip.end);

      while (current <= end) {
        const year = current.getUTCFullYear();

        yearly[year] = (yearly[year] ?? 0) + 1;
        current = new Date(current.getTime() + DAY);
      }
    });

    return Object.entries(yearly)
      .map(([year, days]) => ({
        year,
        days,
      }))
      .sort((a, b) => Number(a.year) - Number(b.year));
  }

  function renderChart() {
    const canvas = $("#travelDaysChart");

    if (!canvas || typeof Chart === "undefined") {
      return;
    }

    const yearly = getYearlyDays();
    const labels = yearly.map((item) => item.year);
    const data = yearly.map((item) => item.days);
    const total = data.reduce((sum, value) => sum + value, 0);
    const font = getComputedStyle(document.body).fontFamily;

    const totalEl = $("#totalTravelDays");

    if (totalEl) {
      totalEl.textContent = `${total}일`;
    }

    new Chart(canvas, {
      type: "bar",
      data: {
        labels,
        datasets: [
          {
            label: "여행일수",
            data,
            backgroundColor: cssVar("--map-visited"),
            hoverBackgroundColor: cssVar("--map-visited"),
            borderSkipped: false,
          },
        ],
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,

        plugins: {
          legend: {
            display: false,
          },
          tooltip: {
            titleFont: {
              family: font,
            },
            bodyFont: {
              family: font,
            },
            callbacks: {
              label(context) {
                return `${context.raw}일`;
              },
            },
          },
        },

        scales: {
          x: {
            grid: {
              display: false,
            },
            ticks: {
              color: cssVar("--muted"),
              font: {
                family: font,
                weight: "bold",
              },
            },
          },
          y: {
            beginAtZero: true,
            grid: {
              display: false,
            },
            ticks: {
              color: cssVar("--muted"),
              stepSize: 10,
              font: {
                family: font,
              },
              callback(value) {
                return value;
              },
            },
          },
        },
      },
    });
  }

  const countries = uniqueFrom("countries");
  const prefectures = uniqueFrom("prefectures");

  paintMap(countries, (country) => `.world-map [id="${country}"]`);
  paintMap(prefectures, (pref) => `.japan-map .prefecture[data-name="${pref}"]`);

  renderVisitedStats(countries, prefectures);
  renderChart();
</script>