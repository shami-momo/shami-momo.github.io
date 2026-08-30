---
layout: page
title: "오타쿠"
subtitle: "쓸데없는 내용"
permalink: /anime/
---

## 디맥 성과
<div id="djmax" class="djmax-result" aria-live="polite">
  성과 정보를 불러오는 중…
</div>

## 애니 추천
<div class="page">
  <div class="toolbar">
    <div class="filter-list" aria-label="작품 종류 필터">
      <button class="filter-button" type="button" data-filter="all" aria-pressed="true">전체</button>
      <button class="filter-button" type="button" data-filter="series" aria-pressed="false">TVA</button>
      <button class="filter-button" type="button" data-filter="movie" aria-pressed="false">극장판</button>
    </div>
    <span id="anime-count" class="count"></span>
  </div>

  <div id="anime-list"></div>
</div>

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

<script id="anime-data" type="text/yaml">
- year: 2026
  season: "2분기"
  title: "카미이나 보탄, 취한 모습은 백합의 꽃"
- year: 2026
  season: "극장판"
  title: "초(超) 가구야 공주!"
- year: 2025
  season: "3분기"
  title: "타코피의 원죄"
- year: 2025
  season: "3분기"
  title: "루리의 보석"
- year: 2025
  season: "2분기"
  title: "흘러가는 나날, 밥은 맛있어"
- year: 2025
  season: "2분기"
  title: "mono"
- year: 2025
  season: "1분기"
  title: "BanG Dream! Ave Mujica"
- year: 2025
  season: "1분기"
  title: "메달리스트"
- year: 2025
  season: "극장판"
  title: "좀비 랜드 사가 유메긴가 파라다이스"
- year: 2024
  season: "극장판"
  title: "우마무스메 PRETTY DERBY 새로운 시대의 문"
- year: 2024
  season: "극장판"
  title: "룩 백"
- year: 2024
  season: "극장판"
  title: "극장판 BanG Dream! It's MyGO!!!!! 후편: 노래하자, 우리가 될 수 있는 노래 & FILM LIVE"
- year: 2024
  season: "극장판"
  title: "극장판 BanG Dream! It's MyGO!!!!! 전편: 봄의 양지, 방황하는 고양이"
- year: 2024
  season: "3분기"
  title: "패배 히로인이 너무 많아!"
- year: 2024
  season: "2분기"
  title: "괴짜의 샐러드 볼"
- year: 2024
  season: "2분기"
  title: "유루캠△ 3"
- year: 2024
  season: "2분기"
  title: "울려라! 유포니엄 3"
- year: 2024
  season: "2분기"
  title: "걸즈 밴드 크라이"
- year: 2023
  season: "4분기"
  title: "별무리 텔레패스"
- year: 2023
  season: "3분기"
  title: "장송의 프리렌"
- year: 2023
  season: "3분기"
  title: "BanG Dream! It's MyGO!!!!!"
- year: 2023
  season: "2분기"
  title: "아이돌 마스터 신데렐라 걸즈 U149"
- year: 2023
  season: "2분기"
  title: "【최애의 아이】"
- year: 2023
  season: "1분기"
  title: "전생 왕녀와 천재 영애의 마법 혁명"
- year: 2022
  season: "4분기"
  title: "기동전사 건담 수성의 마녀"
- year: 2022
  season: "4분기"
  title: "봇치 더 락!"
- year: 2022
  season: "2분기"
  title: "길모퉁이 마족 2번가"
- year: 2021
  season: "3분기"
  title: "하얀 모래의 아쿠아톱"
- year: 2021
  season: "2분기"
  title: "슈퍼 커브"
- year: 2021
  season: "2분기"
  title: "좀비 랜드 사가 리벤지"
- year: 2021
  season: "2분기"
  title: "86 -에이티식스-"
- year: 2021
  season: "1분기"
  title: "유루캠△ 2"
- year: 2021
  season: "극장판"
  title: "극장판 소녀☆가극 레뷰 스타라이트"
- year: 2020
  season: "4분기"
  title: "마녀의 여행"
- year: 2020
  season: "4분기"
  title: "러브 라이브! 니지가사키 학원 스쿨 아이돌 동호회"
- year: 2020
  season: "3분기"
  title: "Re: 제로부터 시작하는 이세계 생활 2기"
- year: 2020
  season: "3분기"
  title: "역시 내 청춘의 러브 코미디는 잘못됐다. 완"
- year: 2020
  season: "1분기"
  title: "어떤 과학의 초전자포 T"
- year: 2019
  season: "3분기"
  title: "길모퉁이 마족"
