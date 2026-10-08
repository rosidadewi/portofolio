<script setup>
import { ref, computed, onMounted, onUnmounted, onActivated, nextTick } from 'vue'
import { projects } from './data/projects'

/* Metadata tampilan link: bedakan repo GitHub vs live demo/domain lain */
function getLinkMeta(link) {
  if (link.includes('github.com')) {
    return { label: 'Lihat Kode', icon: 'github' }
  }
  return { label: 'Kunjungi Proyek', icon: 'external' }
}

const prefersReducedMotion = () =>
  typeof window !== 'undefined' &&
  window.matchMedia?.('(prefers-reduced-motion: reduce)').matches

const scrollContainer = ref(null)
const visibleCards = ref(new Set())
const cardScales = ref({}) // { index: { scale, opacity, proximity } }
const currentIndex = ref(0)
let intersectionObs = null
let rafId = null

const isFirst = computed(() => currentIndex.value === 0)
const isLast = computed(() => currentIndex.value === projects.length - 1)
const pad = (n) => String(n).padStart(2, '0')

// Scroll ke kartu berdasarkan index, bukan jarak tetap,
// sehingga klik panah berkali-kali selalu pindah tepat satu kartu.
function scrollToIndex(index) {
  if (!scrollContainer.value) return
  const clamped = Math.max(0, Math.min(index, projects.length - 1))
  currentIndex.value = clamped

  const card = scrollContainer.value.querySelector(
    `.project-card[data-index="${clamped}"]`
  )
  card?.scrollIntoView({
    behavior: prefersReducedMotion() ? 'auto' : 'smooth',
    inline: 'center',
    block: 'nearest',
  })
}

function scroll(direction) {
  if (direction === 'next' && isLast.value) return
  if (direction === 'prev' && isFirst.value) return
  scrollToIndex(direction === 'next' ? currentIndex.value + 1 : currentIndex.value - 1)
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

    newScales[index] = {
      scale: 0.92 + proximity * 0.1, // 0.92 (pinggir) sampai 1.02 (tengah)
      opacity: 0.5 + proximity * 0.5,
      proximity,
    }

    if (distance < closestDistance) {
      closestDistance = distance
      closestIndex = index
    }
  })

  cardScales.value = newScales
  // sinkronkan currentIndex kalau pengguna scroll/swipe manual
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
        if (entry.isIntersecting) {
          visibleCards.value.add(Number(entry.target.dataset.index))
          visibleCards.value = new Set(visibleCards.value)
        }
      })
    },
    { threshold: 0.2 }
  )

  scrollContainer.value
    ?.querySelectorAll('.project-card')
    .forEach((card) => intersectionObs.observe(card))

  await nextTick()
  updateCenterScale()

  scrollContainer.value?.addEventListener('scroll', handleScroll, { passive: true })
  window.addEventListener('resize', handleScroll, { passive: true })
})

// dipanggil saat komponen kembali aktif (mis. di dalam KeepAlive)
onActivated(updateCenterScale)

onUnmounted(() => {
  intersectionObs?.disconnect()
  if (rafId) cancelAnimationFrame(rafId)
  scrollContainer.value?.removeEventListener('scroll', handleScroll)
  window.removeEventListener('resize', handleScroll)
})

function getCardStyle(index) {
  const data = cardScales.value[index]
  if (!data || prefersReducedMotion()) return {}
  return {
    transform: `scale(${data.scale})`,
    opacity: data.opacity,
    zIndex: Math.round(data.proximity * 10),
  }
}
</script>

