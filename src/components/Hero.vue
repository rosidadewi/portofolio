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
          class="flex flex-col sm:flex-row gap-4 justify-center md:justify-start transition-all duration-700 ease-out"
          :class="isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-4'"
          style="transition-delay: 700ms"
        >

          <a href="#projects" class="btn-primary relative overflow-hidden group inline-flex items-center justify-center gap-2 transition-all duration-300 hover:shadow-lg hover:shadow-primary-300/50 hover:-translate-y-0.5 active:translate-y-0">
            <span class="relative z-10">Lihat Proyek</span>
            <svg class="relative z-10 w-4 h-4 transition-transform duration-300 group-hover:translate-x-1" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M5 12h14m-6-6l6 6-6 6" />
            </svg>
            <span class="absolute inset-0 bg-white/20 -translate-x-full group-hover:translate-x-full transition-transform duration-700 ease-out"></span>
          </a>

          <a href="#contact" class="btn-outline transition-all duration-300 hover:-translate-y-0.5 hover:shadow-md active:translate-y-0">
            Hubungi Saya
          </a>

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

@media (prefers-reduced-motion: reduce) {
  .blob-float-1,
  .blob-float-2,
  .blob-float-3,
  .scroll-bounce {
    animation: none !important;
  }
}
</style>