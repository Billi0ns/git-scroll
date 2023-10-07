<script setup lang="ts">
import { useDark, useToggle } from '@vueuse/core';
import IconLightMode from '@/components/icons/IconLightMode.vue';
import IconDarkMode from '@/components/icons/IconDarkMode.vue';
import IconGithub from '@/components/icons/IconGithub.vue';

const isDark = useDark();
const toggleDark = useToggle(isDark);
</script>
<template>
  <header
    w="full"
    p="x-24px y-16px"
    position="fixed top-0 left-0"
    flex="~ justify-center items-center"
    bg="$header-bg"
    shadow="lg"
    z="10"
  >
    <div class="container flex items-center justify-between">
      <h1 class="text-xl text-$header-text font-bold">Evan You's Repos</h1>
      <div class="flex justify-center items-center">
        <a
          href="https://github.com/Billi0ns/git-scroll"
          class="cursor-pointer flex justify-center items-center mr-20px sm:mr-30px"
        >
          <IconGithub class="text-black dark:text-white" />
        </a>
        <IconDarkMode v-if="isDark" class="cursor-pointer" @click="toggleDark()" />
        <IconLightMode v-else class="cursor-pointer" @click="toggleDark()" />
      </div>
    </div>
  </header>
</template>

<style>
/* Set the height of the body to account for the fixed header */
body {
  padding-top: 64px;
}
</style>
