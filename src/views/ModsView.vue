<script setup lang="ts">
import { type Ref, ref, watch } from 'vue'
import { HeartIcon,ArrowDownTrayIcon }from '@heroicons/vue/24/outline'

interface SearchHit {
  slug: string
  title: string
  description: string
  categories: string[]
  client_side: string
  server_side: string
  project_type: string
  downloads: number
  icon_url: string
  color: number
  thread_id: string
  monetization_status: string
  project_id: string
  author: string
  display_categories: string[]
  versions: string[]
  follows: number
  date_created: string
  date_modified: string
  latest_version: string
  license: string
  gallery: string[]
  featured_gallery: string
}

interface SearchResults {
  hits: SearchHit[]
  limit: number
  offset: number
  total_hits: number
}

let mods: Ref<SearchResults | undefined> = ref(undefined)
let page = ref(0)

const asyncCode = async () => {
  mods.value = (await (await fetch(`https://api.modrinth.com/v2/search?offset=${page.value*10}`)).json()) as SearchResults
  console.log(mods.value.hits)
}

watch(page, asyncCode, { immediate: true });

// asyncCode()
</script>

<template>
  <h1 class="text-4xl">Mods Page</h1>
  <ul v-if="mods && mods.hits" class="my-4 grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-8">
    <li
      v-for="mod in mods.hits"
      :key="mod.project_id"
      class="bg-[--color-background-soft] rounded-xl overflow-hidden grid grid-rows-[max-content,auto]"
    >
      <img :src="mod.icon_url" alt="" class="bg-white aspect-square w-full h-full" />
      <div class="p-4 flex flex-col justify-between">
        <div>
          <RouterLink :to="`/mods/${mod.slug}`" class="text-2xl text-emerald-500 mb-2 inline-block">
            {{ mod.title }}
          </RouterLink>
          <h2>
            <span class="text-emerald-50">{{ mod.author }}</span> -
            <span class="text-emerald-50">{{ new Date(mod.date_modified).toDateString() }}</span>
          </h2>
          <p>{{ mod.description }}</p>
        </div>
        <div class="flex divide-gray-500 divide-solid divide-x-2 mt-2 text-gray-300 font-semibold">
            <div class="flex gap-1 pr-2">
              <ArrowDownTrayIcon class="w-6 h-6 text-gray-500" />
            {{ Intl.NumberFormat([], { notation: 'compact' }).format(mod.downloads) }}
            </div>
            <div class="flex gap-1 pl-2">

            <HeartIcon class="w-6 h-6 text-gray-500" />
            {{ Intl.NumberFormat([], { notation: 'compact' }).format(mod.follows) }}
            </div>
        </div>
        <!-- {{ mod }} -->
      </div>
    </li>
  </ul>
  <div class="flex gap-2 justify-center">
    <button @click="page--" :disabled="page === 0">Prev</button><p class="text-gray-300">Page {{ page+1 }}</p> <button @click="page++">Next</button>
  </div>
  <!-- {{ mods }} -->
</template>
