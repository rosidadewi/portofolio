<script setup>
import { ref, reactive, computed, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue'

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
    pill: 'bg-blue-100 text-blue-700',
    iconBg: 'bg-blue-500',
    accent: 'border-l-blue-500',
    statIcon: 'bg-blue-100 text-blue-600',
  },
  organisasi: {
    label: 'Organisasi',
    pill: 'bg-purple-100 text-purple-700',
    iconBg: 'bg-purple-500',
    accent: 'border-l-purple-500',
    statIcon: 'bg-purple-100 text-purple-600',
  },
}

/* Ringkasan singkat untuk mengisi kolom kanan di bawah foto */
const summaryStats = computed(() => {
  const kerjaCount = experiences.filter((e) => e.type === 'kerja').length
  const orgCount = experiences.filter((e) => e.type === 'organisasi').length
  return [
    { label: 'Pengalaman Kerja', value: kerjaCount, icon: 'briefcase', style: badgeStyle.kerja.statIcon },
    { label: 'Pengalaman Organisasi', value: orgCount, icon: 'users', style: badgeStyle.organisasi.statIcon },
    { label: 'Total Riwayat', value: experiences.length, icon: 'sparkle', style: 'bg-primary-100 text-primary-600' },
  ]
})

/* ------------------------------------------------------------------ */
/* Album foto — auto swipe                                             */
/* ------------------------------------------------------------------ */
const photos = [
  { src: '/public/bukti magang 2.jpg', caption: ' Presentasi Project Aplikasi web SIMPEG Non-ASN Diskominfo Kota Madiun' },
  { src: '/public/bukti magang.jpg', caption: 'Deployment aplikasi di kantor Diskominfo Kota Madiun' },
  { src: '/public/fosti.jpg', caption: 'Sebagai sekretaris panitia Rapat Pleno 3 FOSTI 2024' },
]

const activeIndex = ref(0)
const isPaused = ref(false)
let autoplayTimer = null

function goTo(index) {
  activeIndex.value = (index + photos.length) % photos.length
}
function next() {
  goTo(activeIndex.value + 1)
}

function startAutoplay() {
  stopAutoplay()
  autoplayTimer = setInterval(() => {
    if (!isPaused.value) next()
  }, 3200)
}
function stopAutoplay() {
  if (autoplayTimer) {
    clearInterval(autoplayTimer)
    autoplayTimer = null
  }
}

/* ------------------------------------------------------------------ */
/* Reveal animation saat timeline masuk viewport                       */
/* ------------------------------------------------------------------ */
const sectionRef = ref(null)
const visibleItems = reactive(experiences.map(() => false))
let observer = null

function setupObserver() {
  const items = sectionRef.value?.querySelectorAll('[data-timeline-item]')
  if (!items) return

  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        const idx = Number(entry.target.dataset.timelineItem)
        if (entry.isIntersecting) {
          visibleItems[idx] = true
        }
      })
    },
    { threshold: 0.25 }
  )

  items.forEach((el) => observer.observe(el))
}

function destroyObserver() {
  if (observer) {
    observer.disconnect()
    observer = null
  }
}

onMounted(() => {
  setupObserver()
  startAutoplay()
})
onUnmounted(() => {
  destroyObserver()
  stopAutoplay()
})
onActivated(() => {
  setupObserver()
  startAutoplay()
})
onDeactivated(() => {
  destroyObserver()
  stopAutoplay()
})
</script>

