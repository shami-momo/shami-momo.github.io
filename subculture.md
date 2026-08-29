---
layout: page
title: "Subculture"
subtitle: "쓸데없는 내용"
permalink: /subculture/
---

<style>
  .subculture-section {
    margin-top: 1.5rem;
  }

  .djmax-profile {
    border: 1px solid var(--line-soft);
    border-radius: 1rem;
    overflow: hidden;
    background: var(--bg);
  }

  .djmax-profile-header {
    display: flex;
    align-items: baseline;
    justify-content: space-between;
    gap: 1rem;
    padding: 1.25rem 1.35rem;
    border-bottom: 1px solid var(--line-soft);
    background: var(--highlight-soft);
  }

  .djmax-profile-header h2 {
    margin: 0;
    font-size: 1.1rem;
  }

  .djmax-profile-header p {
    margin: 0;
    color: var(--muted);
    font-size: 0.9rem;
  }

  .djmax-grid {
    display: grid;
    grid-template-columns: repeat(4, minmax(0, 1fr));
  }

  .djmax-mode {
    padding: 1.25rem;
    border-right: 1px solid var(--line-soft);
  }

  .djmax-mode:last-child {
    border-right: 0;
  }

  .djmax-mode h3 {
    margin: 0 0 1rem;
    color: var(--muted);
    font-size: 0.85rem;
    letter-spacing: 0.08em;
  }

  .djmax-label {
    display: block;
    margin-top: 0.85rem;
    color: var(--muted);
    font-size: 0.75rem;
    font-weight: 700;
    letter-spacing: 0.08em;
  }

  .djmax-value {
    display: block;
    margin-top: 0.2rem;
    font-size: 1.05rem;
    font-weight: 800;
    overflow-wrap: anywhere;
  }

  .djmax-status {
    margin: 0;
    padding: 0.9rem 1.35rem;
    border-top: 1px solid var(--line-soft);
    color: var(--muted);
    font-size: 0.85rem;
  }

  .djmax-status.is-error {
    color: #c24a4a;
  }

  @media (max-width: 700px) {
    .djmax-grid {
      grid-template-columns: repeat(2, minmax(0, 1fr));
    }

    .djmax-mode:nth-child(2) {
      border-right: 0;
    }

    .djmax-mode:nth-child(-n + 2) {
      border-bottom: 1px solid var(--line-soft);
    }
  }
</style>

<section class="subculture-section" aria-labelledby="djmax-title">
  <div class="djmax-profile">
    <header class="djmax-profile-header">
      <div>
        <h2 id="djmax-title">DJMAX RESPECT V</h2>
        <p>V-ARCHIVE profile</p>
      </div>
      <strong>saika</strong>
    </header>

    <div id="djmax-grid" class="djmax-grid" aria-live="polite"></div>
    <p id="djmax-status" class="djmax-status">성과 정보를 불러오는 중…</p>
  </div>
</section>

<script>
  (() => {
    const nickname = "saika";
    const modes = ["4B", "5B", "6B", "8B"];
    const apiBase = "https://v-archive.net/api/v2/archive";
    const grid = document.querySelector("#djmax-grid");
    const status = document.querySelector("#djmax-status");

    function getDisplayValue(data, keys) {
      for (const key of keys) {
        const value = key.split(".").reduce((result, part) => result?.[part], data);

        if (value !== undefined && value !== null && value !== "") {
          return String(value);
        }
      }

      return "—";
    }

    async function getModeData(mode) {
      const user = encodeURIComponent(nickname);
      const [djClassResult, tierResult] = await Promise.allSettled([
        fetch(`${apiBase}/${user}/djClass/${mode}`).then((response) => {
          if (!response.ok) throw new Error(`DJ CLASS ${response.status}`);
          return response.json();
        }),
        fetch(`${apiBase}/${user}/tier/${mode}`).then((response) => {
          if (!response.ok) throw new Error(`Tier ${response.status}`);
          return response.json();
        }),
      ]);

      return {
        mode,
        djClass: djClassResult.status === "fulfilled"
          ? getDisplayValue(djClassResult.value, ["djClass", "djClassName", "class"])
          : "조회 실패",
        tier: tierResult.status === "fulfilled"
          ? getDisplayValue(tierResult.value, ["tier", "tierName", "name", "tier.name"])
          : "조회 실패",
        failed: djClassResult.status === "rejected" || tierResult.status === "rejected",
      };
    }

    function renderMode({ mode, djClass, tier }) {
      return `
        <article class="djmax-mode">
          <h3>${mode}</h3>
          <span class="djmax-label">DJ CLASS</span>
          <strong class="djmax-value">${djClass}</strong>
          <span class="djmax-label">TIER</span>
          <strong class="djmax-value">${tier}</strong>
        </article>`;
    }

    Promise.all(modes.map(getModeData))
      .then((results) => {
        grid.innerHTML = results.map(renderMode).join("");

        if (results.some((result) => result.failed)) {
          status.textContent = "일부 성과 정보를 불러오지 못했어.";
          status.classList.add("is-error");
        } else {
          status.textContent = "V-ARCHIVE에서 실시간으로 조회했어.";
        }
      })
      .catch(() => {
        status.textContent = "성과 정보를 불러오지 못했어.";
        status.classList.add("is-error");
      });
  })();
</script>
