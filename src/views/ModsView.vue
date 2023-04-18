<script setup lang="ts">
import { ref } from 'vue'

let mods = ref({})

const asyncCode = async () => {
  mods.value = await (await fetch('https://api.modrinth.com/v2/search')).json()

  console.log(mods.value)
}

asyncCode()
</script>

<template>
  <h1 class="text-4xl">Mods Page</h1>
  <ul v-if="mods && mods.hits" class="my-4 grid grid-cols-4 gap-8">
    <li
      v-for="mod in mods.hits"
      :key="mod.project_id"
      class="bg-[--color-background-soft] rounded-xl overflow-hidden grid grid-rows-[min-content,auto]"
    >
      <img :src="mod.icon_url" alt="" class="bg-white" />
      <div class="p-4 flex flex-col justify-between">
        <div>
          <RouterLink :to="`/mods/${mod.slug}`">
            <h1 class="text-2xl">{{ mod.title }}</h1>
          </RouterLink>
          <h2>
            <span class="text-[hsla(160,100%,37%,1)]">{{ mod.author }}</span> -
            <span class="text-[hsla(160,100%,37%,1)]">{{
              new Date(mod.date_modified).toDateString()
            }}</span>
          </h2>
          <p>{{ mod.description }}</p>
        </div>

        <!-- <ul class="flex gap-2 flex-wrap my-4">
          <li
            class="px-3 py-2 rounded-md text-xs font-semibold bg-[hsla(160,100%,37%,1)]"
            v-for="version in mod.versions.filter((e:string) => !e.includes('pre') && !e.includes('rc') && e.includes('.'))"
            :key="version"
          >
            {{ version }}
          </li>
        </ul> -->
        <div>
          <p>Downloads: {{ mod.downloads }} | Follows: {{ mod.follows }}</p>
        </div>
        <!-- {{ mod }} -->
      </div>
    </li>
  </ul>
  <!-- {{ mods }} -->
</template>
