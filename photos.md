---
layout: page
title: "사진"
permalink: /photos/
styles:
  - photos

photos:
  - file: cos_3.webp
    caption: "Tokyo, Japan · 2026"
    alt: C107에서 촬영한 메이드복을 입고 총을 든 코스어 유키

  - file: shomyo.webp
    caption: "Toyama, Japan · 2026"
    alt: 풀이 덮인 바위 사이를 내려오는 쇼묘폭포의 전경

  - file: seto.webp
    caption: "Ehime, Japan · 2026"
    alt: 페리에서 촬영한 사다미사키 등대와 푸른 하늘

  - file: fuji.webp
    caption: "Yamanashi, Japan · 2026"
    alt: 후지산을 배경으로 한 일주 사진

  - file: cos_1.webp
    caption: "Tokyo, Japan · 2026"
    alt: C106에서 촬영한 비스크돌 이콜라의 코스를 한 코스어 에나코

  - file: biei.webp
    caption: "Hokkaido, Japan · 2026"
    alt: 푸른색으로 소용돌이치는 강과 눈덮인 바위

  - file: nikko.webp
    caption: "Tochigi, Japan · 2026"
    alt: 높은 곳에서 떨어지는 폭포수와 눈이 내리는 풍경

  - file: positano.webp
    caption: "Positano, Italy · 2025"
    alt: 절벽 중간에 있는 색색의 건물들과 푸른 바다와 해수욕장

  - file: rome.webp
    caption: "Vatican City · 2025"
    alt: 성당 돔 중간의 창으로 들어오는 빛 줄기

  - file: bernesealps.webp
    caption: "Bernese Alps, Switzerland · 2025"
    alt: 푸른 하늘을 배경으로 한 높은 설산

  - file: eiger.webp
    caption: "Bernese Alps, Switzerland · 2025"
    alt: 초원을 달리는 산악 열차 위의 높은 절벽과 눈

  - file: skogafoss.webp
    caption: "Skogafoss, Iceland · 2025"
    alt: 넓고 높게 이끼가 쌓인 절벽 사이를 떨어지는 폭포

  - file: seljalandsfoss.webp
    caption: "Seljalandsfoss, Iceland · 2025"
    alt: 절벽에서 떨어지는 여러 폭포들

  - file: nagasaki.webp
    caption: "Nagasaki, Japan · 2024"
    alt: 산을 배경으로 작은 집과 두루미가 있는 호수의 흑백 풍경

  - file: oigawa.webp
    caption: "Shizuoka, Japan · 2024"
    alt: 푸른 강 위로 지나가는 철길
---

<ol class="photo-grid" aria-label="여행 사진 갤러리">
  {% for photo in page.photos %}
    {% assign thumbnail_path = '/assets/images/photos/thumbs/' | append: photo.file %}
    {% assign full_path = '/assets/images/photos/full/' | append: photo.file %}
    <li class="photo-item">
      <button
        class="photo-trigger"
        type="button"
        data-photo
        data-full="{{ full_path | relative_url }}"
        data-caption="{{ photo.caption | escape }}"
        aria-label="사진 크게 보기: {{ photo.alt | escape }}"
        >
        <img
            src="{{ thumbnail_path | relative_url }}"
            alt="{{ photo.alt | escape }}"
            width="720"
            height="720"
            decoding="async"
            {% if forloop.index > 3 %}loading="lazy"{% endif %}
        >
        </button>
        <span class="photo-description">
            {{ photo.caption | escape }}
        </span>
    </li>
  {% endfor %}
</ol>

<dialog
  class="photo-lightbox"
  id="photo-lightbox"
  aria-label="사진 크게 보기"
>
  <div class="photo-lightbox-inner">
    <button
      class="photo-lightbox-close"
      type="button"
      aria-label="사진 닫기"
    >
      ×
    </button>

    <button
      class="photo-lightbox-nav photo-lightbox-prev"
      type="button"
      aria-label="이전 사진"
    >
      ←
    </button>

    <figure class="photo-lightbox-figure">
      <img class="photo-lightbox-image" alt="">

      <figcaption class="photo-lightbox-caption" aria-live="polite">
        <span id="photo-lightbox-caption"></span>
        <span class="photo-lightbox-count"></span>
      </figcaption>
    </figure>

    <button
      class="photo-lightbox-nav photo-lightbox-next"
      type="button"
      aria-label="다음 사진"
    >
      →
    </button>
  </div>
