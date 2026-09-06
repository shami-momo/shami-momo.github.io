---
layout: page
title: "Test"
subtitle: "리겜 테스트"
permalink: /rythm/
---

<div class="game-layout">
  <div class="play-area">
    <div class="meta">HAMSIN 4B MX· BPM 150</div>
    <section class="stage" aria-label="4 line rhythm game">
      <div class="notes" id="notes"></div>
      <div class="judge-line"></div>
      <div class="judgement" id="judgement"></div>
    </section>
    <div class="key-row" aria-hidden="true">
      <kbd>S</kbd><kbd>D</kbd><kbd>;</kbd><kbd>'</kbd>
    </div>
  </div>

  <aside class="result" id="result"></aside>
</div>

<script>
  // Hamsin
  const BPM = 150;
  const SIXTEENTH = 60000 / BPM / 4;
  const lanesBySixteenth = [
    [0, 3], [0, 3], [0, 3], [0, 3],
    [1, 3], [1, 3], [1, 3], [1, 3],
    [1, 2], [1, 2], [1, 2], [1, 2],
    [0, 2], [0, 2], [0, 2], [0, 2],
  ];

  const FALL_TIME = 700;
  const MAX_100 = 42;
  const BREAK = 180;

  const KEYS = ["s", "d", ";", "'"];
  const JUDGEMENTS = [100, 90, 80, 70, 60, 50, 40, 30, 20, 10];

  const chart = lanesBySixteenth.flatMap((lanes, index) =>
    lanes.map((lane) => ({
      lane,
      time: FALL_TIME + index * SIXTEENTH,
    }))
  );

  const notesEl = document.querySelector("#notes");
  const judgementEl = document.querySelector("#judgement");
  const resultEl = document.querySelector("#result");
  const keyEls = [...document.querySelectorAll(".key-row kbd")];

  let started = false;
  let startTime = 0;
  let animationId = null;
  let judged = 0;
  let totalPercent = 0;
  let breakCount = 0;
  let judgeCounts = {};

  function getMaxPercent(diff) {
    if (diff <= MAX_100) return 100;

    const stepSize = (BREAK - MAX_100) / (JUDGEMENTS.length - 1);
    const step = Math.ceil((diff - MAX_100) / stepSize);

    return JUDGEMENTS[Math.min(step, JUDGEMENTS.length - 1)];
  }

  function showJudgement(text, type) {
    judgementEl.textContent = text;
    judgementEl.className = `judgement ${type}`;

    void judgementEl.offsetWidth;
    judgementEl.classList.add("show");
  }

  function updateResult() {
    const accuracy = judged ? totalPercent / judged : 0;
    const finished = judged === chart.length;
    const fullCombo = finished && breakCount === 0;

    const status = finished
      ? (fullCombo ? "MAX COMBO!" : "FAILED")
      : (started ? "PLAYING" : "PRESS SPACE");

    const breakdown = JUDGEMENTS
      .map((percent) => `
        <div class="judge-count-row">
          <span>MAX ${percent}%</span>
          <span>${judgeCounts[percent]}</span>
        </div>
      `)
      .join("");

    resultEl.innerHTML = `
      <span class="judge-title">${status}</span>

      <div class="judge-counts">
        <div class="judge-count-row">
          <span>ACCURACY</span>
          <span>${accuracy.toFixed(2)}%</span>
        </div>

        <div class="result-divider"></div>

        ${breakdown}

        <div class="judge-count-row">
          <span>BREAK</span>
          <span>${breakCount}</span>
        </div>
      </div>
    `;
  }

  function reset() {
    cancelAnimationFrame(animationId);

    started = false;
    startTime = 0;
    animationId = null;
    judged = 0;
    totalPercent = 0;
    breakCount = 0;

    judgeCounts = Object.fromEntries(
      JUDGEMENTS.map((percent) => [percent, 0])
    );

    notesEl.replaceChildren();

    for (const note of chart) {
      note.state = "waiting";
      note.el = document.createElement("div");
      note.el.className = [
        "note",
        `lane-${note.lane}`,
        note.lane === 0 || note.lane === 3 ? "yellow" : "blue",
      ].join(" ");

      notesEl.append(note.el);
    }

    updateResult();
  }

  function findNearestNote(lane, time) {
    let nearest = null;

    for (const note of chart) {
      if (note.lane !== lane || note.state !== "waiting") continue;

      if (
        !nearest ||
        Math.abs(note.time - time) < Math.abs(nearest.time - time)
      ) {
        nearest = note;
      }
    }

    return nearest;
  }

  function miss(note) {
    note.state = "miss";
    note.el.style.opacity = "0";

    judged++;
    breakCount++;

    showJudgement("BREAK", "miss");
    updateResult();
  }

  function judge(lane) {
    if (!started) return;

    const elapsed = performance.now() - startTime;
    const note = findNearestNote(lane, elapsed);

    if (!note) return;

    const diff = Math.abs(note.time - elapsed);

    if (diff > BREAK) return;

    const maxPercent = getMaxPercent(diff);

    note.state = "hit";
    note.el.classList.add("hit");

    judged++;
    totalPercent += maxPercent;
    judgeCounts[maxPercent]++;

    showJudgement(
      `MAX ${maxPercent}%`,
      maxPercent === 100 ? "perfect" : "normal"
    );
    updateResult();
  }

  function showHitEffect(lane) {
    const effect = document.createElement("div");
    const color = lane === 0 || lane === 3 ? "yellow" : "blue";

    effect.className = `hit-effect lane-${lane} ${color}`;

    effect.addEventListener("animationend", () => effect.remove(), {
      once: true,
    });
  }

  function finish() {
    started = false;
    updateResult();
  }

  function frame(now) {
    const elapsed = now - startTime;

    for (const note of chart) {
      if (note.state !== "waiting") continue;

      const delta = note.time - elapsed;
      const y = 96 - (delta / FALL_TIME) * 100;

      note.el.style.top = `${y}%`;
      note.el.style.opacity = y > -4 && y < 112 ? "1" : "0";

      if (elapsed - note.time > BREAK) {
        miss(note);
      }
    }

    if (judged === chart.length) {
      finish();
      return;
    }

    animationId = requestAnimationFrame(frame);
  }

  function start() {
    reset();

    started = true;
    startTime = performance.now();

    showJudgement("START!", "normal");
    updateResult();

    animationId = requestAnimationFrame(frame);
  }

  window.addEventListener("keydown", (event) => {
    if (event.code === "Space") {
        event.preventDefault();

        if (!started) start();
        return;
    }

    const lane = KEYS.indexOf(event.key.toLowerCase());

    if (lane < 0 || event.repeat) return;

    event.preventDefault();

    keyEls[lane].classList.add("active");
    showHitEffect(lane);
    judge(lane);
  });

  window.addEventListener("keyup", (event) => {
    const lane = KEYS.indexOf(event.key.toLowerCase());

    if (lane >= 0) {
      keyEls[lane].classList.remove("active");
    }
  });

  reset();
</script>