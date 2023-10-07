<script setup lang="ts">
import { useDark, useToggle } from '@vueuse/core';
import IconLightMode from '@/components/icons/IconLightMode.vue';
import IconDarkMode from '@/components/icons/IconDarkMode.vue';

const isDark = useDark({
  attribute: 'data-scheme',
  valueDark: 'dark',
  valueLight: 'light',
});
const toggleDark = useToggle(isDark);
</script>
<template>
  <header
    class="fixed top-0 left-0 w-full bg-$header-bg py-16px px-24px shadow-lg flex justify-center items-center"
  >
    <div class="container flex items-center justify-between">
      <h1 class="text-xl text-$header-text font-bold">Evan You's Repo</h1>
      <div class="flex justify-center items-center">
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
