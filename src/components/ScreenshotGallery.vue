<template>
  <button v-if="shots.length" type="button" class="trigger" @click="open(0)">
    Screenshots
  </button>

  <dialog
    ref="dialogEl"
    class="gallery"
    aria-label="Screenshots"
    @close="onClose"
    @click="onBackdropClick"
    @keydown.left.prevent="step(-1)"
    @keydown.right.prevent="step(1)"
  >
    <div class="gallery_inner">
      <div class="gallery_bar">
        <span class="gallery_count" aria-live="polite">
          {{ index + 1 }} / {{ shots.length }}
        </span>
        <button
          type="button"
          class="gallery_close"
          aria-label="Close"
          @click="close"
        >
          <svg viewBox="0 0 7 7" aria-hidden="true">
            <path
              d="M0 0h1v1H0zM1 1h1v1H1zM2 2h1v1H2zM3 3h1v1H3zM4 4h1v1H4zM5 5h1v1H5zM6 6h1v1H6zM6 0h1v1H6zM5 1h1v1H5zM4 2h1v1H4zM2 4h1v1H2zM1 5h1v1H1zM0 6h1v1H0z"
            />
          </svg>
        </button>
      </div>

      <div
        class="gallery_stage"
        @touchstart.passive="onTouchStart"
        @touchend.passive="onTouchEnd"
      >
        <button
          v-if="shots.length > 1"
          type="button"
          class="gallery_nav gallery_nav--prev"
          aria-label="Previous screenshot"
          @click="step(-1)"
        >
          <svg viewBox="0 0 4 7" aria-hidden="true">
            <path
              d="M3 0h1v1H3zM2 1h1v1H2zM1 2h1v1H1zM0 3h1v1H0zM1 4h1v1H1zM2 5h1v1H2zM3 6h1v1H3z"
            />
          </svg>
        </button>

        <img
          :key="shots[index]"
          class="gallery_image"
          :src="shots[index]"
          :alt="`Heroes of Crimson screenshot ${index + 1}`"
        />

        <button
          v-if="shots.length > 1"
          type="button"
          class="gallery_nav gallery_nav--next"
          aria-label="Next screenshot"
          @click="step(1)"
        >
          <svg viewBox="0 0 4 7" aria-hidden="true">
            <path
              d="M0 0h1v1H0zM1 1h1v1H1zM2 2h1v1H2zM3 3h1v1H3zM2 4h1v1H2zM1 5h1v1H1zM0 6h1v1H0z"
            />
          </svg>
        </button>
      </div>

      <div v-if="shots.length > 1" class="gallery_thumbs">
        <button
          v-for="(src, i) in shots"
          :key="src"
          type="button"
          class="gallery_thumb"
          :class="{ 'is-active': i === index }"
          :aria-label="`Show screenshot ${i + 1}`"
          :aria-current="i === index ? 'true' : undefined"
          @click="index = i"
        >
          <img :src="src" alt="" loading="lazy" />
        </button>
      </div>
    </div>
  </dialog>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";

const MAX_SCREENSHOTS = 50;

const shots = ref<string[]>([]);
const index = ref(0);
const dialogEl = ref<HTMLDialogElement | null>(null);
let touchX: number | null = null;

async function exists(src: string) {
  try {
    const res = await fetch(src, { method: "HEAD", cache: "no-cache" });
    const type = res.headers.get("content-type") ?? "";
    return res.ok && type.startsWith("image/");
  } catch {
    return false;
  }
}

onMounted(async () => {
  const found: string[] = [];
  for (let i = 1; i <= MAX_SCREENSHOTS; i++) {
    const src = `/${i}.png`;
    if (!(await exists(src))) break;
    found.push(src);
  }
  shots.value = found;
});

function open(i: number) {
  index.value = i;
  dialogEl.value?.showModal();
  document.documentElement.style.overflow = "hidden";
}

function close() {
  dialogEl.value?.close();
}

function onClose() {
  document.documentElement.style.overflow = "";
}

function step(dir: number) {
  const n = shots.value.length;
  index.value = (index.value + dir + n) % n;
}

function onBackdropClick(e: MouseEvent) {
  if (e.target === dialogEl.value) close();
}

function onTouchStart(e: TouchEvent) {
  touchX = e.changedTouches[0]?.clientX ?? null;
}

function onTouchEnd(e: TouchEvent) {
  const endX = e.changedTouches[0]?.clientX;
  if (touchX === null || endX === undefined) return;
  const dx = endX - touchX;
  touchX = null;
  if (Math.abs(dx) > 40) step(dx < 0 ? 1 : -1);
}
</script>

<style lang="scss" scoped>
$sky: #6c2c52;
$sky-4: #37162d;
$night: #02050e;
$cream: #f5d7bf;
$crimson-hi: #f04a42;
$crimson: #dc2330;
$ridge: #2d3a78;

$px: 4px;

