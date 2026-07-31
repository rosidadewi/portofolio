<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const isOpen = ref(false)
const menu = [
  { label: 'Beranda', href: '#hero' },
  { label: 'Tentang', href: '#about' },
  { label: 'Skill', href: '#skills' },
  { label: 'Experience', href: '#experiencework' },
  { label: 'Proyek', href: '#projects' },
  { label: 'Kontak', href: '#contact' },
]

const activeSection = ref('#hero')

function closeMenu() {
  isOpen.value = false
}

let observer = null

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
    {
      rootMargin: '-40% 0px -50% 0px',
      threshold: 0,
    }
  )

  sections.forEach((section) => observer.observe(section))
}

onMounted(() => {
  setupScrollSpy()
})

onUnmounted(() => {
  if (observer) observer.disconnect()
})
</script>

<template>
 <header class="fixed top-0 left-0 w-full bg-pink-300/80 backdrop-blur-md z-50 shadow-sm">
    <nav class="max-w-6xl mx-auto flex items-center justify-between px-6 py-4">
     <!-- Logo/brand di navbar -->
<a href="#hero" class="text-xl font-bold text-blue-900 font-montserrat">Portofolio</a>

      <!-- Menu Desktop -->
      <ul class="hidden md:flex items-center gap-8 text-sm font-medium">
        <li v-for="item in menu" :key="item.href" class="relative group">
          <a
            :href="item.href"
            class="relative inline-block pb-1 transition-all duration-300 ease-out"
            :class="
              activeSection === item.href
                ? 'text-primary-600 font-semibold -translate-y-0.5'
                : 'text-gray-600 hover:text-primary-600 hover:-translate-y-0.5'
            "
          >
            {{ item.label }}

            <!-- underline aktif: meluncur dari tengah -->
            <span
              class="absolute left-1/2 -bottom-0.5 h-0.5 bg-primary-600 transition-all duration-300 ease-out"
              :class="
                activeSection === item.href
                  ? 'w-full -translate-x-1/2'
                  : 'w-0 -translate-x-1/2 group-hover:w-full'
              "
            ></span>
          </a>
        </li>
      </ul>

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
          :style="{ transitionDelay: isOpen ? `${index * 60}ms` : '0ms' }"
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
          >
            <span
              class="w-1.5 h-1.5 rounded-full bg-primary-600 transition-all duration-300"
              :class="activeSection === item.href ? 'scale-100 opacity-100' : 'scale-0 opacity-0'"
            ></span>
            {{ item.label }}
          </a>
        </li>
      </ul>
    </transition>
  </header>
</template>

<style scoped>
/* Transisi container menu mobile */
.menu-enter-active,
.menu-leave-active {
  transition: all 0.3s ease;
}
.menu-enter-from,
.menu-leave-to {
  opacity: 0;
  max-height: 0;
}
.menu-enter-to,
.menu-leave-from {
  opacity: 1;
  max-height: 500px;
}

/* Animasi staggered per item: geser + fade masuk satu-satu */
.menu-item {
  opacity: 0;
  transform: translateX(-12px);
  transition: opacity 0.35s ease, transform 0.35s ease;
}
.menu-enter-active .menu-item,
.menu-enter-to .menu-item {
  opacity: 1;
  transform: translateX(0);
}
</style>