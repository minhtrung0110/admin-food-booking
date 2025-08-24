<script lang="ts" setup>
import { defineProps, withDefaults } from 'vue'

withDefaults(
  defineProps<{
    show?: boolean
  }>(),
  { show: true },
)
</script>

<template>
  <transition appear name="fade">
    <div
      v-if="show"
      aria-live="polite"
      class="fixed inset-0 z-[9999] bg-slate-950/45 backdrop-blur-[2px] grid"
      role="status"
    >
      <!-- Top thin progress bar -->
      <div class="absolute top-0 left-0 right-0 h-0.5 overflow-hidden">
        <div
          class="h-full w-[40%] animate-topbar
                 bg-gradient-to-r from-sky-400/0 via-sky-400 to-sky-400/0"
        />
      </div>

      <!-- Center content -->
      <div class="place-self-center text-center select-none">
        <div
          class="inline-block rounded-lg px-6 py-3 bg-white/5 ring-1 ring-white/10"
        >
          <p class="text-white/90 tracking-[0.25em] uppercase text-sm md:text-base font-medium">
            Loading...
          </p>
        </div>

        <!-- Subtle indeterminate line -->
        <div class="mt-5 h-2 w-72 max-w-[70vw] overflow-hidden rounded-full bg-white/12">
          <div class="h-full animate-line bg-white/70" />
        </div>

        <p class="mt-3 text-white/60 text-base">
          Please wait while the application loads...
        </p>
        <span class="sr-only">Website is loading content</span>
      </div>
    </div>
  </transition>
</template>

<style scoped>
/* Line under title */
@keyframes line {
  0%   { transform: translateX(-100%); width: 30%; }
  50%  { width: 60%; }
  100% { transform: translateX(200%);  width: 30%; }
}
.animate-line { animation: line 1.4s ease-in-out infinite; }

/* Thin top bar */
@keyframes topbar {
  0%   { transform: translateX(-50%); }
  100% { transform: translateX(250%); }
}
.animate-topbar { animation: topbar 1.15s cubic-bezier(.4,0,.2,1) infinite; }

/* Transition overlay */
.fade-enter-active, .fade-leave-active { transition: opacity .2s ease; }
.fade-enter-from, .fade-leave-to { opacity: 0; }

/* Respect reduced motion */
@media (prefers-reduced-motion: reduce) {
  .animate-line, .animate-topbar { animation: none !important; }
}
</style>
