---
layout: page
title: "Test"
subtitle: "리겜 테스트"
permalink: /rythm/
---

<div class="game">
    <div class="meta">
        <span>HAMSIN 4B MX · BPM 150</span>
        <span id="combo">COMBO 0 / 0</span>
    </div>
    <section class="stage" aria-label="4 line rhythm game">
        <div class="notes" id="notes"></div>
        <div class="judge-line"></div>
    </section>
    <div class="key-row" aria-hidden="true">
        <kbd>D</kbd><kbd>F</kbd><kbd>J</kbd><kbd>K</kbd>
    </div>
    <div class="result" id="result">PRESS START</div>
    <button id="start" type="button">START</button>
    <p class="hint">햄신 후살을 맛보세요</p>
</div>

<script>
    const BPM = 150;
    const BEAT = 60000 / BPM;       // 400 ms
    const SIXTEENTH = BEAT / 4;     // 100 ms
    const FALL_TIME = 700;
    const PERFECT = 52;
    const GOOD = 96;
    const keys = ["d", "f", "j", "k"];

    const lanesBySixteenth = [
        [0, 3], [0, 3], [0, 3], [0, 3],
        [1, 3], [1, 3], [1, 3], [1, 3],
        [1, 2], [1, 2], [1, 2], [1, 2],
        [0, 2], [0, 2], [0, 2], [0, 2]
    ];
    const chart = lanesBySixteenth.flatMap((lanes, index) =>
        lanes.map(lane => ({ time: FALL_TIME + index * SIXTEENTH, lane }))
    );
    const notesEl = document.querySelector("#notes");
    const comboEl = document.querySelector("#combo");
    const resultEl = document.querySelector("#result");
    const startButton = document.querySelector("#start");
    const keyEls = [...document.querySelectorAll("kbd")];
    let started = false, startTime = 0, combo = 0, judged = 0, animationId;

    function reset() {
        cancelAnimationFrame(animationId);
        notesEl.replaceChildren();
        combo = judged = 0;
        comboEl.textContent = `COMBO 0 / ${chart.length}`;
        resultEl.className = "result";
        resultEl.textContent = "READY";
        chart.forEach(note => {
        note.state = "waiting";
        note.el = document.createElement("div");
        note.el.className = `note lane-${note.lane} ${note.lane === 0 || note.lane === 3 ? "yellow" : "blue"}`;
        notesEl.append(note.el);
        });
    }

    function frame(now) {
        const elapsed = now - startTime;
        for (const note of chart) {
        if (note.state !== "waiting") continue;
        const delta = note.time - elapsed;
        const y = 96 - (delta / FALL_TIME) * 100;
        note.el.style.top = `${y}%`;
        note.el.style.opacity = y > -4 && y < 112 ? "1" : "0";
        if (elapsed - note.time > GOOD) miss(note);
        }
        if (judged < chart.length) animationId = requestAnimationFrame(frame);
        else finish();
    }

    function judge(lane) {
        if (!started) return;
        const elapsed = performance.now() - startTime;
        const candidates = chart.filter(n => n.lane === lane && n.state === "waiting");
        const note = candidates.sort((a, b) => Math.abs(a.time - elapsed) - Math.abs(b.time - elapsed))[0];
        if (!note || Math.abs(note.time - elapsed) > GOOD) return;
        const diff = Math.abs(note.time - elapsed);
        note.state = "hit";
        note.el.classList.add("hit");
        combo++; judged++;
        comboEl.textContent = `COMBO ${combo} / ${chart.length}`;
        resultEl.className = "result good";
        resultEl.textContent = diff <= PERFECT ? "PERFECT" : "GOOD";
    }

    function miss(note) {
        note.state = "miss";
        note.el.style.opacity = "0";
        combo = 0; judged++;
        comboEl.textContent = `COMBO 0 / ${chart.length}`;
        resultEl.className = "result bad";
        resultEl.textContent = "MISS";
    }

    function finish() {
        started = false;
        startButton.disabled = false;
        const fullCombo = chart.every(n => n.state === "hit");
        resultEl.className = `result ${fullCombo ? "good" : "bad"}`;
        resultEl.textContent = fullCombo ? "MAX COMBO!" : "FAILED";
    }

    function start() {
        reset();
        started = true;
        startTime = performance.now();
        startButton.disabled = true;
        resultEl.textContent = "START!";
        animationId = requestAnimationFrame(frame);
    }

    startButton.addEventListener("click", start);
    window.addEventListener("keydown", event => {
        const lane = keys.indexOf(event.key.toLowerCase());
        if (lane < 0 || event.repeat) return;
        event.preventDefault();
        keyEls[lane].classList.add("active");
        judge(lane);
    });
    window.addEventListener("keyup", event => {
        const lane = keys.indexOf(event.key.toLowerCase());
        if (lane >= 0) keyEls[lane].classList.remove("active");
    });
    reset();
</script>