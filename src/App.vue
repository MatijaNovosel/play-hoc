<template>
  <div class="flex justify-center px-5 pb-5 min-h-full page">
    <div
      class="max-w-screen-lg w-full flex flex-col items-center justify-between"
    >
      <div class="home_page h-full w-full pt-5 md:pt-15">
        <div class="home_page_content">
          <div
            class="text-3xl md:text-5xl font-bold home_page_content_title_text"
          >
            Heroes of Crimson
          </div>
          <div class="text-lg md:text-xl pt-4 text-gray-700 font-bold">
            A singleplayer indie bullet hell game heavily inspired by Realm of
            the Mad God!
          </div>
          <div class="text-md pt-2 text-gray-500">
            Currently a work in progress!
          </div>
          <div class="flex flex-col gap-2 mt-8 links">
            <div class="flex justify-between w-full">
              <div class="text-md text-gray-500">GitHub</div>
              <a
                href="https://github.com/MatijaNovosel/heroes-of-crimson"
                class="text-md flex flex-center text-blue-700"
              >
                Heroes of Crimson
              </a>
            </div>
            <div class="flex justify-between w-full">
              <div class="text-md text-gray-500">Helper tools</div>
              <a
                href="https://github.com/MatijaNovosel/hoc-utils"
                class="text-md flex flex-center text-blue-700"
              >
                HOC Utils
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted } from "vue";

onMounted(() => {
  //
});
</script>

<style lang="scss" scoped>
.home_page {
  background: url("/bg.png");

  &_content {
    max-width: 850px;

    &_title_text {
      color: #bb2121;
    }
  }
}

.links {
  max-width: calc(100% - 15px);
}
</style>
