<script setup>
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue'
import { projects } from './data/projects'

/* Metadata tampilan link: bedakan repo GitHub vs live demo/domain lain */
function getLinkMeta(link) {
  if (link.includes('github.com')) {
    return { label: 'Lihat Kode', icon: 'github' }
  }
  return { label: 'Kunjungi Proyek', icon: 'external' }
}

const scrollContainer = ref(null)
const visibleCards = ref(new Set())
const cardScales = ref({}) // { index: scaleValue }
const currentIndex = ref(0)
let intersectionObs = null
let rafId = null

const isFirst = computed(() => currentIndex.value === 0)
const isLast = computed(() => currentIndex.value === projects.length - 1)

// Scroll ke kartu tertentu berdasarkan index, bukan jarak tetap.
// Ini memastikan klik panah berkali-kali selalu pindah tepat satu kartu.
function scrollToIndex(index) {
  if (!scrollContainer.value) return
  const clamped = Math.max(0, Math.min(index, projects.length - 1))
  currentIndex.value = clamped

  const card = scrollContainer.value.querySelector(
    `.project-card[data-index="${clamped}"]`
  )
  card?.scrollIntoView({
    behavior: 'smooth',
    inline: 'center',
    block: 'nearest',
  })
}

function scroll(direction) {
  if (direction === 'next' && isLast.value) return
  if (direction === 'prev' && isFirst.value) return
  const nextIndex =
    direction === 'next' ? currentIndex.value + 1 : currentIndex.value - 1
  scrollToIndex(nextIndex)
}

function updateCenterScale() {
  if (!scrollContainer.value) return
  const containerRect = scrollContainer.value.getBoundingClientRect()
  const containerCenter = containerRect.left + containerRect.width / 2

  const cards = scrollContainer.value.querySelectorAll('.project-card')
  const newScales = {}
  let closestIndex = currentIndex.value
  let closestDistance = Infinity

  cards.forEach((card) => {
    const index = Number(card.dataset.index)
    const cardRect = card.getBoundingClientRect()
    const cardCenter = cardRect.left + cardRect.width / 2
    const distance = Math.abs(containerCenter - cardCenter)

    // semakin dekat ke tengah, semakin besar & jelas
    const maxDistance = containerRect.width / 2 + cardRect.width / 2
    const proximity = Math.max(0, 1 - distance / maxDistance)

    // scale antara 0.88 (paling pinggir) - 1.05 (paling tengah)
    const scale = 0.88 + proximity * 0.17
    const opacity = 0.6 + proximity * 0.4

    newScales[index] = { scale, opacity, proximity }

    if (distance < closestDistance) {
      closestDistance = distance
      closestIndex = index
    }
  })

  cardScales.value = newScales
  // sinkronkan currentIndex kalau user scroll/swipe manual
  currentIndex.value = closestIndex
}

function handleScroll() {
  if (rafId) cancelAnimationFrame(rafId)
  rafId = requestAnimationFrame(updateCenterScale)
}

onMounted(async () => {
  intersectionObs = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        const index = Number(entry.target.dataset.index)
        if (entry.isIntersecting) {
          visibleCards.value.add(index)
          visibleCards.value = new Set(visibleCards.value)
        }
      })
    },
    { threshold: 0.2 }
  )

  const cards = scrollContainer.value?.querySelectorAll('.project-card')
  cards?.forEach((card) => intersectionObs.observe(card))

  await nextTick()
  updateCenterScale()

  scrollContainer.value?.addEventListener('scroll', handleScroll, { passive: true })
  window.addEventListener('resize', handleScroll)
})

onUnmounted(() => {
  if (intersectionObs) intersectionObs.disconnect()
  if (rafId) cancelAnimationFrame(rafId)
  scrollContainer.value?.removeEventListener('scroll', handleScroll)
  window.removeEventListener('resize', handleScroll)
})

function getCardStyle(index) {
  const data = cardScales.value[index]
  if (!data) return {}
  return {
    transform: `scale(${data.scale})`,
    opacity: data.opacity,
    zIndex: Math.round(data.proximity * 10),
  }
}
</script>

