<script setup lang="ts">
import { computed } from 'vue';
import type { PropType } from 'vue';
import type { RepoData } from '@/views/HomeView.vue';
import IconStar from '@/components/icons/IconStar.vue';

const props = defineProps({
  repoData: {
    type: Object as PropType<RepoData>,
    required: true,
  },
});

const formattedTime = computed(() => {
  return props.repoData.updated_at?.split('T')[0];
});
</script>

<template>
  <div class="w-full flex flex-col border-#d8dee4 border-solid rounded-10px px-20px py-12px">
    <a
      :href="repoData.url"
      class="text-#0969da text-20px leading-30px font-600 no-underline cursor-pointer hover:underline"
      >{{ repoData.name }}</a
    >
    <div class="text-#656d76 text-14px leading-21px mb-5px">
      {{ repoData.description }}
    </div>
    <div class="flex items-center text-#656d76 text-12px leading-18px mt-auto">
      <div class="flex items-center cursor-pointer hover:text-#0969da">
        <IconStar class="mr-3px" />
        <span class="mr-10px">{{ repoData.stargazers_count }}</span>
      </div>
      <div>Updated on {{ formattedTime }}</div>
    </div>
  </div>
</template>
