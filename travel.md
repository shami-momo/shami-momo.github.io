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

  function lineGradient(context) {
    const { chart } = context;
    const { ctx, chartArea } = chart;

    if (!chartArea) {
      return cssVar("--chart-area-top");
    }

    const gradient = ctx.createLinearGradient(
      0,
      chartArea.top,
      0,
      chartArea.bottom
    );

    gradient.addColorStop(0, cssVar("--chart-area-top"));
    gradient.addColorStop(1, cssVar("--chart-area-bottom"));

    return gradient;
  }

  function date(dateString) {
    const [y, m, d] = dateString.split("-").map(Number);
    return new Date(Date.UTC(y, m - 1, d));
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
      type: "line",
      data: {
        labels,
        datasets: [
          {
            label: "여행일수",
            data,

            fill: true,
            backgroundColor: lineGradient,
            borderColor: cssVar("--main"),

            borderWidth: 3,
            tension: 0.35,

            pointRadius: 5,
            pointHoverRadius: 6,
            pointBackgroundColor: cssVar("--main"),
            pointBorderColor: cssVar("--bg"),
            pointBorderWidth: 2,
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

  function daysBetween(start, end) {
    const startDate = date(start);
    let endDate = date(end);
    return Math.round((endDate - startDate) / DAY) + 1;
  }

  const TIMELINE_INITIAL_COUNT = 5;
  let timelineExpanded = false; 

  function formatTripMonth(trip) {
    const [year, month] = trip.start.split("-");
    return `${year}년 ${Number(month)}월`;
  }

  function formatTripCount(trip) {
    const days = daysBetween(trip.start, trip.end);
    return trip.count ?? `${days-1}박 ${days}일`;
  }

  function createTravelCard(trip) {
    return `
      <a class="travel-card" href="${trip.url}">
        <div class="travel-card-body">
          <div class="travel-card-meta">
            <span>${formatTripMonth(trip)}</span>
            <span>・</span>
            <span class="travel-card-count">${formatTripCount(trip)}</span>
          </div>

          <h3>${trip.title} <span class="travel-card-icon">${trip.emoji ?? "✈️"}</span></h3>
          <p>${trip.description ?? ""}</p>
        </div>
      </a>
    `;
  }

  function renderTimeline() {
    const root = $("#travelTimeline");
    const shell = $("#travelTimelineShell");
    const toggle = $("#travelTimelineToggle");

    if (!root) return;

    const sortedTrips = trips
      .filter((trip) => trip.url)
      .sort((a, b) => date(b.start) - date(a.start));

    root.innerHTML = sortedTrips.map(createTravelCard).join("");

    if (shell) {
      shell.classList.toggle("is-expanded", timelineExpanded);
    }

    if (!toggle) return;

    if (sortedTrips.length <= TIMELINE_INITIAL_COUNT) {
      toggle.hidden = true;
      return;
    }

    toggle.hidden = false;
    toggle.innerHTML = timelineExpanded
      ? `
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-chevron-up-icon lucide-chevron-up"><path d="m18 15-6-6-6 6"/></svg>
      `
      : `
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-chevron-down-icon lucide-chevron-down"><path d="m6 9 6 6 6-6"/></svg>
      `;
  }

  function setupTimelineToggle() {
    const toggle = $("#travelTimelineToggle");

    if (!toggle) return;

    toggle.onclick = () => {
      timelineExpanded = !timelineExpanded;
      renderTimeline();

      if (!timelineExpanded) {
        const timeline = $("#travelTimeline");

        if (timeline) {
          timeline.scrollIntoView({
            behavior: "smooth",
            block: "start",
          });
        }
      }
    };
  }

  const countries = uniqueFrom("countries");
  const prefectures = uniqueFrom("prefectures");

  paintMap(countries, (country) => `.world-map [id="${country}"]`);
  paintMap(prefectures, (pref) => `.japan-map .prefecture[data-name="${pref}"]`);

  renderVisitedStats(countries, prefectures);
  renderChart();
  renderTimeline();
  setupTimelineToggle();
</script>