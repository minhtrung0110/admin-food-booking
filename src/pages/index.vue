<script lang="ts" setup>
import { useRouter } from 'vue-router'

import GlobalLoader from '@/components/loading/GlobalLoader.vue'

const router = useRouter()
router.push({ name: '/dashboard/' })
</script>

<template>
  <div class="flex items-center justify-center w-screen h-screen">
    <GlobalLoader />
  </div>
</template>

<route lang="yaml">
  meta:
    layout: false
</route>
