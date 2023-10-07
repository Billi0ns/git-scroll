<template>
  <div ref="el" class="flex flex-col justify-center items-center h-100px">
    <div
      class="animate-spin"
      w="45px"
      h="45px"
      rounded="1/2"
      border="8px solid $light-gray"
      border-t="8px solid $blue"
    ></div>
    <div class="mt-10px text-18px font-bold text-$dark-gray">Loading...</div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const el = ref<HTMLElement | null>(null);

defineExpose({
  el,
});
</script>
