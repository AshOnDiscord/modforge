<script setup lang="ts">
import { computed, ref, version } from 'vue'
import { RouterLink, useRoute } from 'vue-router'
import VueMarkdown from 'vue-markdown-render'

let mod = ref({})
let devs = ref([])
let versions = ref([])

const asyncCode = async () => {
  ;(async () => {
    mod.value = await (
      await fetch(`https://api.modrinth.com/v2/project/${useRoute().params.modSlug}`)
    ).json()

    devs.value = await (
      await fetch(`https://api.modrinth.com/v2/team/${mod.value.team}/members`)
    ).json()

    console.log(JSON.parse(JSON.stringify(mod.value)))
    console.log(devs)
  })()
  ;(async () => {
    versions.value = await (
      await fetch(
        `https://api.modrinth.com/v2/project/${useRoute().params.modSlug}/version?featured=true`
      )
    ).json()

    console.log(versions.value)
  })()
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
  <div class="grid grid-cols-[auto,max-content] gap-8">
    <div>
      <div class="grid grid-cols-[max-content,auto,max-content] gap-4">
        <img :src="mod.icon_url" class="bg-white w-40 h-40 rounded-2xl" />
        <div class="pr-8 flex flex-col justify-between">
          <div>
            <h1 class="text-4xl font-semibold text-white">{{ mod.title }}</h1>
            <h2 class="text-xl text-[hsla(160,100%,37%,1)]">{{ modType }}</h2>
            <p>{{ mod.description }}</p>
          </div>
          <div>
            <a
              :href="`https://choosealicense.com/licenses/${mod.license.id.toLowerCase()}/`"
              class="mt-2"
              >{{ mod.license.name }}</a
            >
            <div class="flex gap-4">
              <p>
                <span class="text-[hsla(160,100%,37%,1)] font-semibold">{{ mod.downloads }}</span>
                downloads
              </p>
              <p>
                <span class="text-[hsla(160,100%,37%,1)] font-semibold">{{ mod.followers }}</span>
                followers
              </p>
            </div>
          </div>
        </div>
        <div>
          <div class="flex flex-col items-end border-r-4 pr-4 border-[hsla(160,100%,37%,1)]">
            <a v-if="mod.source_url" :href="mod.source_url">Source</a>
            <a v-if="mod.issues_url" :href="mod.issues_url">Issues</a>
            <a v-if="mod.wiki_url" :href="mod.wiki_url">Wiki</a>
            <a v-if="mod.discord_url" :href="mod.discord_url">Discord</a>
          </div>
        </div>
      </div>
      <vue-markdown
        class="prose dark:prose-invert prose-a:text-[hsla(160,100%,37%,1)] my-8 max-w-none"
        v-if="mod.body"
        :source="mod.body.toString()"
      />
      <!-- <p>{{ mod.body }}</p> -->
      <h1 class="text-2xl mb-4 text-white font-semibold">Team Members</h1>
      <ul class="flex gap-4 mb-6">
        <li v-for="dev in devs" :key="dev.id" class="flex gap-2 items-center">
          <img :src="dev.user.avatar_url" alt="" class="w-12 h-12 rounded-md" />
          <div>
            <a :href="`https://api.modrinth.com/v2/user/${dev.user.id}`">{{
              dev.user.name || dev.user.username
            }}</a>
            <h2>{{ dev.role }}</h2>
          </div>
        </li>
      </ul>
      <h1>Mod {{ $route.params.modSlug }}</h1>
      <div v-if="mod">{{ mod }}</div>
    </div>

    <div>
      <aside class="bg-[--color-background-soft] w-64 rounded-2xl p-4">
        <ul v-if="versions" class="flex flex-col gap-4">
          <li v-for="version in versions" :key="version.id">
            <a :href="`https://api.modrinth.com/v2/version/${version.id}`">{{ version.name }}</a>
            <p>{{ version.loaders.join(', ') }} {{ version.game_versions.join(', ') }}</p>
          </li>
        </ul>
      </aside>
    </div>
  </div>
</template>