<template>
  <section
    id="projects"
    class="relative overflow-hidden bg-gray-50 px-6 py-24 md:py-32"
  >
    <!-- latar: grid halus yang memudar di tepi + satu cahaya aksen -->
    <div
      class="bg-grid pointer-events-none absolute inset-0 text-gray-900/[0.06]"
      aria-hidden="true"
    ></div>
    <div
      class="pointer-events-none absolute left-1/2 top-0 h-80 w-[40rem] -translate-x-1/2 rounded-full bg-primary-200/50 blur-3xl"
      aria-hidden="true"
    ></div>

    <div class="relative mx-auto max-w-6xl">
      <!-- Header: judul di kiri, kontrol navigasi di kanan -->
      <div class="flex flex-wrap items-end justify-between gap-6">
        <div class="max-w-xl">
          <h2 class="text-4xl font-extrabold tracking-tight text-gray-900 md:text-5xl">
            Proyek Saya
          </h2>
          <p class="mt-3 text-base leading-relaxed text-gray-600 md:text-lg">
            Beberapa proyek yang pernah saya kerjakan.
          </p>
        </div>

        <div class="flex items-center gap-4">
          <p class="text-sm font-medium tabular-nums text-gray-500" aria-live="polite">
            <span class="text-lg font-bold text-gray-900">{{ pad(currentIndex + 1) }}</span>
            / {{ pad(projects.length) }}
          </p>

          <div class="flex gap-2">
            <button
              type="button"
              :disabled="isFirst"
              class="nav-btn"
              aria-label="Proyek sebelumnya"
              @click="scroll('prev')"
            >
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5" aria-hidden="true">
                <path d="M15 18l-6-6 6-6" />
              </svg>
            </button>
            <button
              type="button"
              :disabled="isLast"
              class="nav-btn"
              aria-label="Proyek berikutnya"
              @click="scroll('next')"
            >
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5" aria-hidden="true">
                <path d="M9 18l6-6-6-6" />
              </svg>
            </button>
          </div>
        </div>
      </div>

      <div class="relative mt-10">
        <!-- Container scroll -->
        <div
          ref="scrollContainer"
          class="scrollbar-hide -mx-2 flex snap-x snap-mandatory gap-6 overflow-x-auto scroll-smooth px-2 pb-10 pt-4 motion-reduce:scroll-auto"
        >
          <article
            v-for="(project, index) in projects"
            :key="project.title + index"
            :data-index="index"
            class="project-card group flex w-[85%] flex-shrink-0 snap-center flex-col overflow-hidden rounded-[1.75rem] bg-white ring-1 sm:w-[45%] lg:w-[calc(33.333%-1rem)]"
            :class="[
              visibleCards.has(index) ? 'opacity-100' : 'translate-y-10 opacity-0',
              index === currentIndex
                ? 'shadow-2xl shadow-primary-900/10 ring-primary-300'
                : 'shadow-sm ring-gray-200',
            ]"
            :style="visibleCards.has(index) ? getCardStyle(index) : {}"
          >
            <!-- Header: gradien + pratinjau antarmuka sederhana -->
            <div class="relative h-48 overflow-hidden bg-gradient-to-br from-primary-500 via-primary-600 to-primary-800">
              <!-- pola titik -->
              <div
                class="absolute inset-0 text-white/15"
                style="background-image: radial-gradient(currentColor 1.2px, transparent 1.2px); background-size: 18px 18px;"
                aria-hidden="true"
              ></div>
              <!-- kilau -->
              <div class="absolute -right-10 -top-10 h-44 w-44 rounded-full bg-white/20 blur-2xl" aria-hidden="true"></div>

              <!-- tipe proyek -->
              <span
                class="absolute left-4 top-4 z-10 inline-flex items-center gap-1.5 rounded-full bg-white/15 px-3 py-1 text-xs font-semibold text-white ring-1 ring-inset ring-white/30 backdrop-blur-md"
              >
                <svg v-if="project.type === 'mobile'" viewBox="0 0 24 24" width="12" height="12" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="5" y="2" width="14" height="20" rx="2" />
                  <line x1="12" y1="18" x2="12.01" y2="18" />
                </svg>
                <svg v-else viewBox="0 0 24 24" width="12" height="12" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="2" y="3" width="20" height="14" rx="2" />
                  <line x1="8" y1="21" x2="16" y2="21" />
                  <line x1="12" y1="17" x2="12" y2="21" />
                </svg>
                {{ project.type === 'mobile' ? 'Mobile App' : 'Web App' }}
              </span>

              <!-- monogram sebagai identitas visual proyek -->
              <span
                class="absolute -bottom-10 -left-2 select-none text-[10rem] font-black leading-none text-white/20 transition-transform duration-500 group-hover:-translate-y-2"
                aria-hidden="true"
              >
                {{ project.title.charAt(0) }}
              </span>

              <!-- pratinjau perangkat -->
              <div
                v-if="project.type === 'mobile'"
                class="absolute -bottom-6 right-8 h-36 w-20 rotate-6 rounded-2xl bg-white p-2 shadow-xl shadow-primary-900/30 transition-transform duration-500 group-hover:-translate-y-2 group-hover:rotate-3"
                aria-hidden="true"
              >
                <div class="mx-auto h-1 w-6 rounded-full bg-gray-200"></div>
                <div class="mt-3 h-10 rounded-lg bg-primary-100"></div>
                <div class="mt-2 h-2 w-3/4 rounded-full bg-gray-200"></div>
                <div class="mt-1.5 h-2 w-1/2 rounded-full bg-gray-100"></div>
              </div>
              <div
                v-else
                class="absolute -bottom-4 right-6 h-28 w-44 rotate-3 overflow-hidden rounded-xl bg-white shadow-xl shadow-primary-900/30 transition-transform duration-500 group-hover:-translate-y-2 group-hover:rotate-1"
                aria-hidden="true"
              >
                <div class="flex items-center gap-1 border-b border-gray-100 px-3 py-2">
                  <span class="h-1.5 w-1.5 rounded-full bg-gray-300"></span>
                  <span class="h-1.5 w-1.5 rounded-full bg-gray-300"></span>
                  <span class="h-1.5 w-1.5 rounded-full bg-gray-300"></span>
                </div>
                <div class="space-y-2 p-3">
                  <div class="h-8 rounded-md bg-primary-100"></div>
                  <div class="h-2 w-3/4 rounded-full bg-gray-200"></div>
                  <div class="h-2 w-1/2 rounded-full bg-gray-100"></div>
                </div>
              </div>
            </div>

            <div class="flex flex-1 flex-col p-6">
              <h3 class="text-xl font-bold tracking-tight text-gray-900">
                {{ project.title }}
              </h3>
              <p class="mt-2 text-sm leading-relaxed text-gray-600">{{ project.desc }}</p>

              <ul class="mb-6 mt-5 flex flex-wrap gap-2">
                <li
                  v-for="tag in project.tags"
                  :key="tag"
                  class="rounded-lg bg-gray-100 px-2.5 py-1 text-xs font-medium text-gray-700"
                >
                  {{ tag }}
                </li>
              </ul>

              <a
                :href="project.link"
                target="_blank"
                rel="noopener noreferrer"
                class="mt-auto inline-flex w-full items-center justify-center gap-2 rounded-xl bg-gray-900 px-4 py-3 text-sm font-semibold text-white transition hover:bg-primary-600 focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-500 focus-visible:ring-offset-2 active:scale-[0.98]"
              >
                <svg v-if="getLinkMeta(project.link).icon === 'github'" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <path d="M15 22v-3.87a3.37 3.37 0 0 0-.94-2.61c3.14-.35 6.44-1.54 6.44-7A5.44 5.44 0 0 0 19 4.77 5.07 5.07 0 0 0 18.91.65S17.73.35 15 2.48a13.38 13.38 0 0 0-7 0C5.27.35 4.09.65 4.09.65A5.07 5.07 0 0 0 4 4.77a5.44 5.44 0 0 0-1.5 3.75c0 5.42 3.3 6.61 6.44 7A3.37 3.37 0 0 0 8 18.13V22" />
                </svg>
                <svg v-else viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <path d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6" />
                  <polyline points="15 3 21 3 21 9" />
                  <line x1="10" y1="14" x2="21" y2="3" />
                </svg>
                {{ getLinkMeta(project.link).label }}
              </a>
            </div>
          </article>
        </div>

        <!-- Indikator posisi -->
        <div class="flex items-center justify-center gap-2">
          <button
            v-for="(project, index) in projects"
            :key="'dot-' + project.title + index"
            type="button"
            class="h-1.5 rounded-full transition-all duration-300 focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-500 focus-visible:ring-offset-2"
            :class="index === currentIndex ? 'w-8 bg-primary-600' : 'w-1.5 bg-gray-300 hover:bg-gray-400'"
            :aria-label="`Ke proyek ${index + 1}`"
            :aria-current="index === currentIndex ? 'true' : undefined"
            @click="scrollToIndex(index)"
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

