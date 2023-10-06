<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { Octokit } from 'octokit';
import TheHeader from '@/components/TheHeader.vue';
// import TheWelcome from '../components/TheWelcome.vue';

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
  </main>
</template>
