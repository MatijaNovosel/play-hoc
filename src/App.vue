<template>
  <main class="page">
    <header class="hero">
      <div class="hero_art" aria-hidden="true">
        <img src="/mainArt.png" alt="" width="1128" height="1080" />
      </div>
      <div class="hero_menu">
        <h1 class="title">
          <img
            src="/mainMenuTitle.png"
            alt="Heroes of Crimson"
            width="1920"
            height="1080"
          />
        </h1>
        <div class="download">
          <a class="download_button" :href="DOWNLOAD_URL" download>
            <svg class="download_icon" viewBox="0 0 9 9" aria-hidden="true">
              <path
                d="M4 0h1v5H4zM2 3h1v1H2zM3 4h1v1H3zM6 3h1v1H6zM5 4h1v1H5zM0 6h1v3H0zM8 6h1v3H8zM1 8h7v1H1z"
              />
            </svg>
            Download
          </a>
          <ScreenshotGallery />
          <p class="download_version">Version {{ GAME_VERSION }}</p>
        </div>
        <section class="about" aria-labelledby="about-heading">
          <p class="about_lead">
            A story driven bullet hell RPG heavily inspired by Realm of the Mad
            God and Hammerwatch. Choose your character, try not to die and
            progress through the story!
          </p>
          <p class="about_body about_body--muted">
            Heroes of Crimson is a very early work in progress, so expect things
            to break and change between versions.
          </p>
          <div class="flex">
            Made by Matija, link to my
            <a
              class="about_body about_body--author pl-1"
              href="https://github.com/MatijaNovosel"
            >
              GitHub </a
            >!
          </div>
        </section>
      </div>
    </header>
  </main>
</template>

<script setup lang="ts">
import ScreenshotGallery from "./components/ScreenshotGallery.vue";

const GAME_VERSION = "0.0.389";
const DOWNLOAD_URL = "/heroes-of-crimson.zip";
</script>

<style lang="scss" scoped>
@use "./styles/tokens" as *;

.page {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  min-height: 100dvh;
  background: $night;
  overflow-x: hidden;
}

.hero {
  display: flex;
  flex-direction: column;
  flex: 1;
  background: linear-gradient(to bottom, $sky 0%, $sky-4 55%, $night 100%);

  &_art {
    order: 2;

    img {
      display: block;
      width: 100%;
      height: auto;
      image-rendering: pixelated;
      -webkit-mask-image: linear-gradient(to top, #000 80%, transparent);
      mask-image: linear-gradient(to top, #000 80%, transparent);
    }
  }

  &_menu {
    order: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: clamp(16px, 4vh, 48px) 24px 0;
    position: relative;
    z-index: 1;
  }

  @media (min-width: 900px) {
    display: grid;
    grid-template-columns: minmax(0, 1.15fr) minmax(0, 1fr);
    grid-template-rows: minmax(0, 1fr);
    align-items: center;
    flex: none;
    height: 100vh;
    height: 100dvh;
    overflow: hidden;

    &_art {
      order: 1;
      align-self: stretch;
      position: relative;
      min-height: 0;

      img {
        position: absolute;
        inset: 0;
        height: 100%;
        object-fit: cover;
        object-position: 40% 60%;
        -webkit-mask-image: linear-gradient(to right, #000 60%, transparent);
        mask-image: linear-gradient(to right, #000 60%, transparent);
      }
    }

    &_menu {
      order: 2;
      padding: clamp(16px, 4vh, 48px) clamp(24px, 4vw, 64px)
        clamp(16px, 4vh, 48px) 0;
    }
  }
}

.title {
  margin: 0;
  width: min(100%, 620px, 62vh);
  line-height: 0;

  img {
    width: 100%;
    height: auto;
    image-rendering: pixelated;
    animation: title-in 0.7s steps(7) both;
  }
}

@keyframes title-in {
  from {
    opacity: 0;
    transform: translateY(-16px);
  }
  to {
    opacity: 1;
    transform: none;
  }
}

.download {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: clamp(4px, 1vh, 10px);
  margin-top: 24px;

  &_button {
    position: relative;
    display: inline-flex;
    align-items: center;
    gap: 0.4em;
    padding: clamp(4px, 0.7vh, 7px) 30px clamp(6px, 1vh, 9px);
    font-family: var(--font-pixel);
    font-size: clamp(20px, 3.2vh, 30px);
    line-height: 1;
    color: $cream;
    text-decoration: none;
    background: $crimson;
    --shade:
      inset 0 #{-$px} 0 #{$crimson-lo}, inset 0 #{$px * 0.5} 0 #{$crimson-hi};
    box-shadow: var(--shade);
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
    transition: background-color 80ms steps(2);

    &:hover {
      background: $crimson-hi;
    }

    &:active {
      --shade: inset 0 #{$px} 0 #{$crimson-lo};
      background: $crimson;
    }

    &:focus-visible {
      outline: none;
      box-shadow:
        inset 0 0 0 $px $cream,
        var(--shade);
    }
  }

  &_icon {
    flex: none;
    width: 0.55em;
    height: 0.55em;
    fill: currentColor;
    shape-rendering: crispEdges;
  }

  &_version {
    margin: 0;
    font-size: clamp(18px, 2.6vh, 24px);
    line-height: 1;
    color: rgba($cream, 0.75);
  }
}

.about {
  width: 100%;
  max-width: 460px;
  margin-top: clamp(10px, 3.5vh, 36px);
  font-size: clamp(14px, 2.1vh, 19px);
  line-height: 1.25;
  text-align: left;

  &_lead {
    margin: 0 0 clamp(4px, 1vh, 10px);
    font-size: clamp(16px, 2.45vh, 22px);
    line-height: 1.15;
    color: $cream;
  }

  &_body {
    margin: 0 0 clamp(4px, 1vh, 10px);
    color: rgba($cream, 0.85);

    &--muted {
      color: $ember;
    }

    &--author {
      color: $crimson-hi;
      text-decoration: underline;
      text-underline-offset: 4px;

      &:hover {
        color: $cream;
        transition: 0.5s;
      }
    }
  }
}
</style>
