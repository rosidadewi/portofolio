<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const visible = ref(false)

function onScroll() {
  visible.value = window.scrollY > 300
}

function scrollToTop() {
  const reduce = window.matchMedia('(prefers-reduced-motion: reduce)').matches
  window.scrollTo({ top: 0, behavior: reduce ? 'auto' : 'smooth' })
}

onMounted(() => {
  onScroll()
  window.addEventListener('scroll', onScroll, { passive: true })
})
onUnmounted(() => window.removeEventListener('scroll', onScroll))
</script>

<template>
  <!-- tombol melayang, tengah bawah -->
  <div
    class="top-glow fixed inset-x-0 bottom-6 z-50 mx-auto h-11 w-11 transition-all duration-300 motion-reduce:transition-none sm:bottom-8"
    :class="visible ? 'translate-y-0 opacity-100' : 'pointer-events-none translate-y-4 opacity-0'"
  >
    <!-- halo blur yang bernapas -->
    <span class="glow-halo pointer-events-none absolute -inset-[4px] overflow-hidden rounded-full" aria-hidden="true">
      <span class="glow-spin"></span>
    </span>

    <!-- garis cahaya tipis dengan komet yang berputar -->
    <span class="glow-ring pointer-events-none absolute -inset-[3px] rounded-full" aria-hidden="true">
      <span class="glow-spin"></span>
    </span>

    <button
      type="button"
      aria-label="Kembali ke atas"
      title="Kembali ke atas"
      :tabindex="visible ? 0 : -1"
      class="relative z-10 flex h-11 w-11 items-center justify-center rounded-full bg-gray-900 text-gray-200 ring-1 ring-white/10 transition duration-200 hover:bg-primary-500 hover:text-white focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-primary-300 motion-reduce:transition-none"
      @click="scrollToTop"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2.4"
        stroke-linecap="round"
        stroke-linejoin="round"
        class="h-5 w-5"
        aria-hidden="true"
      >
        <path d="M12 19V5M5 12l7-7 7 7" />
      </svg>
    </button>
  </div>
</template>

<style scoped>
.glow-spin {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 200%;
  aspect-ratio: 1 / 1;
  background: conic-gradient(
    from 0deg,
    transparent 0%,
    transparent 52%,
    rgba(34, 211, 238, 0.9) 68%,
    rgba(99, 102, 241, 1) 84%,
    rgba(232, 121, 249, 1) 96%,
    transparent 100%
  );
  transform: translate(-50%, -50%) rotate(0deg);
  animation: glowSpin 4s linear infinite;
  will-change: transform;
}
@keyframes glowSpin {
  to {
    transform: translate(-50%, -50%) rotate(360deg);
  }
}

.glow-ring {
  padding: 1.5px;
  overflow: hidden;
  background: rgba(129, 140, 248, 0.22);
  -webkit-mask: linear-gradient(#000 0 0) content-box, linear-gradient(#000 0 0);
  -webkit-mask-composite: xor;
  mask: linear-gradient(#000 0 0) content-box exclude, linear-gradient(#000 0 0);
}

.glow-halo {
  filter: blur(7px);
  opacity: 0.5;
  animation: glowBreath 4s ease-in-out infinite;
  transition: opacity 0.3s ease;
}
.top-glow:hover .glow-halo {
  opacity: 0.95;
}
@keyframes glowBreath {
  0%, 100% { opacity: 0.35; transform: scale(0.98); }
  50% { opacity: 0.7; transform: scale(1.04); }
}

@media (prefers-reduced-motion: reduce) {
  .glow-spin,
  .glow-halo {
    animation: none;
  }
  .glow-halo {
    transition: none;
  }
}
</style>