</dialog>

<script>
(() => {
  const dialog = document.querySelector("#photo-lightbox");
  const triggers = [...document.querySelectorAll("[data-photo]")];

  if (!dialog || triggers.length === 0) return;

  const image = dialog.querySelector(".photo-lightbox-image");
  const caption = dialog.querySelector("#photo-lightbox-caption");
  const count = dialog.querySelector(".photo-lightbox-count");
  const closeButton = dialog.querySelector(".photo-lightbox-close");
  const previousButton = dialog.querySelector(".photo-lightbox-prev");
  const nextButton = dialog.querySelector(".photo-lightbox-next");

  let currentIndex = 0;
  let lastFocusedElement = null;
  let touchStartX = 0;
  let touchStartY = 0;

  function getPhoto(index) {
    const trigger = triggers[index];
    const thumbnail = trigger.querySelector("img");

    return {
      src: trigger.dataset.full || thumbnail.currentSrc || thumbnail.src,
      alt: thumbnail.alt,
      caption: trigger.dataset.caption || ""
    };
  }

  function renderPhoto() {
    const photo = getPhoto(currentIndex);

    image.src = photo.src;
    image.alt = photo.alt;
    caption.textContent = photo.caption;
    count.textContent = `${currentIndex + 1} / ${triggers.length}`;
  }

  function showPhoto(index) {
    currentIndex =
      (index + triggers.length) % triggers.length;

    renderPhoto();
  }

  function openPhoto(index, trigger) {
    currentIndex = index;
    lastFocusedElement = trigger;

    renderPhoto();
    dialog.showModal();
    document.body.classList.add("photo-lightbox-open");
    closeButton.focus();
  }

  function closePhoto() {
    dialog.close();
  }

  triggers.forEach((trigger, index) => {
    trigger.addEventListener("click", () => {
      openPhoto(index, trigger);
    });
  });

  closeButton.addEventListener("click", closePhoto);
  previousButton.addEventListener("click", () => showPhoto(currentIndex - 1));
  nextButton.addEventListener("click", () => showPhoto(currentIndex + 1));

  dialog.addEventListener("keydown", (event) => {
    if (event.key === "ArrowLeft") {
      event.preventDefault();
      showPhoto(currentIndex - 1);
    }

    if (event.key === "ArrowRight") {
      event.preventDefault();
      showPhoto(currentIndex + 1);
    }

    if (event.key === "Home") {
      event.preventDefault();
      showPhoto(0);
    }

    if (event.key === "End") {
      event.preventDefault();
      showPhoto(triggers.length - 1);
    }
  });

  dialog.addEventListener("click", (event) => {
    const clickedContent = event.target.closest(
      ".photo-lightbox-figure, .photo-lightbox-close, .photo-lightbox-nav"
    );

    if (!clickedContent) {
      closePhoto();
    }
  });

  image.addEventListener(
    "touchstart",
    (event) => {
      const touch = event.changedTouches[0];

      touchStartX = touch.clientX;
      touchStartY = touch.clientY;
    },
    { passive: true }
  );

  image.addEventListener(
    "touchend",
    (event) => {
      const touch = event.changedTouches[0];
      const differenceX = touch.clientX - touchStartX;
      const differenceY = touch.clientY - touchStartY;

      const isHorizontalSwipe =
        Math.abs(differenceX) > 50 &&
        Math.abs(differenceX) > Math.abs(differenceY);

      if (!isHorizontalSwipe) return;

      showPhoto(
        differenceX < 0
          ? currentIndex + 1
          : currentIndex - 1
      );
    },
    { passive: true }
  );

  dialog.addEventListener("close", () => {
    document.body.classList.remove("photo-lightbox-open");
    image.removeAttribute("src");
    lastFocusedElement?.focus();
  });
})();
</script>