- year: 2019
  season: "극장판"
  title: "날씨의 아이"
- year: 2018
  season: "4분기"
  title: "좀비 랜드 사가"
- year: 2018
  season: "4분기"
  title: "청춘 돼지는 바니걸 선배의 꿈을 꾸지 않는다"
- year: 2018
  season: "4분기"
  title: "이윽고 네가 된다"
- year: 2018
  season: "3분기"
  title: "소녀☆가극 레뷰 스타라이트"
- year: 2018
  season: "1분기"
  title: "유루캠△"
- year: 2018
  season: "극장판"
  title: "리즈와 파랑새"
- year: 2017
  season: "4분기"
  title: "메이드 인 어비스"
- year: 2017
  season: "4분기"
  title: "소녀 종말 여행"
- year: 2017
  season: "3분기"
  title: "NEW GAME!!"
- year: 2017
  season: "2분기"
  title: "종말에 뭐 하세요? 바쁘세요? 구해 주실 수 있나요?"
- year: 2017
  season: "1분기"
  title: "케모노 프렌즈"
- year: 2017
  season: "극장판"
  title: "노 게임 노 라이프 제로"
- year: 2016
  season: "4분기"
  title: "울려라! 유포니엄 2"
- year: 2016
  season: "4분기"
  title: "하이 스쿨 플릿"
- year: 2016
  season: "3분기"
  title: "NEW GAME!"
- year: 2016
  season: "2분기"
  title: "Re: 제로부터 시작하는 이세계 생활"
- year: 2016
  season: "극장판"
  title: "너의 이름은."
- year: 2015
  season: "3분기"
  title: "학교생활!"
- year: 2015
  season: "2분기"
  title: "울려라! 유포니엄"
- year: 2015
  season: "1분기"
  title: "유리쿠마 아라시"
- year: 2014
  season: "4분기"
  title: "아마기 브릴리언트 파크"
- year: 2013
  season: "극장판"
  title: "언어의 정원"
- year: 2012
  season: "3분기"
  title: "인류는 쇠퇴했습니다"
- year: 2010
  season: "2분기"
  title: "케이온!!"
- year: 2009
  season: "4분기"
  title: "어떤 과학의 초전자포"
- year: 2009
  season: "2분기"
  title: "케이온!"
- year: "2008년 이전"
  season: "극장판"
  title: "안녕 은하철도 999: 안드로메다 종착"
- year: "2008년 이전"
  season: "극장판"
  title: "천공의 성 라퓨타"
  </script>

  <script>
    const yaml = document.querySelector("#anime-data").textContent.trim();
    const target = document.querySelector("#anime-list");
    const count = document.querySelector("#anime-count");
    const filters = [...document.querySelectorAll("[data-filter]")];

    function parseYamlList(source) {
      const entries = [];
      let current = null;

      source.split("\n").forEach((line) => {
        if (line.startsWith("- ")) {
          if (current) entries.push(current);
          current = {};
          line = line.slice(2);
        }

        const separator = line.indexOf(":");
        if (!current || separator === -1) return;

        const key = line.slice(0, separator).trim();
        const rawValue = line.slice(separator + 1).trim();
        current[key] = JSON.parse(rawValue);
      });

      if (current) entries.push(current);
      return entries;
    }

    const anime = parseYamlList(yaml).map((item) => ({
      ...item,
      type: item.season === "극장판" ? "movie" : "series",
    }));

    function createCard(item) {
      const movieClass = item.type === "movie" ? " is-movie" : "";

      return `
        <li class="anime-card">
          <span class="season${movieClass}">${item.season}</span>
          <span class="anime-title">${item.title}</span>
        </li>`;
    }

    function render(filter = "all") {
      const visible = anime.filter((item) => filter === "all" || item.type === filter);
      const groups = new Map();

      visible.forEach((item) => {
        const items = groups.get(item.year) ?? [];
        items.push(item);
        groups.set(item.year, items);
      });

      target.innerHTML = [...groups]
        .map(([year, items]) => `
          <section class="year-section">
            <span class="year">${year}</span>
            <span class="anime-list">${items.map(createCard).join("")}</span>
          </section>`)
        .join("") || '<p class="empty">해당하는 작품이 없어.</p>';

      count.textContent = `${visible.length}개 작품`;
    }

    filters.forEach((button) => {
      button.addEventListener("click", () => {
        filters.forEach((item) => item.setAttribute("aria-pressed", "false"));
        button.setAttribute("aria-pressed", "true");
        render(button.dataset.filter);
      });
    });

    render();
  </script>