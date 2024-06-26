<script setup lang="ts">
import request from "~/composables/request";

interface Contributor {
  login: string;
  html_url: string;
  avatar_url: string;
}

const star = ref("0");
const downloads = ref("0");
const contributors: Ref<Contributor[]> = ref([]);

function getTimeRange() {
  const today = new Date();
  const formattedToday = `${today.getFullYear()}-${String(
    today.getMonth() + 1
  ).padStart(2, "0")}-${String(today.getDate()).padStart(2, "0")}`;
  const lastYear = new Date(new Date().setFullYear(today.getFullYear() - 1));
  const formattedLastYear = `${lastYear.getFullYear()}-${String(
    lastYear.getMonth() + 1
  ).padStart(2, "0")}-${String(lastYear.getDate()).padStart(2, "0")}`;
  const timeRange = `${formattedLastYear}:${formattedToday}`;
  return timeRange;
}

async function fetchState() {
  const { format } = Intl.NumberFormat("en", { notation: "compact" });
  const { data: contributorsData } = await request.getContributors();
  contributors.value = contributorsData.map((item: Contributor) => ({
    login: item.login,
    html_url: item.html_url,
    avatar_url: item.avatar_url,
  }));
  const { data: starData } = await request.getStar();
  star.value = format(starData.stargazers_count);
  const { data: downloadsData } = await request.getDownloads(getTimeRange());
  downloads.value = format(downloadsData.downloads);
}

onMounted(() => {
  fetchState();
});
</script>

<template>
  <div>
    <ULandingSection class="!pt-0 from-gray-950/50 to-gray-900">
      <ULandingCTA
        align="left"
        card
        :ui="{
          background: 'dark:bg-gradient-to-b from-gray-800 to-gray-900',
          shadow: 'dark:shadow-2xl',
          title: 'text-center lg:text-left',
          links: 'justify-center lg:justify-start',
        }"
      >
        <template #title>
          <span>
            因为热爱，
            <br />
            我们汇聚在一起。
          </span>
        </template>

        <template #links>
          <ClientOnly>
            <UAvatarGroup
              :max="16"
              size="md"
              class="flex-wrap-reverse [&_span:first-child]:text-xs justify-center"
            >
              <UTooltip
                v-for="(contributor, index) of contributors"
                :key="index"
                :text="contributor.login"
                class="rounded-full"
                :ui="{ background: 'bg-gray-50 dark:bg-gray-800/50' }"
                :popper="{ offsetDistance: 16 }"
              >
                <UAvatar
                  :alt="contributor.login"
                  :src="`https://ipx.nuxt.com/s_40x40/gh_avatar/${contributor.login}`"
                  :srcset="`https://ipx.nuxt.com/s_80x80/gh_avatar/${contributor.login} 2x`"
                  class="lg:hover:scale-125 lg:hover:ring-2 lg:hover:ring-primary-500 dark:lg:hover:ring-primary-400 transition-transform"
                  width="40"
                  height="40"
                  size="md"
                  loading="lazy"
                >
                  <NuxtLink
                    :to="`https://github.com/${contributor.login}`"
                    :aria-label="contributor.login"
                    target="_blank"
                    class="focus:outline-none"
                    tabindex="-1"
                  >
                    <span class="absolute inset-0" aria-hidden="true" />
                  </NuxtLink>
                </UAvatar>
              </UTooltip>
            </UAvatarGroup>
          </ClientOnly>
        </template>

        <div
          class="flex flex-col sm:flex-row items-center justify-center gap-8 lg:gap-16"
        >
          <NuxtLink
            class="text-center group"
            to="https://www.npmjs.com/package/hexo-theme-solitude"
            target="_blank"
          >
            <p
              class="text-6xl font-semibold text-gray-900 dark:text-white group-hover:text-primary-500 dark:group-hover:text-primary-400"
            >
              {{ downloads }}+
            </p>
            <p>最近一年下载量</p>
          </NuxtLink>

          <NuxtLink
            class="text-center group"
            to="https://github.com/valor-x/hexo-theme-solitude"
            target="_blank"
          >
            <p
              class="text-6xl font-semibold text-gray-900 dark:text-white group-hover:text-primary-500 dark:group-hover:text-primary-400"
            >
              {{ star }}+
            </p>
            <p>Star 数</p>
          </NuxtLink>
        </div>
      </ULandingCTA>
    </ULandingSection>
  </div>
</template>
~/server/api/user