<template>
  <section id="projects" class="relative py-20 px-6 bg-white overflow-hidden">
    <!-- Dekorasi blob gradien lembut -->
    <div class="pointer-events-none absolute -top-24 -left-24 w-[26rem] h-[26rem] rounded-full bg-primary-100/60 blur-3xl -z-10"></div>
    <div class="pointer-events-none absolute -bottom-32 -right-20 w-[22rem] h-[22rem] rounded-full bg-blue-100/50 blur-3xl -z-10"></div>

    <div class="max-w-6xl mx-auto">
      <span class="inline-flex items-center gap-2 text-xs font-semibold tracking-wide text-primary-700 bg-primary-50 border border-primary-100 px-3 py-1 rounded-full mb-3">
        <span class="w-1.5 h-1.5 rounded-full bg-primary-500"></span>
        Portofolio Proyek
      </span>

      <h2 class="section-title">Proyek Saya</h2>
      <p class="section-subtitle">
        Beberapa proyek yang pernah saya kerjakan untuk belajar dan latihan
      </p>

      <div class="relative mt-4">
        <!-- Tombol Panah Kiri -->
        <button
          @click="scroll('prev')"
          :disabled="isFirst"
          class="absolute -left-4 top-1/2 -translate-y-1/2 z-20 w-10 h-10 rounded-full bg-white shadow-lg border border-gray-100 flex items-center justify-center text-gray-600 transition-all duration-300"
          :class="isFirst
            ? 'opacity-40 cursor-not-allowed'
            : 'hover:text-primary-600 hover:shadow-xl hover:-translate-x-1 hover:-translate-y-1/2 active:scale-90'"
          aria-label="Sebelumnya"
        >
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="w-5 h-5">
            <path d="M15 18l-6-6 6-6" />
          </svg>
        </button>

        <!-- Tombol Panah Kanan -->
        <button
          @click="scroll('next')"
          :disabled="isLast"
          class="absolute -right-4 top-1/2 -translate-y-1/2 z-20 w-10 h-10 rounded-full bg-white shadow-lg border border-gray-100 flex items-center justify-center text-gray-600 transition-all duration-300"
          :class="isLast
            ? 'opacity-40 cursor-not-allowed'
            : 'hover:text-primary-600 hover:shadow-xl hover:translate-x-1 hover:-translate-y-1/2 active:scale-90'"
          aria-label="Selanjutnya"
        >
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="w-5 h-5">
            <path d="M9 18l6-6-6-6" />
          </svg>
        </button>

        <!-- Container Scroll -->
        <div
          ref="scrollContainer"
          class="flex gap-8 overflow-x-auto scroll-smooth snap-x snap-mandatory scrollbar-hide pb-4"
          style="padding-top: 12px; padding-bottom: 24px;"
        >
          <div
            v-for="(project, index) in projects"
            :key="project.title + index"
            :data-index="index"
            class="project-card group flex-shrink-0 w-[85%] sm:w-[45%] lg:w-[calc(33.333%-1.4rem)] snap-center rounded-2xl border border-gray-100 shadow-sm overflow-hidden transition-all duration-500 ease-out hover:shadow-2xl"
            :class="visibleCards.has(index) ? 'opacity-100' : 'opacity-0 translate-y-10'"
            :style="[
              { transitionDelay: visibleCards.has(index) ? `${index * 100}ms` : '0ms' },
              visibleCards.has(index) ? getCardStyle(index) : {}
            ]"
          >
            <!-- Header ala mockup jendela aplikasi -->
            <div class="h-40 bg-gradient-to-br from-primary-500 to-primary-700 relative overflow-hidden">
              <!-- traffic light dots -->
              <div class="absolute top-3 left-4 flex gap-1.5 z-10">
                <span class="w-2 h-2 rounded-full bg-white/40"></span>
                <span class="w-2 h-2 rounded-full bg-white/40"></span>
                <span class="w-2 h-2 rounded-full bg-white/40"></span>
              </div>

              <!-- badge tipe proyek -->
              <span
                class="absolute top-3 right-3 z-10 flex items-center gap-1 text-[10px] font-semibold uppercase tracking-wide text-white/95 bg-white/15 backdrop-blur-sm px-2 py-1 rounded-full"
              >
                <svg v-if="project.type === 'mobile'" viewBox="0 0 24 24" width="11" height="11" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <rect x="5" y="2" width="14" height="20" rx="2" />
                  <line x1="12" y1="18" x2="12.01" y2="18" />
                </svg>
                <svg v-else viewBox="0 0 24 24" width="11" height="11" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <rect x="2" y="3" width="20" height="14" rx="2" />
                  <line x1="8" y1="21" x2="16" y2="21" />
                  <line x1="12" y1="17" x2="12" y2="21" />
                </svg>
                {{ project.type === 'mobile' ? 'Mobile App' : 'Web App' }}
              </span>

              <!-- monogram besar transparan -->
              <span class="absolute -bottom-6 -right-2 text-8xl font-black text-white/10 select-none leading-none">
                {{ project.title.charAt(0) }}
              </span>

              <div class="absolute inset-0 bg-gradient-to-r from-transparent via-white/20 to-transparent -translate-x-full group-hover:translate-x-full transition-transform duration-1000 ease-out"></div>

              <div class="relative z-10 h-full flex items-center justify-center px-6 pt-4">
                <span class="text-white font-semibold text-center transition-transform duration-300 group-hover:scale-105">
                  {{ project.title }}
                </span>
              </div>
            </div>

            <div class="p-6">
              <h3 class="text-lg font-bold text-gray-900 mb-2 transition-colors duration-300 group-hover:text-primary-600">
                {{ project.title }}
              </h3>
              <p class="text-gray-500 text-sm mb-4">{{ project.desc }}</p>
              <div class="flex flex-wrap gap-2 mb-4">
                <span
                  v-for="(tag, tagIndex) in project.tags"
                  :key="tag"
                  class="text-xs px-3 py-1 bg-primary-50 text-primary-600 rounded-full font-medium transition-all duration-300 hover:bg-primary-600 hover:text-white hover:scale-105"
                  :style="{ transitionDelay: `${tagIndex * 50}ms` }"
                >
                  {{ tag }}
                </span>
              </div>
              <a
                :href="project.link"
                target="_blank"
                rel="noopener"
                class="text-primary-600 font-medium text-sm inline-flex items-center gap-1.5 group-hover:gap-2.5 transition-all duration-300"
              >
                <svg v-if="getLinkMeta(project.link).icon === 'github'" viewBox="0 0 24 24" width="15" height="15" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M15 22v-3.87a3.37 3.37 0 0 0-.94-2.61c3.14-.35 6.44-1.54 6.44-7A5.44 5.44 0 0 0 19 4.77 5.07 5.07 0 0 0 18.91.65S17.73.35 15 2.48a13.38 13.38 0 0 0-7 0C5.27.35 4.09.65 4.09.65A5.07 5.07 0 0 0 4 4.77a5.44 5.44 0 0 0-1.5 3.75c0 5.42 3.3 6.61 6.44 7A3.37 3.37 0 0 0 8 18.13V22" />
                </svg>
                <svg v-else viewBox="0 0 24 24" width="15" height="15" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6" />
                  <polyline points="15 3 21 3 21 9" />
                  <line x1="10" y1="14" x2="21" y2="3" />
                </svg>
                {{ getLinkMeta(project.link).label }}
                <span class="transition-transform duration-300 group-hover:translate-x-1">→</span>
              </a>
            </div>
          </div>
        </div>

        <!-- Dot indikator posisi -->
        <div class="flex items-center justify-center gap-2 mt-2">
          <button
            v-for="(project, index) in projects"
            :key="'dot-' + project.title"
            @click="scrollToIndex(index)"
            class="h-1.5 rounded-full transition-all duration-300"
            :class="index === currentIndex ? 'w-6 bg-primary-600' : 'w-1.5 bg-gray-300 hover:bg-gray-400'"
            :aria-label="`Ke proyek ${index + 1}`"
          ></button>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.scrollbar-hide::-webkit-scrollbar {
  display: none;
}
.scrollbar-hide {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
.project-card {
  transition: transform 0.35s ease-out, opacity 0.35s ease-out, box-shadow 0.3s ease;
}
</style>