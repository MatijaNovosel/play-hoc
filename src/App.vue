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
          <a class="download_button" :href="DOWNLOAD_URL">Download</a>
          <p class="download_version">Version {{ GAME_VERSION }}</p>
        </div>
        <section class="about" aria-labelledby="about-heading">
          <p class="about_lead">
            A singleplayer bullet hell RPG heavily inspired by Realm of the Mad
            God. Pick a class, dodge through screens full of projectiles,
            progress through the story and get better loot!
          </p>
          <p class="about_body about_body--muted">
            Heroes of Crimson is a work in progress, so expect things to change
            between versions.
          </p>
          <nav class="links" aria-label="Project links">
            <a href="https://github.com/MatijaNovosel/heroes-of-crimson">
              Source on GitHub
            </a>
            <a href="https://github.com/MatijaNovosel/hoc-utils">HOC Utils</a>
          </nav>
        </section>
      </div>
    </header>
  </main>
</template>

<script setup lang="ts">
const GAME_VERSION = "0.0.1";
const DOWNLOAD_URL =
  "hhttps://drive.google.com/file/d/1Q1TSoc1HCRYR2paQiXfmPfs3eYmS_kN9/view?usp=sharing";
</script>

<style lang="scss" scoped>
$sky: #6c2c52;
$sky-2: #5a2446;
$sky-3: #481d3a;
$sky-4: #37162d;
$night: #02050e;
$cream: #f5d7bf;
$crimson-hi: #f04a42;
$crimson: #dc2330;
$crimson-lo: #ae1e2d;
$ember: #f26b3a;

$px: 4px;

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
    display: inline-block;
    padding: clamp(6px, 1vh, 10px) 44px clamp(8px, 1.4vh, 14px);
    font-family: var(--font-pixel);
    font-size: clamp(28px, 4.6vh, 44px);
    line-height: 1;
    color: $cream;
    text-decoration: none;
    text-shadow: 0 $px 0 $crimson-lo;
    background: linear-gradient(
      to bottom,
      $crimson-hi 0 34%,
      $crimson 34% 68%,
      $crimson-lo 68% 100%
    );
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
    transition: transform 80ms steps(2);

    &:hover {
      transform: translateY(-$px);
    }

    &:active {
      transform: translateY($px);
    }

    &:focus-visible {
      outline: none;
      box-shadow: inset 0 0 0 $px $cream;
    }
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
  }
}

.links {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 8px 28px;
  margin-top: clamp(8px, 2vh, 20px);
  padding-top: clamp(6px, 1.5vh, 14px);

  a {
    color: $cream;
    text-decoration: underline;
    text-decoration-thickness: 2px;
    text-underline-offset: 4px;
    text-decoration-color: $crimson;

    &:hover {
      color: $crimson-hi;
    }

    &:focus-visible {
      outline: $px solid $cream;
      outline-offset: $px;
    }
  }
}
</style>
