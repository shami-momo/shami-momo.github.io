---
layout: page
title: "리듬게임"
permalink: /rythm/
styles: [rhythm]
robots: "noindex, nofollow"
---

<p class="game-help">게임 시작 버튼을 눌러 시작, <kbd>Space</kbd>를 눌러 재시작</p>

<div class="game-layout">
  <div class="play-area">
    <div class="meta">HAMSIN 4B MX · BPM 150</div>
    <section
      class="stage"
      id="gameStage"
      tabindex="-1"
      aria-label="4키 리듬게임 플레이 영역"
    >
      <div class="notes" id="notes"></div>
      <div class="hit-effects" id="hitEffects"></div>
      <div class="judge-line"></div>
      <div class="judgement" id="judgement"></div>
    </section>

    <div class="game-controls" aria-label="리듬게임 입력 버튼">
      <button class="game-key" type="button" data-lane="0" aria-label="왼쪽 첫 번째 레인">S</button>
      <button class="game-key" type="button" data-lane="1" aria-label="왼쪽 두 번째 레인">D</button>
      <button class="game-key" type="button" data-lane="2" aria-label="오른쪽 두 번째 레인">;</button>
      <button class="game-key" type="button" data-lane="3" aria-label="오른쪽 첫 번째 레인">'</button>
    </div>

    <button class="game-start" id="gameStart" type="button">게임 시작</button>
  </div>

  <aside class="result" id="result" aria-label="판정 결과"></aside>
  <p class="visually-hidden" id="gameStatus" aria-live="polite"></p>
</div>

<script>
  const BPM = 150;
  const SIXTEENTH = 60000 / BPM / 4;
  const lanesBySixteenth = [
    [0, 2], [0, 2], [0, 2], [0, 2],
    [1, 2], [1, 2], [1, 2], [1, 2],
    [1, 3], [1, 3], [1, 3], [1, 3],
    [0, 3], [0, 3], [0, 3], [0, 3]
  ];

  const FALL_TIME = 600;
  const MAX_100 = 42;
  const BREAK = 180;
  const KEY_CODES = ["KeyS", "KeyD", "Semicolon", "Quote"];
  const JUDGEMENTS = [100, 90, 80, 70, 60, 50, 40, 30, 20, 10];

  const chart = lanesBySixteenth.flatMap((lanes, index) =>
    lanes.map((lane) => ({
      lane,
      time: FALL_TIME + index * SIXTEENTH
    }))
  );

  const gameRoot = document.querySelector(".game-layout");
  const stageEl = document.querySelector("#gameStage");
  const notesEl = document.querySelector("#notes");
  const hitEffectsEl = document.querySelector("#hitEffects");
  const judgementEl = document.querySelector("#judgement");
  const resultEl = document.querySelector("#result");
  const statusEl = document.querySelector("#gameStatus");
  const startButton = document.querySelector("#gameStart");
  const keyEls = [...document.querySelectorAll(".game-key")];

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
    const state = finished
      ? (fullCombo ? "MAX COMBO!" : "FINISHED")
      : (started ? "PLAYING" : "READY");

    const breakdown = JUDGEMENTS.map((percent) => `
      <div class="judge-count-row">
        <span>MAX ${percent}%</span>
        <span>${judgeCounts[percent]}</span>
      </div>
    `).join("");

    resultEl.innerHTML = `
      <span class="judge-title">${state}</span>
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
    judgeCounts = Object.fromEntries(JUDGEMENTS.map((percent) => [percent, 0]));
    notesEl.replaceChildren();
    hitEffectsEl.replaceChildren();

    for (const note of chart) {
      note.state = "waiting";
      note.el = document.createElement("div");
      note.el.className = [
        "note",
        `lane-${note.lane}`,
        note.lane === 0 || note.lane === 3 ? "yellow" : "blue"
      ].join(" ");
      notesEl.append(note.el);
    }

    updateResult();
  }

  function findNearestNote(lane, time) {
    let nearest = null;
    for (const note of chart) {
      if (note.lane !== lane || note.state !== "waiting") continue;
      if (!nearest || Math.abs(note.time - time) < Math.abs(nearest.time - time)) {
        nearest = note;
      }
    }
    return nearest;
  }

  function miss(note) {
    note.state = "miss";
    note.el.style.opacity = "0";
    judged += 1;
    breakCount += 1;
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
    judged += 1;
    totalPercent += maxPercent;
    judgeCounts[maxPercent] += 1;

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
    effect.addEventListener("animationend", () => effect.remove(), { once: true });
    hitEffectsEl.append(effect);
  }

  function setKeyActive(lane, active) {
    keyEls[lane]?.classList.toggle("active", active);
  }

  function pressLane(lane) {
    setKeyActive(lane, true);
    showHitEffect(lane);
    judge(lane);
  }

  function finish() {
    started = false;
    startButton.textContent = "다시 시작";
    statusEl.textContent = `게임 종료. 정확도 ${(totalPercent / judged).toFixed(2)}%, 브레이크 ${breakCount}회.`;
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
      if (elapsed - note.time > BREAK) miss(note);
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
    startButton.textContent = "재시작";
    statusEl.textContent = "게임이 시작됐습니다.";
    showJudgement("START!", "normal");
    updateResult();
    stageEl.focus();
    animationId = requestAnimationFrame(frame);
  }

  startButton.addEventListener("click", start);

  keyEls.forEach((button) => {
    const lane = Number(button.dataset.lane);
    button.addEventListener("pointerdown", (event) => {
      event.preventDefault();
      pressLane(lane);
    });
    ["pointerup", "pointercancel", "pointerleave"].forEach((type) => {
      button.addEventListener(type, () => setKeyActive(lane, false));
    });
  });

  window.addEventListener("keydown", (event) => {
    if (!gameRoot.contains(document.activeElement)) return;
    if (event.target.closest("button, input, textarea, select, a")) return;

    if (event.code === "Space") {
      event.preventDefault();
      start();
      return;
    }

    const lane = KEY_CODES.indexOf(event.code);
    if (lane < 0 || event.repeat) return;
    event.preventDefault();
    pressLane(lane);
  });

  window.addEventListener("keyup", (event) => {
    const lane = KEY_CODES.indexOf(event.code);
    if (lane >= 0) setKeyActive(lane, false);
  });

  window.addEventListener("blur", () => {
    keyEls.forEach((_, lane) => setKeyActive(lane, false));
  });

  reset();
</script>
