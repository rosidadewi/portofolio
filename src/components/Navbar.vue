<script setup>
import { ref, watch, nextTick, onMounted, onUnmounted } from 'vue'

// Ganti dengan path fotomu, mis. file di folder public/ atau hasil import
const photo = '/foto-profil.png'

const isOpen = ref(false)
const isScrolled = ref(false)
const progress = ref(0)
const activeSection = ref('#hero')

const menu = [
  { label: 'Beranda', href: '#hero' },
  { label: 'Tentang', href: '#about' },
  { label: 'Skill', href: '#skills' },
  { label: 'Pengalaman', href: '#experiencework' },
  { label: 'Proyek', href: '#projects' },
  { label: 'Kontak', href: '#contact' },
]

/* ---------- Indikator aktif yang bergeser mengikuti menu ---------- */
const linkEls = {}
const indicator = ref({ x: 0, w: 0, visible: false, animated: false })

function setLinkRef(href, el) {
  if (el) linkEls[href] = el
}

function updateIndicator() {
  const el = linkEls[activeSection.value]
  if (!el) return
  const wasVisible = indicator.value.visible
  indicator.value = {
    x: el.offsetLeft,
    w: el.offsetWidth,
    visible: true,
    // placement pertama tanpa animasi supaya tidak meluncur dari 0
    animated: wasVisible,
  }
}

/* ---------- Scroll spy ---------- */
let observer = null
let rafId = null

function setupScrollSpy() {
  const sections = menu
    .map((item) => document.querySelector(item.href))
    .filter(Boolean)

  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          activeSection.value = `#${entry.target.id}`
        }
      })
    },
    { rootMargin: '-40% 0px -50% 0px', threshold: 0 }
  )

  sections.forEach((section) => observer.observe(section))
}

function updateScrollState() {
  const y = window.scrollY
  isScrolled.value = y > 16
  const max = document.documentElement.scrollHeight - window.innerHeight
  progress.value = max > 0 ? Math.min(1, y / max) : 0

  // Di dasar halaman, section terakhir bisa terlalu pendek untuk terdeteksi
  if (max > 0 && max - y < 4) {
    activeSection.value = menu[menu.length - 1].href
  }
}

function handleScroll() {
  if (rafId) cancelAnimationFrame(rafId)
  rafId = requestAnimationFrame(updateScrollState)
}

function closeMenu() {
  isOpen.value = false
}

function handleKeydown(e) {
  if (e.key === 'Escape') closeMenu()
}

/* Kunci scroll halaman saat menu mobile terbuka */
watch(isOpen, (open) => {
  document.body.style.overflow = open ? 'hidden' : ''
})

watch(activeSection, updateIndicator)

onMounted(() => {
  setupScrollSpy()
  updateScrollState()
  nextTick(updateIndicator)
  // font web yang terlambat dimuat mengubah lebar menu
  document.fonts?.ready.then(updateIndicator)

  window.addEventListener('scroll', handleScroll, { passive: true })
  window.addEventListener('resize', updateIndicator)
  window.addEventListener('keydown', handleKeydown)
})

onUnmounted(() => {
  if (observer) observer.disconnect()
  if (rafId) cancelAnimationFrame(rafId)
  document.body.style.overflow = ''
  window.removeEventListener('scroll', handleScroll)
  window.removeEventListener('resize', updateIndicator)
  window.removeEventListener('keydown', handleKeydown)
})
</script>

<template>
<<<<<<< HEAD
  <header class="pointer-events-none fixed inset-x-0 top-0 z-50 px-4 pt-3 md:pt-4">
    <!-- Garis progres baca -->
    <div
      class="progress fixed inset-x-0 top-0 h-0.5 origin-left bg-gradient-to-r from-primary-400 to-primary-600"
      :style="{ transform: `scaleX(${progress})` }"
      aria-hidden="true"
    ></div>
=======
  <header class="fixed top-0 left-0 w-full bg-pink-300/80 backdrop-blur-md z-50 shadow-sm">
    <nav class="max-w-6xl mx-auto flex items-center justify-between px-6 py-4">
      <!-- Logo/brand di navbar -->
      <a href="#hero" class="text-xl font-bold text-blue-900 font-montserrat">Portofolio</a>