<template>
  <section id="experiencework" class="relative py-20 px-6 bg-white overflow-hidden" ref="sectionRef">
    <!-- Dekorasi blob gradien lembut -->
    <div class="pointer-events-none absolute -top-24 -right-24 w-[26rem] h-[26rem] rounded-full bg-primary-100/60 blur-3xl -z-10"></div>
    <div class="pointer-events-none absolute -bottom-32 -left-20 w-[22rem] h-[22rem] rounded-full bg-purple-100/50 blur-3xl -z-10"></div>

    <div class="max-w-6xl mx-auto">
      <span class="inline-flex items-center gap-2 text-xs font-semibold tracking-wide text-primary-700 bg-primary-50 border border-primary-100 px-3 py-1 rounded-full mb-3">
        <span class="w-1.5 h-1.5 rounded-full bg-primary-500"></span>
        Timeline Perjalanan
      </span>

      <h2 class="section-title">Pengalaman Kerja & Organisasi</h2>
      <p class="section-subtitle">
        Perjalanan saya dalam dunia kerja maupun organisasi
      </p>

      <!-- Legenda kategori -->
      <div class="flex flex-wrap gap-4 mt-6 mb-2">
        <span class="flex items-center gap-2 text-xs font-medium text-gray-500">
          <span class="w-2.5 h-2.5 rounded-full bg-blue-500"></span> Pengalaman Kerja
        </span>
        <span class="flex items-center gap-2 text-xs font-medium text-gray-500">
          <span class="w-2.5 h-2.5 rounded-full bg-purple-500"></span> Pengalaman Organisasi
        </span>
      </div>

      <div class="mt-8 grid lg:grid-cols-5 gap-10 lg:gap-14 items-start">
        <!-- Timeline -->
        <div class="lg:col-span-3 relative">
          <div class="absolute left-[13px] top-2 bottom-2 w-px bg-gradient-to-b from-gray-300 via-gray-200 to-transparent"></div>

          <div class="space-y-10">
            <div
              v-for="(exp, index) in experiences"
              :key="exp.company + exp.period"
              :data-timeline-item="index"
              class="relative pl-11 transition-all duration-700 ease-out"
              :class="visibleItems[index] ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-6'"
              :style="{ transitionDelay: `${index * 120}ms` }"
            >
              <!-- Titik timeline berbentuk ikon -->
              <span
                class="absolute left-0 top-0 w-7 h-7 rounded-full flex items-center justify-center text-white ring-4 ring-white shadow-md"
                :class="badgeStyle[exp.type].iconBg"
              >
                <svg v-if="exp.type === 'kerja'" viewBox="0 0 24 24" width="14" height="14" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <rect x="2" y="7" width="20" height="14" rx="2" />
                  <path d="M16 21V5a2 2 0 0 0-2-2h-4a2 2 0 0 0-2 2v16" />
                </svg>
                <svg v-else viewBox="0 0 24 24" width="14" height="14" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2" />
                  <circle cx="9" cy="7" r="4" />
                  <path d="M22 21v-2a4 4 0 0 0-3-3.87" />
                  <path d="M16 3.13a4 4 0 0 1 0 7.75" />
                </svg>
              </span>

              <div
                class="group bg-gray-50 hover:bg-white rounded-2xl p-6 transition-all duration-300 border border-gray-100 hover:border-primary-200 border-l-4 hover:shadow-lg hover:-translate-y-0.5"
                :class="badgeStyle[exp.type].accent"
              >
                <div class="flex flex-wrap items-center gap-2 mb-2">
                  <span
                    class="text-[11px] font-semibold uppercase tracking-wide px-2 py-0.5 rounded-full"
                    :class="badgeStyle[exp.type].pill"
                  >
                    {{ badgeStyle[exp.type].label }}
                  </span>
                  <span
                    v-if="index === 0"
                    class="flex items-center gap-1.5 text-[11px] font-semibold text-emerald-700 bg-emerald-50 px-2 py-0.5 rounded-full"
                  >
                    <span class="relative flex h-2 w-2">
                      <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span>
                      <span class="relative inline-flex rounded-full h-2 w-2 bg-emerald-500"></span>
                    </span>
                    Terbaru
                  </span>
                </div>

                <div class="flex flex-wrap items-baseline justify-between gap-2 mb-1">
                  <h3 class="text-lg font-semibold text-gray-800 group-hover:text-primary-700 transition-colors">
                    {{ exp.role }}
                  </h3>
                  <span class="text-xs font-medium text-primary-600 bg-primary-100 px-2.5 py-1 rounded-full whitespace-nowrap">
                    {{ exp.period }}
                  </span>
                </div>
                <p class="text-sm font-medium text-gray-500 mb-3">{{ exp.company }}</p>
                <p class="text-gray-600 text-sm leading-relaxed mb-4">{{ exp.desc }}</p>

                <div class="flex flex-wrap gap-2">
                  <span
                    v-for="tag in exp.tags"
                    :key="tag"
                    class="text-xs font-medium text-gray-500 bg-white border border-gray-200 rounded-full px-2.5 py-1"
                  >
                    {{ tag }}
                  </span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Album foto auto-swipe + ringkasan -->
        <div class="lg:col-span-2 lg:sticky lg:top-24 space-y-6">
          <div class="relative">
            <!-- Lapisan foto dekoratif di belakang (efek tumpukan album) -->
            <div class="hidden sm:block absolute inset-0 rounded-[1.75rem] bg-gray-300/70 rotate-6 scale-[0.96] translate-x-3 translate-y-3 -z-10"></div>
            <div class="hidden sm:block absolute inset-0 rounded-[1.75rem] bg-gray-200/80 -rotate-3 scale-[0.98] -translate-x-2 translate-y-2 -z-10"></div>

            <!-- Label mengambang -->
            <div class="absolute -top-3 -left-3 z-10 flex items-center gap-1.5 bg-white text-gray-700 text-[11px] font-semibold px-3 py-1.5 rounded-full shadow-md border border-gray-100">
              <svg viewBox="0 0 24 24" width="13" height="13" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-primary-600">
                <path d="M14.5 4h-5L7 7H4a2 2 0 0 0-2 2v9a2 2 0 0 0 2 2h16a2 2 0 0 0 2-2V9a2 2 0 0 0-2-2h-3l-2.5-3z" />
                <circle cx="12" cy="13" r="3" />
              </svg>
              Galeri Momen
            </div>

            <div
              class="relative rounded-[1.75rem] overflow-hidden shadow-2xl aspect-[4/5] bg-gray-900 group"
              @mouseenter="isPaused = true"
              @mouseleave="isPaused = false"
            >
              <transition-group name="fade-slide" tag="div" class="absolute inset-0">
                <img
                  v-for="(photo, i) in photos"
                  v-show="i === activeIndex"
                  :key="photo.src"
                  :src="photo.src"
                  :alt="photo.caption"
                  class="absolute inset-0 w-full h-full object-cover"
                />
              </transition-group>

              <div class="absolute inset-0 bg-gradient-to-t from-black/70 via-black/0 to-black/10 pointer-events-none"></div>

              <div class="absolute bottom-0 left-0 right-0 p-5 flex items-end justify-between gap-3">
                <p class="text-white text-sm font-medium leading-snug drop-shadow-sm">
                  {{ photos[activeIndex].caption }}
                </p>
                <span class="shrink-0 text-[11px] font-semibold text-white/90 bg-white/15 backdrop-blur-sm rounded-full px-2.5 py-1 tabular-nums">
                  {{ activeIndex + 1 }} / {{ photos.length }}
                </span>
              </div>

              <div class="absolute top-4 left-1/2 -translate-x-1/2 flex gap-1.5">
                <button
                  v-for="(photo, i) in photos"
                  :key="'dot-' + photo.src"
                  @click="goTo(i)"
                  class="h-1.5 rounded-full transition-all duration-300"
                  :class="i === activeIndex ? 'w-6 bg-white' : 'w-1.5 bg-white/40 hover:bg-white/70'"
                  :aria-label="`Ke foto ${i + 1}`"
                ></button>
              </div>
            </div>
          </div>

          <p class="text-center text-xs text-gray-400">
            Momen keseharian di balik layar pekerjaan & organisasi
          </p>

          <!-- Kartu ringkasan statistik -->
          <div class="bg-gray-50 border border-gray-100 rounded-2xl p-5 grid grid-cols-3 gap-3">
            <div v-for="stat in summaryStats" :key="stat.label" class="flex flex-col items-center text-center gap-2">
              <span class="w-9 h-9 rounded-full flex items-center justify-center" :class="stat.style">
                <svg v-if="stat.icon === 'briefcase'" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <rect x="2" y="7" width="20" height="14" rx="2" />
                  <path d="M16 21V5a2 2 0 0 0-2-2h-4a2 2 0 0 0-2 2v16" />
                </svg>
                <svg v-else-if="stat.icon === 'users'" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2" />
                  <circle cx="9" cy="7" r="4" />
                  <path d="M22 21v-2a4 4 0 0 0-3-3.87" />
                  <path d="M16 3.13a4 4 0 0 1 0 7.75" />
                </svg>
                <svg v-else viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M12 3v3M12 18v3M4.2 4.2l2.1 2.1M17.7 17.7l2.1 2.1M3 12h3M18 12h3M4.2 19.8l2.1-2.1M17.7 6.3l2.1-2.1" />
                </svg>
              </span>
              <div>
                <p class="text-xl font-bold text-gray-800 tabular-nums leading-none">{{ stat.value }}</p>
                <p class="text-[10.5px] text-gray-500 leading-snug mt-1">{{ stat.label }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: opacity 0.7s ease, transform 0.7s ease;
}
.fade-slide-enter-from {
  opacity: 0;
  transform: scale(1.03);
}
.fade-slide-leave-to {
  opacity: 0;
}

@media (prefers-reduced-motion: reduce) {
  .fade-slide-enter-active,
  .fade-slide-leave-active {
    transition: none;
  }
  .animate-ping {
    animation: none;
  }
}
</style>