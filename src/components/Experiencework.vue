<script setup>
import { ref, reactive, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue'

/* ------------------------------------------------------------------ */
/* Data pengalaman kerja & organisasi                                   */
/* type: 'kerja' | 'organisasi' -> menentukan warna badge & ikon timeline */
/* ------------------------------------------------------------------ */
const experiences = [
  {
    type: 'kerja',
    role: 'Frontend Developer Internship',
    company: 'Dinas Komunikasi dan Informatika Kota Madiun',
    period: 'Maret 2025 — April 2025',
    desc: 'Mengembangkan antarmuka sistem informasi kepegawaian untuk instansi Diskominfo Kota Madiun selama masa Praktek Kerja Nyata. Menyusun tampilan halaman menggunakan template Blade (Laravel) yang responsif dan konsisten dengan kebutuhan pengguna instansi.',
    tags: ['Vue.js', 'Tailwind', 'REST API', 'Laravel Blade'],
  },
  {
    type: 'organisasi',
    role: 'Pengurus Divisi Keorganisasian',
    company: 'Forum Open Source Teknik Informatika (FOSTI) UMS',
    period: 'Jan 2024 — Des 2024',
    desc: 'Mengoordinasikan dan melaksanakan program kerja organisasi hingga tercapai tujuan utama divisi keorganisasian.',
    tags: ['Organisasi', 'Koordinasi', 'Kepemimpinan'],
  },
  {
    type: 'organisasi',
    role: 'Anggota Divisi Keorganisasian',
    company: 'Forum Open Source Teknik Informatika (FOSTI) UMS',
    period: 'Jan 2023 — Des 2023',
    desc: 'Berkontribusi aktif dalam kegiatan organisasi serta membantu meningkatkan efektivitas kerja tim dan komunikasi antar anggota.',
    tags: ['Organisasi', 'Kerja Tim', 'Komunikasi'],
  },
]

const badgeStyle = {
  kerja: {
    label: 'Kerja',
    pill: 'bg-blue-50 text-blue-700 ring-blue-200',
    node: 'bg-blue-500',
  },
  organisasi: {
    label: 'Organisasi',
    pill: 'bg-purple-50 text-purple-700 ring-purple-200',
    node: 'bg-purple-500',
  },
}

/* ------------------------------------------------------------------ */
/* Album foto. Pergantian otomatis digerakkan oleh animasi progress     */
/* (@animationend), jadi tidak perlu timer terpisah.                    */
/* File di folder public diakses dari root ("/nama-file"), tanpa "/public" */
/* ------------------------------------------------------------------ */
const photos = [
  { src: '/bukti magang 2.jpg', caption: 'Presentasi project aplikasi web SIMPEG Non-ASN Diskominfo Kota Madiun' },
  { src: '/bukti magang.jpg', caption: 'Deployment aplikasi di kantor Diskominfo Kota Madiun' },
  { src: '/fosti.jpg', caption: 'Sebagai sekretaris panitia Rapat Pleno 3 FOSTI 2024' },
]

const activeIndex = ref(0)
const isPaused = ref(false)

function goTo(index) {
  activeIndex.value = (index + photos.length) % photos.length
}
const next = () => goTo(activeIndex.value + 1)
const prev = () => goTo(activeIndex.value - 1)

/* ------------------------------------------------------------------ */
/* Reveal item timeline + garis progres mengikuti scroll               */
/* ------------------------------------------------------------------ */
const sectionRef = ref(null)
const listRef = ref(null)
const visibleItems = reactive(experiences.map(() => false))
const progress = ref(0) // 0-100
let observer = null
let ticking = false

const prefersReducedMotion = () =>
  typeof window !== 'undefined' &&
  window.matchMedia?.('(prefers-reduced-motion: reduce)').matches

function updateProgress() {
  ticking = false
  const el = listRef.value
  if (!el) return
  if (prefersReducedMotion()) {
    progress.value = 100
    return
  }
  const rect = el.getBoundingClientRect()
  const trigger = window.innerHeight * 0.65
  const p = (trigger - rect.top) / rect.height
  progress.value = Math.min(Math.max(p, 0), 1) * 100
}

function onScroll() {
  if (!ticking) {
    ticking = true
    requestAnimationFrame(updateProgress)
  }
}

function destroyObserver() {
  if (observer) {
    observer.disconnect()
    observer = null
  }
}

function setupObserver() {
  destroyObserver() // hindari observer ganda (onMounted + onActivated)
  const items = sectionRef.value?.querySelectorAll('[data-timeline-item]')
  if (!items) return

  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          visibleItems[Number(entry.target.dataset.timelineItem)] = true
        }
      })
    },
    { threshold: 0.2 }
  )
  items.forEach((el) => observer.observe(el))
}

function activate() {
  setupObserver()
  window.addEventListener('scroll', onScroll, { passive: true })
  window.addEventListener('resize', onScroll, { passive: true })
  updateProgress()
}
function deactivate() {
  destroyObserver()
  window.removeEventListener('scroll', onScroll)
  window.removeEventListener('resize', onScroll)
}

