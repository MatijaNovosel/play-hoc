<template>
  <button
    v-if="loading || shots.length"
    type="button"
    class="trigger"
    :disabled="loading"
    :aria-busy="loading"
    @click="open(0)"
  >
    Screenshots
    <span v-if="loading" class="trigger_loader" aria-hidden="true">
      <i /><i /><i />
    </span>
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
        <button
          type="button"
          class="gallery_close"
          aria-label="Close"
          @click="close"
        >
          <svg viewBox="0 0 24 24" aria-hidden="true">
            <path d="M6 6l12 12M18 6L6 18" />
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
          <svg viewBox="0 0 24 24" aria-hidden="true">
            <path d="M15 5l-7 7 7 7" />
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
          <svg viewBox="0 0 24 24" aria-hidden="true">
            <path d="M9 5l7 7-7 7" />
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

const MAX_SCREENSHOTS = 8;

const shots = ref<string[]>([]);
const loading = ref(true);
const index = ref(0);
const dialogEl = ref<HTMLDialogElement | null>(null);
let touchX: number | null = null;

async function preload(src: string) {
  const img = new Image();
  img.src = src;
  try {
    await img.decode();
    return true;
  } catch {
    return false;
  }
}

onMounted(async () => {
  const candidates = Array.from(
    { length: MAX_SCREENSHOTS - 1 },
    (_, i) => `/${i + 1}.png`
  );
  const loaded = await Promise.all(candidates.map(preload));
  const firstMissing = loaded.indexOf(false);
  shots.value = candidates.slice(
    0,
    firstMissing === -1 ? candidates.length : firstMissing
  );
  loading.value = false;
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

@mixin round-button($size) {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: $size;
  height: $size;
  padding: 0;
  border: 0;
  border-radius: 50%;
  cursor: pointer;
  color: #fff;
  background: #000;
  box-shadow: 0 2px 8px rgba(#000, 0.4);
  transition: background-color 120ms ease;

  &:hover {
    background: #222;
  }

  &:focus-visible {
    outline: 2px solid #fff;
    outline-offset: 2px;
  }

  svg {
    display: block;
    width: 50%;
    height: 50%;
    fill: none;
    stroke: currentColor;
    stroke-width: 2.25;
    stroke-linecap: round;
    stroke-linejoin: round;
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

  &:disabled {
    cursor: progress;
    color: rgba($cream, 0.6);
    text-decoration-color: rgba($crimson, 0.5);
  }

  &_loader {
    display: inline-flex;
    gap: 3px;
    margin-left: 8px;
    vertical-align: middle;

    i {
      width: 5px;
      height: 5px;
      background: $crimson;
      animation: loader-blink 0.9s steps(1) infinite;

      &:nth-child(2) {
        animation-delay: 0.3s;
      }

      &:nth-child(3) {
        animation-delay: 0.6s;
      }
    }
  }
}

@keyframes loader-blink {
  0% {
    opacity: 1;
  }
  33% {
    opacity: 0.25;
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
  user-select: none;
  -webkit-user-select: none;
  -webkit-tap-highlight-color: transparent;

  img {
    -webkit-user-drag: none;
  }

  &::backdrop {
    background: rgba($night, 0.92);
    animation: gallery-in 0.25s steps(4) backwards;
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
    justify-content: flex-end;
    width: min(100%, 1400px);
    margin: 0 auto;
  }

  &_close {
    @include round-button(48px);
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
  }

  &_nav {
    @include round-button(56px);
    position: absolute;
    z-index: 1;
    top: 50%;
    transform: translateY(-50%);

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
    width: 44px;
    height: 44px;
  }

  .gallery_thumb {
    width: 72px;
  }
}
</style>