>>>>>>> a9132c1f4ec41569b5fb607e4111cf15dea2f9df

    <!-- Latar redup di belakang menu mobile; klik untuk menutup -->
    <transition name="fade">
      <div
        v-if="isOpen"
        class="pointer-events-auto fixed inset-0 -z-10 bg-gray-900/25 backdrop-blur-sm md:hidden"
        aria-hidden="true"
        @click="closeMenu"
      ></div>
    </transition>

    <div class="relative mx-auto max-w-5xl">
      <nav
        class="bar pointer-events-auto flex items-center justify-between gap-2 rounded-full py-2 pl-4 pr-2 ring-1 backdrop-blur-xl"
        :class="
          isScrolled
            ? 'bg-white/90 shadow-xl shadow-gray-900/10 ring-gray-200'
            : 'bg-white/60 shadow-sm ring-gray-200/70'
        "
        aria-label="Navigasi utama"
      >
        <!-- Brand -->
        <a
          href="#hero"
          class="group flex items-center gap-2.5 rounded-full pr-2 text-lg font-bold tracking-tight text-gray-900 focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-500 focus-visible:ring-offset-2"
          @click="closeMenu"
        >
          <img
            :src="photo"
            alt="Foto profil Rosida"
            width="36"
            height="36"
            class="logo h-9 w-9 rounded-full object-cover shadow-md shadow-gray-900/15 ring-2 ring-white"
          />
          Portofolio
        </a>

        <!-- Menu desktop dengan indikator yang bergeser -->
        <ul class="relative hidden items-center text-sm font-medium md:flex">
          <span
            class="indicator pointer-events-none absolute left-0 top-0 h-full rounded-full bg-primary-50 ring-1 ring-primary-100"
            :class="{ 'is-animated': indicator.animated }"
            :style="{
              width: `${indicator.w}px`,
              transform: `translateX(${indicator.x}px)`,
              opacity: indicator.visible ? 1 : 0,
            }"
            aria-hidden="true"
          ></span>

          <li v-for="item in menu" :key="item.href">
            <a
              :ref="(el) => setLinkRef(item.href, el)"
              :href="item.href"
              class="link relative z-10 block rounded-full px-3.5 py-2 focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-500"
              :class="
                activeSection === item.href
                  ? 'font-semibold text-primary-700'
                  : 'text-gray-600 hover:text-gray-900'
              "
              :aria-current="activeSection === item.href ? 'true' : undefined"
            >
              {{ item.label }}
            </a>
          </li>
        </ul>

<<<<<<< HEAD
        <div class="flex items-center gap-1">
          <!-- Hamburger mobile -->
          <button
            type="button"
            class="flex h-10 w-10 flex-col items-center justify-center gap-1.5 rounded-full transition hover:bg-gray-900/5 focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-500 md:hidden"
            :aria-expanded="isOpen"
            aria-controls="mobile-menu"
            aria-label="Buka atau tutup menu"
            @click="isOpen = !isOpen"
=======
      <!-- Tombol Hamburger Mobile -->
      <button
        class="md:hidden flex flex-col gap-1.5 p-2"
        @click="isOpen = !isOpen"
        aria-label="Toggle menu"
      >
        <span
          class="w-6 h-0.5 bg-gray-800 transition-all duration-300 origin-center"
          :class="{ 'rotate-45 translate-y-2': isOpen }"
        ></span>
        <span
          class="w-6 h-0.5 bg-gray-800 transition-all duration-300"
          :class="{ 'opacity-0 scale-0': isOpen }"
        ></span>
        <span
          class="w-6 h-0.5 bg-gray-800 transition-all duration-300 origin-center"
          :class="{ '-rotate-45 -translate-y-2': isOpen }"
        ></span>
      </button>
    </nav>

    <!-- Menu Mobile -->
    <transition name="menu">
      <ul v-if="isOpen" class="md:hidden flex flex-col gap-1 px-6 pb-6 bg-white/95">
        <li
          v-for="(item, index) in menu"
          :key="item.href"
          class="menu-item"
          :style="{ animationDelay: isOpen ? `${index * 60}ms` : '0ms' }"
        >
          <a
            :href="item.href"
            class="flex items-center gap-2 py-2.5 font-medium transition-all duration-300"
            :class="
              activeSection === item.href
                ? 'text-primary-600 font-semibold pl-2'
                : 'text-gray-600 hover:text-primary-600 hover:pl-2'
            "
            @click="closeMenu"
