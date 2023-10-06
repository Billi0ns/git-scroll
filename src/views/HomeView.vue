<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { Octokit } from 'octokit';
import TheHeader from '@/components/TheHeader.vue';
import RepoItem from '@/components/RepoItem.vue';

const repoUser = 'yyx990803'; // Evan You
const repoData = ref<RepoData[]>([]);

const octokit = new Octokit({});
export interface RepoData {
  id: number;
  name: string;
  description: string | null;
  url: string | undefined;
  stargazers_count: number | undefined;
  updated_at: string | null | undefined;
}

const getRepoData = async (amount: number, page: number) => {
  try {
    const response = await octokit.request('GET /users/{username}/repos', {
      username: repoUser,
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

    console.log(newData);
  } catch (error) {
    console.error('There was a problem fetching repo data:', error);
  }
};

onMounted(async () => {
  getRepoData(30, 1);
});
</script>

<template>
  <main>
    <TheHeader />

    <div class="container grid px-10 py-5 mx-auto gap-20px" md="grid-cols-2">
      <RepoItem v-for="item in repoData" :key="item.id" :repo-data="item" />
    </div>
  </main>
</template>
