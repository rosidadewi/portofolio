<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import AnimatedAvatar from './AnimatedAvatar.vue'

const roles = ['Software Developer', 'UI/UX Enthusiast']
const currentRoleIndex = ref(0)
const displayedRole = ref('')
let charIndex = 0
let isDeleting = false
let timeoutId = null

const isLoaded = ref(false)
const sectionRef = ref(null)
let observer = null

function typeEffect() {
  const fullText = roles[currentRoleIndex.value]

  if (!isDeleting) {
    displayedRole.value = fullText.slice(0, charIndex + 1)
    charIndex++
    if (charIndex === fullText.length) {
      isDeleting = true
      timeoutId = setTimeout(typeEffect, 1500)
      return
    }
  } else {
    displayedRole.value = fullText.slice(0, charIndex - 1)
    charIndex--
    if (charIndex === 0) {
      isDeleting = false
      currentRoleIndex.value = (currentRoleIndex.value + 1) % roles.length
    }
  }

  const speed = isDeleting ? 50 : 100
  timeoutId = setTimeout(typeEffect, speed)
}

function resetTyping() {
  clearTimeout(timeoutId)
  charIndex = 0
  isDeleting = false
  currentRoleIndex.value = 0
  displayedRole.value = ''
}

function startEverything() {
  resetTyping()
  typeEffect()

  isLoaded.value = false
  requestAnimationFrame(() => {
    requestAnimationFrame(() => {
      isLoaded.value = true
    })
  })
}

function stopEverything() {
  clearTimeout(timeoutId)
  isLoaded.value = false
}

onMounted(() => {
  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          startEverything()
        } else {
          stopEverything()
        }
      })
    },
    { threshold: 0.3 }
  )

  if (sectionRef.value) observer.observe(sectionRef.value)
})

onUnmounted(() => {
  clearTimeout(timeoutId)
  if (observer) observer.disconnect()
})
</script>

