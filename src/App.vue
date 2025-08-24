<script lang="ts" setup>
import { storeToRefs } from 'pinia'

import GlobalLoader from '@/components/loading/GlobalLoader.vue'
import { Toaster } from '@/components/ui/sonner'
import { THEMES, useThemeStore } from '@/stores/theme'

const themeStore = useThemeStore()
const { theme: t, radius } = storeToRefs(themeStore)

watchEffect(() => {
  document.documentElement.classList.remove(...THEMES.map(theme => `theme-${theme}`))
  document.documentElement.classList.add(`theme-${t.value}`)
  document.documentElement.style.setProperty('--radius', `${radius.value}rem`)
})
</script>

<template>
  <Toaster />
  <!-- <VueQueryDevtools /> -->
  <Suspense>
    <router-view v-slot="{ Component, route }">
      <component :is="Component" :key="route" />
    </router-view>

    <template #fallback>
      <GlobalLoader />
    </template>
  </Suspense>
</template>
