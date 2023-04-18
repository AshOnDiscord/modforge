<script setup lang="ts">
import { computed, ref } from 'vue'
import { RouterLink, useRoute } from 'vue-router'

let mod = ref({})
let devs = ref([])

const asyncCode = async () => {
  mod.value = await (
    await fetch(`https://api.modrinth.com/v2/project/${useRoute().params.modSlug}`)
  ).json()

  devs.value = await (
    await fetch(`https://api.modrinth.com/v2/team/${mod.value.team}/members`)
  ).json()

  console.log(JSON.parse(JSON.stringify(mod.value)))
  console.log(devs)
}

const modType = computed(() => {
  const modData = mod.value

  if (!modData) return 'Loading...'

  if (modData.client_side == 'optional' && modData.server_side == 'optional') {
    return 'Client or Server'
  }
  if (modData.client_side == 'required' && modData.server_side == 'required') {
    return 'Client and Server'
  }
  if (modData.client_side == 'required' && modData.server_side == 'unsupported') {
    return 'Client only'
  }
  if (modData.client_side == 'unsupported' && modData.server_side == 'required') {
    return 'Server only'
  }
  return modData.client_side + ' | ' + modData.client_side
})

asyncCode()
</script>

<template>
  <div class="grid grid-cols-[max-content,auto,max-content] gap-4">
    <img :src="mod.icon_url" class="bg-white w-40 h-40 rounded-2xl" />
    <div>
      <h1 class="text-4xl">{{ mod.title }}</h1>
      <h2>{{ modType }}</h2>
      <p>{{ mod.description }}</p>
    </div>
    <div>
      <div class="flex flex-col items-end border-r-4 pr-4 border-[hsla(160,100%,37%,1)]">
        <a :href="mod.source_url">Source</a>
        <a :href="mod.issues_url">Issues</a>
        <a :href="mod.wiki_url">Wiki</a>
        <a :href="mod.discord_url">Discord</a>
      </div>
    </div>
  </div>
  <p>{{ mod.body }}</p>
  <ul class="flex flex-col gap-4">
    <li v-for="dev in devs" :key="dev.id" class="flex gap-2 items-center">
      <img :src="dev.user.avatar_url" alt="" class="w-12 h-12 rounded-md" />
      <div>
        <RouterLink :to="`https://api.modrinth.com/v2/user/${dev.user.id}`">{{
          dev.user.name
        }}</RouterLink>
        <h2>{{ dev.role }}</h2>
      </div>
    </li>
  </ul>
  <h1>Mod {{ $route.params.modSlug }}</h1>
  <div v-if="mod">{{ mod }}</div>
</template>