@mixin notched {
  clip-path: polygon(
    0 $px * 2,
    $px $px * 2,
    $px $px,
    $px * 2 $px,
    $px * 2 0,
    calc(100% - #{$px * 2}) 0,
    calc(100% - #{$px * 2}) $px,
    calc(100% - #{$px}) $px,
    calc(100% - #{$px}) $px * 2,
    100% $px * 2,
    100% calc(100% - #{$px * 2}),
    calc(100% - #{$px}) calc(100% - #{$px * 2}),
    calc(100% - #{$px}) calc(100% - #{$px}),
    calc(100% - #{$px * 2}) calc(100% - #{$px}),
    calc(100% - #{$px * 2}) 100%,
    $px * 2 100%,
    $px * 2 calc(100% - #{$px}),
    $px calc(100% - #{$px}),
    $px calc(100% - #{$px * 2}),
    0 calc(100% - #{$px * 2})
  );
}

@mixin pixel-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  border: 0;
  cursor: pointer;
  font-family: var(--font-pixel);
  color: $cream;
  background: $sky-4;
  box-shadow: inset 0 0 0 $px rgba($cream, 0.35);
  @include notched;
  transition: transform 80ms steps(2);

  &:hover {
    box-shadow: inset 0 0 0 $px $cream;
  }

  &:focus-visible {
    outline: none;
    box-shadow: inset 0 0 0 $px $crimson-hi;
  }

  svg {
    display: block;
    fill: currentColor;
    shape-rendering: crispEdges;
  }
}

.trigger {
  padding: 0;
  border: 0;
  background: none;
  cursor: pointer;
  font-family: var(--font-pixel);
  font-size: clamp(20px, 3vh, 28px);
  line-height: 1.1;
  color: $cream;
  text-decoration: underline;
  text-decoration-thickness: 2px;
  text-underline-offset: 5px;
  text-decoration-color: $crimson;

  &:hover {
    color: $crimson-hi;
  }

  &:focus-visible {
    outline: $px solid $cream;
    outline-offset: $px;
  }
}

.gallery {
  width: 100vw;
  max-width: none;
  height: 100vh;
  height: 100dvh;
  max-height: none;
  margin: 0;
  padding: 0;
  border: 0;
  color: $cream;
  background: transparent;
  font-family: var(--font-pixel);

  &::backdrop {
    background: rgba($night, 0.92);
  }

  &[open] {
    animation: gallery-in 0.25s steps(4) both;
  }

  &_inner {
    display: grid;
    grid-template-rows: auto minmax(0, 1fr) auto;
    gap: 12px;
    height: 100%;
    padding: 16px clamp(12px, 3vw, 40px) 20px;
    box-sizing: border-box;
    pointer-events: none;

    > * {
      pointer-events: auto;
    }
  }

  &_bar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: min(100%, 1400px);
    margin: 0 auto;
  }

  &_count {
    font-size: 28px;
    line-height: 1;
    color: rgba($cream, 0.8);
  }

  &_close {
    @include pixel-button;
    width: 48px;
    height: 48px;

    svg {
      width: 21px;
      height: 21px;
    }
  }

  &_stage {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 0;
    width: min(100%, 1400px);
    margin: 0 auto;
    padding: 0 68px;
    box-sizing: border-box;
    pointer-events: none;

    > * {
      pointer-events: auto;
    }
  }

  &_image {
    display: block;
    max-width: 100%;
    max-height: 100%;
    object-fit: contain;
    image-rendering: pixelated;
    box-shadow: 0 0 0 $px $ridge;
    animation: shot-in 0.2s steps(3) both;
  }

  &_nav {
    @include pixel-button;
    position: absolute;
    z-index: 1;
    top: 50%;
    width: 52px;
    height: 72px;
    transform: translateY(-50%);

    svg {
      width: 16px;
      height: 28px;
    }

    &--prev {
      left: 0;
    }

    &--next {
      right: 0;
    }
  }

  &_thumbs {
    display: flex;
    gap: 10px;
    justify-content: safe center;
    overflow-x: auto;
    width: min(100%, 1400px);
    margin: 0 auto;
    padding: $px;
    scrollbar-width: thin;
    scrollbar-color: $ridge transparent;
  }

  &_thumb {
    flex: none;
    width: 96px;
    aspect-ratio: 16 / 9;
    padding: 0;
    border: 0;
    cursor: pointer;
    background: $sky-4;
    opacity: 0.55;
    box-shadow: 0 0 0 2px $ridge;
    transition: opacity 80ms steps(2);

    img {
      display: block;
      width: 100%;
      height: 100%;
      object-fit: cover;
      image-rendering: pixelated;
    }

    &:hover {
      opacity: 0.85;
    }

    &.is-active {
      opacity: 1;
      box-shadow: 0 0 0 $px $crimson;
    }

    &:focus-visible {
      outline: $px solid $cream;
      outline-offset: 2px;
    }
  }
}

@keyframes gallery-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes shot-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@media (max-width: 600px) {
  .gallery_stage {
    padding: 0;
  }

  .gallery_nav--prev {
    left: 8px;
  }

  .gallery_nav--next {
    right: 8px;
  }

  .gallery_nav {
    width: 40px;
    height: 56px;
  }

  .gallery_thumb {
    width: 72px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .trigger,
  .gallery[open],
  .gallery_image {
    animation: none;
    transition: none;
  }
}
</style>
