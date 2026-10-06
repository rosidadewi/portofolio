<script setup>
import { ref, computed, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue'
import { projects } from './data/projects'
import { skills } from './data/skills'

// Sumber tunggal: src/data/projects.js dan src/data/skills.js
const totalProjects = computed(() => projects.length)
const totalTechnologies = computed(() => skills.length)

const stats = computed(() => [
  { label: 'Proyek Selesai', value: totalProjects.value, suffix: '+' },
  { label: 'Tahun Belajar', value: 3, suffix: '+' },
  { label: 'Teknologi Dikuasai', value: totalTechnologies.value, suffix: '+' },
])

const focusAreas = [
  {
    title: 'Desain UI/UX',
    desc: 'Riset pengguna dan prototipe di Figma',
    icon: 'M12 19l7-7 3 3-7 7-3-3zM18 13l-1.5-7.5L2 2l3.5 14.5L13 18l5-5zM2 2l7.586 7.586M11 13a2 2 0 100-4 2 2 0 000 4z',
  },
  {
    title: 'Front-end',
    desc: 'HTML & CSS, JavaScript, Vue.js, Tailwind CSS, Bootstrap',
    icon: 'M16 18l6-6-6-6M8 6l-6 6 6 6',
  },
  {
    title: 'Back-end',
    desc: 'Laravel Blade dan database SQLite',
    icon: 'M3 5c0-1.1 4-2 9-2s9 .9 9 2-4 2-9 2-9-.9-9-2zM3 5v14c0 1.1 4 2 9 2s9-.9 9-2V5M3 12c0 1.1 4 2 9 2s9-.9 9-2',
  },
  {
    title: 'Mobile',
    desc: 'Aplikasi Android dengan Java',
    icon: 'M7 2h10a2 2 0 012 2v16a2 2 0 01-2 2H7a2 2 0 01-2-2V4a2 2 0 012-2zM12 18h.01',
  },
]

// Daftar untuk marquee (digandakan agar putarannya mulus)
const tools = [
  'Figma',
  'HTML & CSS',
  'JavaScript',
  'Vue.js',
  'Tailwind CSS',
  'Bootstrap',
  'Laravel Blade',
  'SQLite',
  'Java (Android)',
  'GitHub',
]
const marqueeItems = [...tools, ...tools]

const displayValues = ref(stats.value.map(() => 0))
const sectionRef = ref(null)
const revealed = ref(false) // animasi masuk, hanya sekali
let observer = null
let runId = 0 // mencegah animasi lama menimpa animasi baru

const prefersReducedMotion = () =>
  typeof window !== 'undefined' &&
  window.matchMedia?.('(prefers-reduced-motion: reduce)').matches

function resetValues() {
  runId++
  displayValues.value = stats.value.map(() => 0)
}

function animateCount(index, target, id, duration = 1600) {
  const startTime = performance.now()

  function tick(now) {
    if (id !== runId) return
    const progress = Math.min((now - startTime) / duration, 1)
    const eased = 1 - Math.pow(1 - progress, 3)
    displayValues.value[index] = Math.floor(target * eased)

    if (progress < 1) requestAnimationFrame(tick)
    else displayValues.value[index] = target
  }

  requestAnimationFrame(tick)
}

function startAnimation() {
  resetValues()

  if (prefersReducedMotion()) {
    displayValues.value = stats.value.map((s) => s.value)
    return
  }

  const id = runId
  stats.value.forEach((stat, i) => animateCount(i, stat.value, id))
}

function destroyObserver() {
  if (observer) {
    observer.disconnect()
    observer = null
  }
}

function setupObserver() {
  destroyObserver() // hindari observer ganda (onMounted + onActivated)

  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          revealed.value = true
          startAnimation()
        } else {
          resetValues()
        }
      })
    },
    { threshold: 0.3 }
  )

  if (sectionRef.value) observer.observe(sectionRef.value)
}

onMounted(setupObserver)
onUnmounted(destroyObserver)
onActivated(() => {
  resetValues()
  setupObserver()
})
onDeactivated(destroyObserver)
</script>