<template>
  <section id="hero" ref="sectionRef" class="relative min-h-screen flex items-center justify-center pt-24 pb-16 px-6 bg-gradient-to-br from-orange-50 via-white to-white overflow-hidden">

    <div class="absolute inset-0 pointer-events-none overflow-hidden">
      <div class="absolute -top-24 -left-24 w-96 h-96 bg-primary-200/40 rounded-full blur-3xl blob-float-1"></div>
      <div class="absolute top-1/3 -right-32 w-[28rem] h-[28rem] bg-primary-300/30 rounded-full blur-3xl blob-float-2"></div>
      <div class="absolute -bottom-32 left-1/4 w-80 h-80 bg-primary-100/50 rounded-full blur-3xl blob-float-3"></div>
      <div class="absolute inset-0 dotgrid"></div>
    </div>

    <div class="relative max-w-6xl w-full mx-auto grid md:grid-cols-2 gap-12 items-center">

      <div class="text-center md:text-left">

        <p
          class="inline-flex items-center gap-2 mb-5 px-3 py-1.5 rounded-full border border-primary-200 bg-white/70 backdrop-blur-sm text-sm font-medium text-primary-700 transition-all duration-700 ease-out"
          :class="isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 -translate-y-4'"
          style="transition-delay: 100ms"
        >
          <span class="w-1.5 h-1.5 rounded-full bg-primary-500 animate-pulse"></span>
          Halo, Perkenalkan Saya 👋
        </p>

        <h1
          class="text-4xl md:text-5xl lg:text-6xl font-extrabold leading-tight mb-4 transition-all duration-700 ease-out"
          :class="isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-6'"
          style="transition-delay: 250ms"
        >
          <span class="text-gray-900">Rosida Dewi</span>
          <span class="block bg-gradient-to-r from-primary-600 to-primary-400 bg-clip-text text-transparent">
            Utami
          </span>
        </h1>

        <h2
          class="text-xl md:text-2xl font-semibold text-gray-500 mb-6 h-8 font-mono transition-all duration-700 ease-out"
          :class="isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-4'"
          style="transition-delay: 400ms"
        >
          <span class="text-primary-600">&gt;</span> {{ displayedRole }}<span class="text-primary-600 animate-pulse">|</span>
        </h2>

        <p
          class="text-gray-500 max-w-md mx-auto md:mx-0 mb-8 leading-relaxed transition-all duration-700 ease-out"
          :class="isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-4'"
          style="transition-delay: 550ms"
        >
         Saya merancang dan membangun aplikasi web end-to-end — mulai dari riset
         kebutuhan pengguna, wireframing, hingga desain antarmuka menggunakan Figma
         yang kemudian diimplementasikan menjadi tampilan responsif dan intuitif
         menggunakan HTML, CSS, Tailwind CSS, dan Vue.js — lalu mengimplementasikan sisi
         server dengan Laravel dan MySQL, sehingga solusi yang dihasilkan selaras antara
         pengalaman pengguna dan kebutuhan bisnis.
        </p>

        <div
          class="flex flex-col sm:flex-row items-center gap-5 sm:gap-6 justify-center md:justify-start transition-all duration-700 ease-out"
          :class="isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-4'"
          style="transition-delay: 700ms"
        >

          <!-- Tombol utama + cahaya melingkar -->
          <div class="glow-wrap relative isolate w-full rounded-full sm:w-auto">
            <!-- Halo blur yang menyebar keluar -->
            <span class="glow-halo pointer-events-none absolute -inset-[4px] -z-10 overflow-hidden rounded-full" aria-hidden="true">
              <span class="glow-spin"></span>
            </span>
            <!-- Garis cahaya tipis di tepi -->
            <span class="pointer-events-none absolute -inset-px -z-10 overflow-hidden rounded-full" aria-hidden="true">
              <span class="glow-spin"></span>
            </span>

            <a
              href="#projects"
              class="group relative inline-flex h-[52px] w-full sm:w-auto items-center justify-center gap-4 overflow-hidden rounded-full bg-gradient-to-b from-indigo-500 to-violet-600 pl-7 pr-1.5 text-[15px] font-semibold tracking-wide text-white shadow-lg shadow-indigo-500/30 ring-1 ring-inset ring-white/25 transition-all duration-300 hover:-translate-y-0.5 hover:shadow-xl hover:shadow-indigo-500/40 active:translate-y-0 focus:outline-none focus-visible:ring-2 focus-visible:ring-indigo-400 focus-visible:ring-offset-2"
            >
              <span class="relative z-10">Lihat Proyek</span>
              <span
                class="relative z-10 flex h-10 w-10 items-center justify-center rounded-full bg-white/20 ring-1 ring-inset ring-white/30 transition-colors duration-300 group-hover:bg-white group-hover:text-indigo-700"
              >
                <svg
                  class="h-4 w-4 transition-transform duration-300 group-hover:-rotate-45"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2.2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  aria-hidden="true"
                >
                  <path d="M5 12h14m-6-6l6 6-6 6" />
                </svg>
              </span>
              <!-- Kilau -->
              <span
                class="pointer-events-none absolute inset-y-0 left-0 w-1/3 -translate-x-full -skew-x-12 bg-gradient-to-r from-transparent via-white/30 to-transparent transition-transform duration-700 ease-out group-hover:translate-x-[420%] motion-reduce:hidden"
                aria-hidden="true"
              ></span>
            </a>
          </div>

          <!-- Tombol kedua + cahaya melingkar -->
          <div class="glow-wrap relative isolate w-full rounded-full sm:w-auto">
            <span class="glow-halo pointer-events-none absolute -inset-[4px] -z-10 overflow-hidden rounded-full" aria-hidden="true">
              <span class="glow-spin glow-spin-reverse"></span>
            </span>
            <span class="pointer-events-none absolute -inset-px -z-10 overflow-hidden rounded-full" aria-hidden="true">
              <span class="glow-spin glow-spin-reverse"></span>
            </span>

            <a
              href="#contact"
              class="group inline-flex h-[52px] w-full sm:w-auto items-center justify-center gap-2.5 rounded-full bg-white px-7 text-[15px] font-semibold tracking-wide text-gray-800 transition-all duration-300 hover:-translate-y-0.5 hover:text-indigo-700 hover:shadow-lg hover:shadow-indigo-200/50 active:translate-y-0 focus:outline-none focus-visible:ring-2 focus-visible:ring-indigo-400 focus-visible:ring-offset-2"
            >
              <svg
                class="h-[18px] w-[18px] text-gray-400 transition-all duration-300 group-hover:-rotate-6 group-hover:scale-110 group-hover:text-indigo-500"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="1.8"
                stroke-linecap="round"
                stroke-linejoin="round"
                aria-hidden="true"
              >
                <rect x="3" y="5" width="18" height="14" rx="2.5" />
                <path d="m3.5 7 8.5 6 8.5-6" />
              </svg>
              Hubungi Saya
            </a>
          </div>

        </div>
      </div>

      <div
        class="relative transition-all duration-1000 ease-out"
        :class="isLoaded ? 'opacity-100 scale-100' : 'opacity-0 scale-90'"
        style="transition-delay: 200ms"
      >
        <div class="relative mx-auto max-w-sm">
          <AnimatedAvatar />
        </div>
      </div>

    </div>

    <a
      href="#about"
      class="absolute bottom-6 left-1/2 -translate-x-1/2 flex flex-col items-center gap-1 text-gray-400 hover:text-primary-600 transition-all duration-700 ease-out"
      :class="isLoaded ? 'opacity-100' : 'opacity-0'"
      style="transition-delay: 1200ms"
    >
      <span class="text-[11px] font-mono tracking-wide">scroll</span>
      <svg class="w-4 h-4 scroll-bounce" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <path d="M12 4v16m0 0l-5-5m5 5l5-5" />
      </svg>
    </a>

  </section>
