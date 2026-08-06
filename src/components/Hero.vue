<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import AnimatedAvatar from './AnimatedAvatar.vue'

const roles = ['Full Stack Developer','Front-End Developer', 'UI/UX Enthusiast']
const currentRoleIndex = ref(0)
const displayedRole = ref('')
let charIndex = 0
let isDeleting = false
let timeoutId = null

const isLoaded = ref(false)
const sectionRef = ref(null)
let observer = null

// badge teknologi kecil yang mengambang di sekitar avatar
const orbitBadges = [
  { name: 'HTML & CSS', color: '#2563EB', icon: 'M4 3h16l-1.5 15L12 21l-6.5-3L4 3zm4 6h8m-8 4h6', pos: 'badge-tl' },
  { name: 'JavaScript', color: '#D97706', icon: 'M8 4l-2 2v12l2 2m8-16l2 2v12l-2 2M10 15c0 1.5 1 2 2 2s2-.5 2-2-1-2-2-2.5-2-1-2-2.5 1-2 2-2 2 .5 2 2', pos: 'badge-tr' },
  { name: 'Vue.js', color: '#059669', icon: 'M3 4h4l5 9 5-9h4L12 20 3 4zm5 0l4 7 4-7', pos: 'badge-bl' },
  { name: 'Tailwind CSS', color: '#0284C7', icon: 'M6 12c1-3 2.5-4.5 6-4.5S16 9 17 12c-1-1.5-2.5-2-4-2-2 0-3 .8-4 2.5C8 15 6.5 16.5 3 16.5c1-1.5 2-3 3-4.5zm6 0c1-3 2.5-4.5 6-4.5S23 9 24 12', pos: 'badge-br' },
]

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
          Saya membangun aplikasi web secara end-to-end, mulai dari perancangan antarmuka
          pengguna yang responsif, cepat, dan nyaman dipakai menggunakan HTML & CSS, Tailwind
          CSS, JavaScript, dan Vue.js, hingga pengembangan sisi server menggunakan Laravel yang
          terintegrasi dengan database MySQL. Pendekatan ini memungkinkan saya menghadirkan
          solusi web yang utuh, mulai dari tampilan hingga fungsionalitas di balik layar, sesuai
          dengan kebutuhan pengguna.
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

          <div
            v-for="(badge, i) in orbitBadges"
            :key="badge.name"
            class="badge absolute hidden sm:flex items-center gap-2 px-3 py-2 rounded-2xl bg-white shadow-lg shadow-gray-900/5 border border-gray-100 transition-all duration-700 ease-out"
            :class="[badge.pos, isLoaded ? 'opacity-100 scale-100' : 'opacity-0 scale-50']"
            :style="{ transitionDelay: `${850 + i * 120}ms`, '--accent': badge.color }"
          >
            <svg viewBox="0 0 24 24" class="w-4 h-4" fill="none" stroke="var(--accent)" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round">
              <path :d="badge.icon" />
            </svg>
            <span class="text-xs font-semibold text-gray-700 whitespace-nowrap">{{ badge.name }}</span>
          </div>
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

/* posisi badge mengambang di sekitar avatar */
.badge-tl { top: 4%; left: -8%; }
.badge-tr { top: 8%; right: -10%; }
.badge-bl { bottom: 10%; left: -10%; }
.badge-br { bottom: 4%; right: -8%; }

@keyframes badgeFloat {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-8px); }
}
.badge {
  animation: badgeFloat 4s ease-in-out infinite;
}
.badge-tr { animation-duration: 5s; animation-delay: 0.3s; }
.badge-bl { animation-duration: 4.5s; animation-delay: 0.6s; }
.badge-br { animation-duration: 5.5s; animation-delay: 0.9s; }

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
  .badge,
  .scroll-bounce {
    animation: none !important;
  }
}
</style>