onMounted(activate)
onUnmounted(deactivate)
onActivated(activate)
onDeactivated(deactivate)
</script>

<template>
  <section
    id="experiencework"
    ref="sectionRef"
    class="relative overflow-hidden bg-white px-6 py-24 md:py-32"
  >
    <!-- latar: cahaya lembut -->
    <div
      class="pointer-events-none absolute -right-32 -top-32 h-96 w-96 rounded-full bg-primary-100 opacity-70 blur-3xl"
      aria-hidden="true"
    ></div>
    <div
      class="pointer-events-none absolute -bottom-40 -left-32 h-96 w-96 rounded-full bg-purple-100 opacity-50 blur-3xl"
      aria-hidden="true"
    ></div>

    <div class="relative mx-auto max-w-6xl">
      <h2 class="section-title">Pengalaman Kerja &amp; Organisasi</h2>
      <p class="section-subtitle">
        Perjalanan saya dalam dunia kerja maupun organisasi
      </p>

      <div class="mt-16 grid items-start gap-16 lg:grid-cols-5 lg:gap-14">
        <!-- Timeline -->
        <ol ref="listRef" class="relative lg:col-span-3">
          <!-- jalur garis -->
          <div
            class="absolute bottom-2 left-[13px] top-2 w-0.5 rounded-full bg-gray-200"
            aria-hidden="true"
          >
            <!-- garis yang terisi mengikuti scroll -->
            <div
              class="w-full rounded-full bg-gradient-to-b from-primary-500 to-primary-300"
              :style="{ height: progress + '%' }"
            ></div>
          </div>

          <li
            v-for="(exp, index) in experiences"
            :key="exp.company + exp.period"
            :data-timeline-item="index"
            class="relative pb-8 pl-12 transition-all duration-700 ease-out last:pb-0 motion-reduce:translate-y-0 motion-reduce:opacity-100 motion-reduce:transition-none"
            :class="visibleItems[index] ? 'translate-y-0 opacity-100' : 'translate-y-6 opacity-0'"
            :style="{ transitionDelay: `${index * 100}ms` }"
          >
            <!-- titik timeline -->
            <span
              class="absolute left-0 top-6 flex h-7 w-7 items-center justify-center rounded-full text-white shadow-md ring-4 ring-white"
              :class="badgeStyle[exp.type].node"
            >
              <svg
                v-if="exp.type === 'kerja'"
                viewBox="0 0 24 24"
                width="14"
                height="14"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                aria-hidden="true"
              >
                <rect x="2" y="7" width="20" height="14" rx="2" />
                <path d="M16 21V5a2 2 0 0 0-2-2h-4a2 2 0 0 0-2 2v16" />
              </svg>
              <svg
                v-else
                viewBox="0 0 24 24"
                width="14"
                height="14"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                aria-hidden="true"
              >
                <path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2" />
                <circle cx="9" cy="7" r="4" />
                <path d="M22 21v-2a4 4 0 0 0-3-3.87" />
                <path d="M16 3.13a4 4 0 0 1 0 7.75" />
              </svg>
            </span>

            <article
              class="group relative rounded-3xl border border-gray-200 bg-white p-6 shadow-sm transition duration-300 hover:-translate-y-1 hover:shadow-xl md:p-7"
            >
              <div class="flex flex-wrap items-center gap-2">
                <span
                  class="rounded-full px-2.5 py-0.5 text-xs font-semibold ring-1"
                  :class="badgeStyle[exp.type].pill"
                >
                  {{ badgeStyle[exp.type].label }}
                </span>
                <span
                  v-if="index === 0"
                  class="flex items-center gap-1.5 rounded-full bg-emerald-50 px-2.5 py-0.5 text-xs font-semibold text-emerald-700 ring-1 ring-emerald-200"
                >
                  <span class="relative flex h-1.5 w-1.5">
                    <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-emerald-400 opacity-75"></span>
                    <span class="relative inline-flex h-1.5 w-1.5 rounded-full bg-emerald-500"></span>
                  </span>
                  Terbaru
                </span>
              </div>

              <h3 class="mt-4 text-xl font-bold tracking-tight text-gray-900 md:text-2xl">
                {{ exp.role }}
              </h3>
              <p class="mt-1 font-medium text-primary-700">{{ exp.company }}</p>

              <p class="mt-3 inline-flex items-center gap-1.5 rounded-full bg-gray-100 px-3 py-1 text-xs font-medium tabular-nums text-gray-600">
                <svg viewBox="0 0 24 24" width="13" height="13" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="3" y="4" width="18" height="18" rx="2" />
                  <path d="M16 2v4M8 2v4M3 10h18" />
                </svg>
                {{ exp.period }}
              </p>

              <p class="mt-4 text-sm leading-relaxed text-gray-600 md:text-base">
                {{ exp.desc }}
              </p>

              <ul class="mt-5 flex flex-wrap gap-2">
                <li
                  v-for="tag in exp.tags"
                  :key="tag"
                  class="rounded-full border border-gray-200 bg-white px-3 py-1 text-xs font-medium text-gray-600 transition hover:border-primary-300 hover:bg-primary-50 hover:text-primary-700"
                >
                  {{ tag }}
                </li>
              </ul>
            </article>
          </li>
        </ol>

        <!-- Galeri + ringkasan -->
        <div class="space-y-6 lg:sticky lg:top-24 lg:col-span-2">
          <div class="relative mx-auto max-w-sm lg:max-w-none">
            <!-- latar gradien miring, senada dengan bagian Tentang Saya -->
            <div
              class="absolute inset-0 rotate-3 rounded-[2rem] bg-gradient-to-br from-primary-400 via-primary-500 to-primary-700"
              aria-hidden="true"
            ></div>

            <div
              class="relative aspect-[4/5] overflow-hidden rounded-[2rem] bg-gray-900 shadow-2xl ring-4 ring-white"
              role="region"
              aria-roledescription="carousel"
              aria-label="Galeri momen"
              @mouseenter="isPaused = true"
              @mouseleave="isPaused = false"
              @focusin="isPaused = true"
              @focusout="isPaused = false"
            >
              <transition name="fade-zoom">
                <img
                  :key="photos[activeIndex].src"
                  :src="photos[activeIndex].src"
                  :alt="photos[activeIndex].caption"
                  class="absolute inset-0 h-full w-full object-cover"
                />
              </transition>

              <div
                class="pointer-events-none absolute inset-0 bg-gradient-to-b from-black/40 via-transparent to-black/60"
                aria-hidden="true"
              ></div>

              <!-- indikator gaya story: segmen terisi sesuai waktu tayang -->
              <div class="absolute inset-x-5 top-3 flex gap-1.5">
                <button
                  v-for="(photo, i) in photos"
                  :key="'seg-' + photo.src"
                  type="button"
                  class="group/seg flex-1 py-2 focus:outline-none"
                  :aria-label="`Ke foto ${i + 1}`"
                  :aria-current="i === activeIndex ? 'true' : undefined"
                  @click="goTo(i)"
                >
                  <span class="relative block h-1 overflow-hidden rounded-full bg-white/30 group-focus-visible/seg:ring-2 group-focus-visible/seg:ring-white">
                    <span v-if="i < activeIndex" key="done" class="absolute inset-0 bg-white"></span>
                    <span
                      v-else-if="i === activeIndex"
                      :key="'bar-' + activeIndex"
                      class="story-bar absolute inset-0 origin-left bg-white"
                      :style="{ animationPlayState: isPaused ? 'paused' : 'running' }"
                      @animationend="next"
                    ></span>
                  </span>
                </button>
              </div>

              <!-- panah -->
              <button
                type="button"
                class="absolute left-3 top-1/2 flex h-9 w-9 -translate-y-1/2 items-center justify-center rounded-full bg-white/20 text-white backdrop-blur-md transition hover:bg-white/40 focus:outline-none focus-visible:ring-2 focus-visible:ring-white"
                aria-label="Foto sebelumnya"
                :class="isPaused ? 'translate-x-0 opacity-100' : '-translate-x-2 opacity-0'"
                @click="prev"
              >
                <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M15 18l-6-6 6-6" /></svg>
              </button>
              <button
                type="button"
                class="absolute right-3 top-1/2 flex h-9 w-9 -translate-y-1/2 items-center justify-center rounded-full bg-white/20 text-white backdrop-blur-md transition hover:bg-white/40 focus:outline-none focus-visible:ring-2 focus-visible:ring-white"
                aria-label="Foto berikutnya"
                :class="isPaused ? 'translate-x-0 opacity-100' : 'translate-x-2 opacity-0'"
                @click="next"
              >
                <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M9 18l6-6-6-6" /></svg>
              </button>

              <!-- keterangan dalam panel kaca -->
              <div class="absolute inset-x-4 bottom-4 rounded-2xl border border-white/20 bg-black/30 p-4 backdrop-blur-md">
                <p class="text-sm font-medium leading-snug text-white">
                  {{ photos[activeIndex].caption }}
                </p>
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
/* transisi pergantian foto */
.fade-zoom-enter-active,
.fade-zoom-leave-active {
  transition: opacity 0.8s ease, transform 1.2s ease;
}
.fade-zoom-enter-from {
  opacity: 0;
  transform: scale(1.05);
}
.fade-zoom-leave-to {
  opacity: 0;
}

/* progress segmen aktif: saat selesai, memicu foto berikutnya */
@keyframes story {
  from { transform: scaleX(0); }
  to { transform: scaleX(1); }
}
.story-bar {
  animation: story 4s linear forwards;
}

@media (prefers-reduced-motion: reduce) {
  .fade-zoom-enter-active,
  .fade-zoom-leave-active {
    transition: none;
  }
  /* tanpa autoplay: segmen aktif langsung penuh, foto diganti manual */
  .story-bar {
    animation: none;
  }
  .animate-ping {
    animation: none;
  }
}
</style>