/* Grid halus, memudar ke arah tepi */
.bg-grid {
  background-image:
    linear-gradient(to right, currentColor 1px, transparent 1px),
    linear-gradient(to bottom, currentColor 1px, transparent 1px);
  background-size: 48px 48px;
  -webkit-mask-image: radial-gradient(ellipse at 50% 30%, #000 20%, transparent 75%);
  mask-image: radial-gradient(ellipse at 50% 30%, #000 20%, transparent 75%);
}

/* Tombol navigasi: selalu terlihat (ramah layar sentuh), redup saat nonaktif */
.nav-btn {
  display: flex;
  height: 2.75rem;
  width: 2.75rem;
  align-items: center;
  justify-content: center;
  border-radius: 9999px;
  background: #fff;
  color: #374151;
  box-shadow: 0 0 0 1px #e5e7eb, 0 1px 2px rgb(0 0 0 / 0.05);
  transition: transform 0.2s ease, box-shadow 0.2s ease, color 0.2s ease, opacity 0.2s ease;
}
.nav-btn:hover:not(:disabled) {
  color: #fff;
  background: #111827;
  box-shadow: none;
}
.nav-btn:active:not(:disabled) {
  transform: scale(0.92);
}
.nav-btn:focus-visible {
  outline: 2px solid currentColor;
  outline-offset: 2px;
}
.nav-btn:disabled {
  opacity: 0.35;
  cursor: not-allowed;
}

/* Skala/opasitas mengikuti scroll, jadi transisinya dibuat singkat dan tanpa delay */
.project-card {
  transition: transform 0.35s ease-out, opacity 0.35s ease-out, box-shadow 0.3s ease,
    --tw-ring-color 0.3s ease;
}

@media (prefers-reduced-motion: reduce) {
  .project-card,
  .nav-btn {
    transition: none;
  }
}
</style>