<template>
  <section
    id="about"
    ref="sectionRef"
    class="relative overflow-hidden bg-white py-24 md:py-32"
  >
    <!-- latar: cahaya lembut -->
    <div
      class="pointer-events-none absolute -right-32 -top-32 h-96 w-96 rounded-full bg-primary-100 opacity-70 blur-3xl"
      aria-hidden="true"
    ></div>
    <div
      class="pointer-events-none absolute -bottom-40 -left-32 h-96 w-96 rounded-full bg-primary-50 blur-3xl"
      aria-hidden="true"
    ></div>

    <div class="relative mx-auto max-w-6xl px-6">
      <h2 class="section-title">Tentang Saya</h2>
      <p class="section-subtitle">
        Kenali lebih dekat siapa saya dan apa yang saya kerjakan
      </p>

      <div class="mt-16 grid items-center gap-20 md:grid-cols-12 md:gap-14">
        <!-- Foto -->
        <div
          class="transition-all duration-700 ease-out motion-reduce:transition-none md:col-span-5"
          :class="revealed ? 'translate-y-0 opacity-100' : 'translate-y-6 opacity-0'"
        >
          <div class="group relative mx-auto max-w-sm md:max-w-none">
            <!-- pola titik -->
            <div
              class="absolute -left-6 -top-6 h-32 w-32 text-primary-300"
              style="background-image: radial-gradient(currentColor 1.5px, transparent 1.5px); background-size: 14px 14px;"
              aria-hidden="true"
            ></div>

            <!-- latar gradien miring -->
            <div
              class="absolute inset-0 rotate-6 rounded-t-full rounded-b-3xl bg-gradient-to-br from-primary-400 via-primary-500 to-primary-700 transition-transform duration-500 group-hover:rotate-3"
              aria-hidden="true"
            ></div>

            <img
              src="/foto-profil.png"
              alt="Foto Rosida Dewi Utami"
              class="relative aspect-[4/5] w-full rounded-t-full rounded-b-3xl object-cover shadow-2xl ring-4 ring-white"
              loading="lazy"
            />

            <!-- lencana atas (melayang pelan) -->
            <div
              class="badge-float absolute -left-3 top-1/3 flex items-center gap-2 rounded-full border border-white/60 bg-white/85 px-4 py-2 text-sm font-semibold text-gray-800 shadow-lg backdrop-blur-md sm:-left-8"
            >
              <span class="h-2 w-2 rounded-full bg-primary-500"></span>
              Hi!
            </div>

            <!-- lencana bawah (melayang pelan, fase berbeda) -->
            <div
              class="badge-float badge-float-alt absolute -bottom-5 -right-2 rounded-2xl border border-white/60 bg-white/85 px-5 py-3 shadow-xl backdrop-blur-md sm:-right-6"
            >
              <p class="text-sm font-semibold text-gray-900">Rosida Dewi Utami</p>
              <p class="text-xs text-gray-500">Software Developer</p>
            </div>
          </div>
        </div>

        <!-- Konten -->
        <div
          class="transition-all delay-150 duration-700 ease-out motion-reduce:transition-none md:col-span-7"
          :class="revealed ? 'translate-y-0 opacity-100' : 'translate-y-6 opacity-0'"
        >
          <h3
            class="text-3xl font-bold leading-tight tracking-tight text-gray-900 md:text-5xl"
          >
            Membangun aplikasi web yang bersih, modern, dan mudah digunakan.
          </h3>

          <p class="mt-6 max-w-prose text-lg leading-relaxed text-gray-600">
            Saya mulai dari riset kebutuhan pengguna dan perancangan antarmuka,
            lalu mewujudkannya menjadi aplikasi web maupun Android yang utuh.
            Saya senang mempelajari teknologi baru dan menerapkan praktik
            terbaik dalam pengembangan web.
          </p>

          <!-- Area fokus -->
          <ul class="mt-8 space-y-3">
            <li
              v-for="item in focusAreas"
              :key="item.title"
              class="group/row flex items-center gap-4 rounded-2xl border border-transparent p-3 transition hover:border-primary-200 hover:bg-primary-50/60"
            >
              <span
                class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-primary-100 text-primary-600 transition group-hover/row:bg-primary-600 group-hover/row:text-white"
              >
                <svg
                  class="h-5 w-5"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  aria-hidden="true"
                >
                  <path :d="item.icon" />
                </svg>
              </span>
              <span>
                <span class="block font-semibold text-gray-900">{{ item.title }}</span>
                <span class="block text-sm text-gray-600">{{ item.desc }}</span>
              </span>
            </li>
          </ul>

          <!-- Statistik: satu panel dengan border gradien -->
          <div
            class="mt-10 rounded-3xl bg-gradient-to-br from-primary-300 via-primary-100 to-primary-300 p-px shadow-lg"
          >
            <div
              class="grid grid-cols-3 divide-x divide-gray-100 rounded-[calc(1.5rem-1px)] bg-white py-6"
            >
              <div
                v-for="(stat, index) in stats"
                :key="stat.label"
                class="px-2 text-center sm:px-4"
              >
                <p
                  class="bg-gradient-to-br from-primary-500 to-primary-700 bg-clip-text text-4xl font-extrabold tabular-nums tracking-tight text-transparent md:text-5xl"
                >
                  {{ displayValues[index] }}{{ stat.suffix }}
                </p>
                <p class="mt-2 text-xs leading-tight text-gray-500 sm:text-sm">
                  {{ stat.label }}
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Marquee teknologi, selebar layar -->
    <div class="marquee relative mt-24" aria-label="Teknologi yang saya gunakan">
      <div class="marquee-track flex w-max items-center gap-10">
        <template v-for="(tool, i) in marqueeItems" :key="i">
          <span
            class="text-2xl font-bold tracking-tight text-gray-300 transition-colors hover:text-primary-600 md:text-4xl"
            :aria-hidden="i >= tools.length ? 'true' : undefined"
          >
            {{ tool }}
          </span>
          <span class="h-2 w-2 rounded-full bg-primary-300" aria-hidden="true"></span>
        </template>
      </div>
    </div>
  </section>
</template>

<style scoped>
/* lencana melayang pelan */
@keyframes badge-float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-8px); }
}
.badge-float {
  animation: badge-float 5s ease-in-out infinite;
}
.badge-float-alt {
  animation-delay: -2.5s;
}

/* marquee: bergeser setengah lebar (satu set daftar) lalu mengulang */
@keyframes marquee {
  from { transform: translateX(0); }
  to { transform: translateX(-50%); }
}
.marquee {
  overflow: hidden;
  -webkit-mask-image: linear-gradient(to right, transparent, #000 12%, #000 88%, transparent);
  mask-image: linear-gradient(to right, transparent, #000 12%, #000 88%, transparent);
}
.marquee-track {
  animation: marquee 45s linear infinite;
}
.marquee:hover .marquee-track {
  animation-play-state: paused;
}

@media (prefers-reduced-motion: reduce) {
  .badge-float,
  .marquee-track {
    animation: none;
  }
}
</style>