</template>

<style scoped>
@keyframes blobFloat1 {
  0%, 100% { transform: translate(0, 0) scale(1); }
  50% { transform: translate(30px, 40px) scale(1.1); }
}
@keyframes blobFloat2 {
  0%, 100% { transform: translate(0, 0) scale(1); }
  50% { transform: translate(-40px, 30px) scale(1.15); }
}
@keyframes blobFloat3 {
  0%, 100% { transform: translate(0, 0) scale(1); }
  50% { transform: translate(20px, -30px) scale(1.05); }
}

.blob-float-1 {
  animation: blobFloat1 8s ease-in-out infinite;
}
.blob-float-2 {
  animation: blobFloat2 10s ease-in-out infinite;
}
.blob-float-3 {
  animation: blobFloat3 9s ease-in-out infinite;
}

.dotgrid {
  background-image: radial-gradient(rgba(15, 23, 42, 0.07) 1px, transparent 1px);
  background-size: 24px 24px;
  mask-image: radial-gradient(ellipse 65% 55% at 50% 40%, black 30%, transparent 85%);
}

@keyframes scrollBounce {
  0%, 100% { transform: translateY(0); opacity: 0.6; }
  50% { transform: translateY(6px); opacity: 1; }
}
.scroll-bounce {
  animation: scrollBounce 1.8s ease-in-out infinite;
}

/* ---------- Cahaya melingkar di sekitar tombol (warna sama dengan navbar) ---------- */
.glow-spin {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 170%;
  aspect-ratio: 1 / 1;
  background: conic-gradient(
    from 0deg,
    rgba(34, 211, 238, 0.9),
    rgba(99, 102, 241, 0.9),
    rgba(232, 121, 249, 0.9),
    rgba(99, 102, 241, 0.9),
    rgba(34, 211, 238, 0.9)
  );
  transform: translate(-50%, -50%) rotate(0deg);
  animation: glowSpin 6s linear infinite;
  will-change: transform;
}
.glow-spin-reverse {
  animation-direction: reverse;
  animation-duration: 8s;
}
@keyframes glowSpin {
  to {
    transform: translate(-50%, -50%) rotate(360deg);
  }
}

/* Halo blur: cahaya menyebar keluar dari tepi tombol */
.glow-halo {
  filter: blur(10px);
  opacity: 0.55;
  transition: opacity 0.35s ease;
}
.glow-wrap:hover .glow-halo,
.glow-wrap:focus-within .glow-halo {
  opacity: 1;
}

@media (prefers-reduced-motion: reduce) {
  .blob-float-1,
  .blob-float-2,
  .blob-float-3,
  .scroll-bounce,
  .glow-spin {
    animation: none !important;
  }
}
</style>
