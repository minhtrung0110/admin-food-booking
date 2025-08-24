<script lang="ts" setup>
import { computed, defineProps, withDefaults } from 'vue'

const props = withDefaults(
  defineProps<{
    /** Bật/tắt overlay */
    show?: boolean
    /** Tùy biến bảng màu gradient (hex/hsl/rgba) nếu cần */
    palette?: {
      c1?: string
      c2?: string
      c3?: string
      c4?: string
      c5?: string
      c6?: string
    }
  }>(),
  {
    show: true,
  },
)

const visible = computed(() => !!props.show)

/** Tạo inline style cho mỗi blob với màu + quỹ đạo khác nhau */
function blobStyle(colorVar: string, translate: string, dur = '20s', delay = '0s') {
  const [tx, ty] = translate.split(',')
  return `
    --blob-color: ${colorVar};
    animation-duration: ${dur};
    animation-delay: ${delay};
    transform: translate(${tx.trim()}, ${ty.trim()});
  `
}
</script>

<template>
  <transition appear name="fade">
    <div
      v-if="visible"
      :aria-hidden="!visible"
      aria-live="polite"
      class="fixed inset-0 z-[9999] pointer-events-auto"
      role="status"
    >
      <!-- Aurora background + blur -->
      <div

        class="absolute inset-0 bg-[radial-gradient(120%_70%_at_30%_20%,theme(colors.sky.500/.18),transparent_60%),radial-gradient(120%_70%_at_70%_80%,theme(colors.fuchsia.500/.16),transparent_60%)] backdrop-blur-sm"
      />

      <!-- Center stage -->
      <div class="relative h-full w-full grid place-items-center">
        <div class="relative w-[20rem] h-[20rem] max-w-[75vw] max-h-[75vw]">
          <!-- SVG Filter: Gooey -->
          <svg class="absolute" height="0" width="0">
            <filter id="gooey">
              <feGaussianBlur in="SourceGraphic" result="blur" stdDeviation="8" />
              <feColorMatrix
                in="blur"
                mode="matrix"
                result="goo"
                values="
                  1 0 0 0 0
                  0 1 0 0 0
                  0 0 1 0 0
                  0 0 0 24 -8"
              />
              <feBlend in="SourceGraphic" in2="goo" />
            </filter>
          </svg>

          <!-- Gooey blobs -->
          <div class="absolute inset-0" style="filter: url(#gooey)">
            <!-- Base style cho blob -->
            <span
              :style="blobStyle('var(--c1)', '-40%,-40%', '20s', '0s')"
              class="blob"
            />
            <span
              :style="blobStyle('var(--c2)', '40%,-30%', '18s', '-3s')"
              class="blob"
            />
            <span
              :style="blobStyle('var(--c3)', '-35%,35%', '22s', '-5s')"
              class="blob"
            />
            <span
              :style="blobStyle('var(--c4)', '35%,35%', '19s', '-8s')"
              class="blob"
            />
            <span
              :style="blobStyle('var(--c5)', '0%,0%', '12s', '-2s')"
              class="blob blob--small"
            />
            <span
              :style="blobStyle('var(--c6)', '-10%,10%', '9s', '-6s')"
              class="blob blob--tiny"
            />
          </div>

          <!-- Logo / Title -->
          <div class="absolute inset-0 grid place-items-center">
            <div class="text-center select-none">
              <div
                class="px-5 py-2 rounded-xl/2 backdrop-blur-[2px] bg-white/5 ring-1 ring-white/10 shadow-xl"
              >
                <p
                  class="font-semibold tracking-[0.35em] uppercase text-white/90 text-xl md:text-2xl animate-shine"
                >
                  Đang tải
                </p>
              </div>
              <p class="mt-3 text-white/70 text-sm md:text-base">
                Chuẩn bị không gian cho bạn…
              </p>
              <span class="sr-only">Ứng dụng đang tải nội dung</span>
            </div>
          </div>
        </div>

        <!-- Progress bar (indeterminate) -->
        <div
          class="mt-10 w-[min(88vw,560px)] h-1.5 rounded-full overflow-hidden bg-white/15 ring-1 ring-white/10"
        >
          <div class="h-full animate-progress bg-progress" />
        </div>
      </div>
    </div>
  </transition>
</template>

<style scoped>
/* Base tokens (có thể override qua :root hoặc prop.palette bằng CSS vars) */
:host, :root, .fixed {
  --c1: radial-gradient(circle at 30% 30%, #22d3ee, #3b82f6);
  --c2: radial-gradient(circle at 70% 30%, #a855f7, #f472b6);
  --c3: radial-gradient(circle at 30% 70%, #34d399, #10b981);
  --c4: radial-gradient(circle at 70% 70%, #f59e0b, #ef4444);
  --c5: radial-gradient(circle at 50% 50%, #60a5fa, #22d3ee);
  --c6: radial-gradient(circle at 50% 50%, #f472b6, #a78bfa);
}

/* Gooey blobs */
.blob {
  position: absolute;
  inset: 0;
  margin: auto;
  width: 14rem;
  height: 14rem;
  border-radius: 9999px;
  opacity: 0.9;
  mix-blend-mode: screen;
  animation-name: blob-orbit;
  animation-timing-function: ease-in-out;
  animation-iteration-count: infinite;
  filter: drop-shadow(0 10px 30px rgba(255, 255, 255, 0.2));
}

.blob--small {
  width: 9rem;
  height: 9rem;
}

.blob--tiny {
  width: 6rem;
  height: 6rem;
  opacity: 0.8;
}

/* Quỹ đạo hữu cơ */
@keyframes blob-orbit {
  0% {
    transform: translate(-40%, -40%) rotate(0deg) scale(1);
  }
  25% {
    transform: translate(45%, -35%) rotate(60deg) scale(1.05);
  }
  50% {
    transform: translate(40%, 35%) rotate(120deg) scale(0.98);
  }
  75% {
    transform: translate(-45%, 30%) rotate(200deg) scale(1.08);
  }
  100% {
    transform: translate(-40%, -40%) rotate(360deg) scale(1);
  }
}

/* Heading shimmer (nhẹ) */
@keyframes shine {
  0% {
    text-shadow: 0 0 0 rgba(255, 255, 255, 0.0);
  }
  50% {
    text-shadow: 0 0 22px rgba(255, 255, 255, 0.35);
  }
  100% {
    text-shadow: 0 0 0 rgba(255, 255, 255, 0.0);
  }
}

.animate-shine {
  animation: shine 2.8s ease-in-out infinite;
}

/* Progress indeterminate */
.bg-progress {
  background-image: linear-gradient(90deg, rgba(255, 255, 255, 0) 0%,
  rgba(255, 255, 255, 0.85) 50%,
  rgba(255, 255, 255, 0) 100%),
  linear-gradient(90deg, #38bdf8, #a78bfa, #34d399);
  background-size: 20% 100%, 200% 100%;
  background-repeat: no-repeat, no-repeat;
  background-position: -20% 0, 0 0;
}

@keyframes progress-move {
  0% {
    background-position: -20% 0, 0 0;
  }
  100% {
    background-position: 120% 0, 200% 0;
  }
}

.animate-progress {
  animation: progress-move 1.6s cubic-bezier(.4, 0, .2, 1) infinite;
}

/* Transition overlay */
.fade-enter-active, .fade-leave-active {
  transition: opacity .35s ease;
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

/* Accessiblity: tôn trọng reduced motion */
@media (prefers-reduced-motion: reduce) {
  .blob, .animate-shine, .animate-progress {
    animation: none !important;
  }
}
</style>
