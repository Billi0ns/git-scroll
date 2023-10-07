<script setup lang="ts">
import { computed } from 'vue';
import type { PropType } from 'vue';
import type { RepoData } from '@/views/HomeView.vue';
import IconStar from '@/components/icons/IconStar.vue';

const props = defineProps({
  repoData: {
    type: Object as PropType<RepoData>,
    default: () => ({}),
  },
});

const formattedDate = computed(() => {
  if (!props.repoData.updated_at) return '';

  const date = new Date(props.repoData.updated_at);
  return date.toLocaleDateString('zh-Hant');
});
</script>

<template>
  <div class="flex flex-col border-#d8dee4 border-solid border-1.5px rounded-10px px-20px py-12px">
    <template v-if="props.repoData.name">
      <div>
        <a
          :href="repoData.url"
          class="text-#0969da text-20px leading-30px font-600 no-underline cursor-pointer hover:underline"
          >{{ repoData.name }}</a
        >
      </div>
      <div class="text-#656d76 text-14px leading-21px mb-5px">
        {{ repoData.description }}
      </div>
      <div class="flex items-center text-#656d76 text-12px leading-18px mt-auto">
        <div class="flex items-center cursor-pointer hover:text-#0969da">
          <IconStar class="mr-3px" />
          <span class="mr-10px">{{ repoData.stargazers_count }}</span>
        </div>
        <div v-if="formattedDate">Updated on {{ formattedDate }}</div>
      </div>
    </template>

    <template v-else>
      <div class="h-20px w-1/2 bg-gray-200 rounded animate-pulse mb-14px"></div>
      <div class="h-16px w-3/4 bg-gray-200 rounded animate-pulse mb-8px"></div>
      <div class="h-16px w-1/2 bg-gray-200 rounded animate-pulse"></div>
    </template>
  </div>
</template>
