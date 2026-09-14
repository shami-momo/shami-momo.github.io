---
layout: page
title: "Photos"
permalink: /photos/
styles:
  - photos
image: /assets/images/photos/biei.webp

photos:
  - file: cos_3.webp
    caption: "Tokyo, Japan · 2026"

  - file: shomyo.webp
    caption: "Toyama, Japan · 2026"

  - file: kamikochi.webp
    caption: "Toyama, Japan · 2026"

  - file: nagoya.webp
    caption: "Nagoya, Japan · 2026"

  - file: seto.webp
    caption: "Ehime, Japan · 2026"

  - file: fuji.webp
    caption: "Yamanashi, Japan · 2026"

  - file: cos_1.webp
    caption: "Tokyo, Japan · 2026"

  - file: biei.webp
    caption: "Hokkaido, Japan · 2026"

  - file: nikko.webp
    caption: "Tochigi, Japan · 2026"

  - file: illustar.webp
    caption: "Ilsan, Korea · 2025"

  - file: positano.webp
    caption: "Positano, Italy · 2025"

  - file: rome.webp
    caption: "Rome, Italy · 2025"

  - file: bernesealps.webp
    caption: "Bernese Alps, Switzerland · 2025"

  - file: eiger.webp
    caption: "Bernese Alps, Switzerland · 2025"

  - file: skogafoss.webp
    caption: "Skogafoss, Iceland · 2025"

  - file: seljalandsfoss.webp
    caption: "Seljalandsfoss, Iceland · 2025"

  - file: nagasaki.webp
    caption: "Nagasaki, Japan · 2024"

  - file: oigawa.webp
    caption: "Shizuoka, Japan · 2024"
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

  /*
    dialog를 지원하지 않는 구형 브라우저에서는
    사진 파일을 직접 연다.
  */
  if (typeof dialog.showModal !== "function") {
    triggers.forEach((trigger) => {
      trigger.addEventListener("click", () => {
        window.location.assign(trigger.dataset.full);
      });
    });

    return;
  }

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

  function preloadAdjacentPhotos() {
    [-1, 1].forEach((offset) => {
      const index =
        (currentIndex + offset + triggers.length) % triggers.length;

      const preload = new Image();
      preload.src = getPhoto(index).src;
    });
  }

  function renderPhoto() {
    const photo = getPhoto(currentIndex);

    image.src = photo.src;
    image.alt = photo.alt;
    caption.textContent = photo.caption;
    count.textContent = `${currentIndex + 1} / ${triggers.length}`;

    preloadAdjacentPhotos();
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