>>>>>>> a9132c1f4ec41569b5fb607e4111cf15dea2f9df
          >
            <span
              class="h-0.5 w-5 origin-center rounded-full bg-gray-800 transition-all duration-300"
              :class="{ 'translate-y-2 rotate-45': isOpen }"
            ></span>
            <span
              class="h-0.5 w-5 rounded-full bg-gray-800 transition-all duration-300"
              :class="{ 'scale-0 opacity-0': isOpen }"
            ></span>
            <span
              class="h-0.5 w-5 origin-center rounded-full bg-gray-800 transition-all duration-300"
              :class="{ '-translate-y-2 -rotate-45': isOpen }"
            ></span>
          </button>
        </div>
      </nav>

      <!-- Menu mobile: kartu yang turun dari pil -->
      <transition name="menu">
        <div
          v-if="isOpen"
          id="mobile-menu"
          class="pointer-events-auto absolute inset-x-0 top-full mt-2 origin-top overflow-hidden rounded-3xl bg-white/95 p-3 shadow-2xl shadow-gray-900/15 ring-1 ring-gray-200 backdrop-blur-xl md:hidden"
        >
          <ul class="flex flex-col gap-1">
            <li
              v-for="(item, index) in menu"
              :key="item.href"
              class="menu-item"
              :style="{ animationDelay: `${index * 40}ms` }"
            >
              <a
                :href="item.href"
                class="flex items-center justify-between rounded-2xl px-4 py-3 text-base font-medium transition-colors focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-500"
                :class="
                  activeSection === item.href
                    ? 'bg-primary-50 font-semibold text-primary-700'
                    : 'text-gray-700 hover:bg-gray-100'
                "
                :aria-current="activeSection === item.href ? 'true' : undefined"
                @click="closeMenu"
              >
                {{ item.label }}
                <span
                  class="h-2 w-2 rounded-full bg-primary-600 transition-all duration-300"
                  :class="activeSection === item.href ? 'scale-100 opacity-100' : 'scale-0 opacity-0'"
                ></span>
              </a>
            </li>
          </ul>
        </div>
      </transition>
    </div>
  </header>
</template>

<style scoped>
.bar {
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}
.link {
  transition: color 0.2s ease;
}
.logo {
  transition: transform 0.35s cubic-bezier(0.3, 0.7, 0.2, 1);
}
.group:hover .logo {
  transform: rotate(-8deg) scale(1.06);
}

/* Progres mengikuti scroll, jadi tanpa transisi agar tidak tertinggal */
.progress {
  will-change: transform;
}

/* Indikator aktif */
.indicator {
  will-change: transform, width;
}
.indicator.is-animated {
  transition: transform 0.4s cubic-bezier(0.3, 0.7, 0.2, 1),
    width 0.4s cubic-bezier(0.3, 0.7, 0.2, 1), opacity 0.2s ease;
}

/* Latar redup */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.25s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Kartu menu mobile */
.menu-enter-active,
.menu-leave-active {
  transition: opacity 0.25s ease, transform 0.25s ease;
}
.menu-enter-from,
.menu-leave-to {
  opacity: 0;
  transform: translateY(-8px) scale(0.97);
}

/* Item muncul satu-satu */
.menu-item {
  opacity: 0;
<<<<<<< HEAD
  transform: translateX(-10px);
  animation: menuItemIn 0.3s ease forwards;
}
=======
  transform: translateX(-12px);
  animation: menuItemIn 0.35s ease forwards;
}

>>>>>>> a9132c1f4ec41569b5fb607e4111cf15dea2f9df
@keyframes menuItemIn {
  to {
    opacity: 1;
    transform: translateX(0);
  }
<<<<<<< HEAD
}

@media (prefers-reduced-motion: reduce) {
  .bar,
  .link,
  .logo,
  .indicator.is-animated,
  .fade-enter-active,
  .fade-leave-active,
  .menu-enter-active,
  .menu-leave-active {
    transition: none;
  }
  .group:hover .logo {
    transform: none;
  }
  .menu-item {
    animation: none;
    opacity: 1;
    transform: none;
  }
=======
>>>>>>> a9132c1f4ec41569b5fb607e4111cf15dea2f9df
}
</style>
