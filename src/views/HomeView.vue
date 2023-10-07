<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue';
import { Octokit } from '@octokit/core';
import TheHeader from '@/components/TheHeader.vue';
import TheLoading from '@/components/TheLoading.vue';
import RepoItem from '@/components/RepoItem.vue';

export interface RepoData {
  id: number;
  name: string;
  description: string | null;
  url: string | undefined;
  stargazers_count: number | undefined;
  updated_at: string | null | undefined;
}

const loadingEl = ref<InstanceType<typeof TheLoading> | null>(null);
const isLoading = ref(false);
const isInited = ref(false);

const REPO_USER = 'yyx990803'; // Evan You
const repoAmount = ref(0);
const repoData = ref<RepoData[]>([]);
const isRepoFullyLoaded = computed(
  () => repoData.value.length >= repoAmount.value && isInited.value,
);
const shouldShowLoading = computed(() => !isRepoFullyLoaded.value && isInited.value);

const REPO_USER = 'yyx990803'; // Evan You
const repoAmount = ref(0);
const repoData = ref<RepoData[]>([]);

const octokit = new Octokit({
  auth: import.meta.env.VITE_GITHUB_TOKEN,
});

const getRepoAmount = async () => {
  try {
    const response = await octokit.request('GET /users/{username}', {
      username: REPO_USER,
    });
    const { data } = response;
    repoAmount.value = data.public_repos;
  } catch (error) {
    // eslint-disable-next-line no-console
    console.error('There was a problem fetching user data:', error);
  }
};

const getRepoData = async (amount: number, page: number) => {
  try {
    isLoading.value = true;
    const response = await octokit.request('GET /users/{username}/repos', {
      username: REPO_USER,
      per_page: amount,
      page,
    });

    const { data } = response;
    const newData = data.map(
      ({ id, name, description, svn_url, stargazers_count, updated_at }) => ({
        id,
        name,
        description,
        url: svn_url,
        stargazers_count,
        updated_at,
      }),
    );
    repoData.value.push(...newData);
    isLoading.value = false;
  } catch (error) {
    // eslint-disable-next-line no-console
    console.error('There was a problem fetching repo data:', error);
    isLoading.value = false;
  }
};

let observer: IntersectionObserver;
const FIRST_FETCH_SIZE = 30;
const SUBSEQUENT_FETCH_SIZE = 10;
const startPage = ref(Math.floor(FIRST_FETCH_SIZE / SUBSEQUENT_FETCH_SIZE) + 1);

const fetchInitialData = async () => {
  await getRepoData(FIRST_FETCH_SIZE, 1);
  isInited.value = true;
};

const fetchAdditionalData = async () => {
  await getRepoData(SUBSEQUENT_FETCH_SIZE, startPage.value);
  startPage.value += 1;
};

const disconnectObserverIfLoaded = () => {
  if (isRepoFullyLoaded.value) {
    observer.disconnect();
  }
};

const handleIntersect = async () => {
  if (isLoading.value) return;

  await fetchAdditionalData();
  disconnectObserverIfLoaded();
};

const createIntersectionObserver = (target: HTMLElement) => {
  const options = {
    root: null,
    rootMargin: '0px 0px 100px 0px',
    threshold: 0,
  };

  const callback = (entries: IntersectionObserverEntry[]) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        handleIntersect();
      }
    });
  };
  observer = new IntersectionObserver(callback, options);
  observer.observe(target);
};

onMounted(async () => {
  if (!loadingEl.value?.el) return;

  await getRepoAmount();
  createIntersectionObserver(loadingEl.value.el);
  fetchInitialData();
});

onUnmounted(() => {
  observer.disconnect();
});
</script>

<template>
  <main>
    <TheHeader />

    <div class="container grid px-10 py-5 mx-auto gap-20px" md="grid-cols-2">
      <template v-if="!repoData.length">
        <RepoItem v-for="item in 30" :key="item" />
      </template>
      <RepoItem v-for="item in repoData" :key="item.id" :repo-data="item" />
    </div>

    <TheLoading ref="loadingEl" v-show="shouldShowLoading" />
  </main>
</template>
