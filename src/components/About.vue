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

// Gugusan bintang: posisi (%), ukuran (px), warna, delay & durasi kedip
// Warna memakai hex langsung (bukan class Tailwind) agar pasti tampil berwarna
const stars = [
  { x: 6, y: 10, size: 26, color: '#22d3ee', delay: 0, dur: 3.4 },
  { x: 38, y: 2, size: 12, color: '#e879f9', delay: -1.1, dur: 2.8 },
  { x: 62, y: 18, size: 18, color: '#818cf8', delay: -2.2, dur: 3.9 },
  { x: 20, y: 42, size: 14, color: '#a78bfa', delay: -0.6, dur: 3.1 },
  { x: 48, y: 36, size: 8, color: '#67e8f9', delay: -1.8, dur: 2.5 },
  { x: 2, y: 68, size: 10, color: '#f0abfc', delay: -2.6, dur: 3.6 },
  { x: 34, y: 66, size: 20, color: '#6366f1', delay: -0.3, dur: 4.1 },
  { x: 74, y: 52, size: 9, color: '#c084fc', delay: -1.5, dur: 2.9 },
  { x: 58, y: 80, size: 13, color: '#22d3ee', delay: -2.9, dur: 3.3 },
  { x: 14, y: 88, size: 7, color: '#e879f9', delay: -0.9, dur: 2.7 },
]

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
            <!-- gugusan bintang aesthetic -->
            <div
              class="pointer-events-none absolute -left-10 -top-10 h-40 w-40"
              aria-hidden="true"
            >
              <svg
                v-for="(s, i) in stars"
                :key="i"
                class="star-twinkle absolute"
                :style="{
                  '--c': s.color,
                  color: s.color,
                  left: s.x + '%',
                  top: s.y + '%',
                  width: s.size + 'px',
                  height: s.size + 'px',
                  animationDelay: s.delay + 's',
                  animationDuration: s.dur + 's',
                }"
                viewBox="0 0 24 24"
                :fill="s.color"
              >
                <path d="M12 0c.8 6.2 5.8 11.2 12 12-6.2.8-11.2 5.8-12 12-.8-6.2-5.8-11.2-12-12 6.2-.8 11.2-5.8 12-12Z" />
              </svg>
            </div>

            <!-- latar gradien miring (dibuat lebih lembut) -->
            <div
              class="absolute inset-0 rotate-6 rounded-t-full rounded-b-3xl bg-gradient-to-br from-primary-300 via-primary-400 to-primary-600 opacity-80 transition-transform duration-500 group-hover:rotate-3"
              aria-hidden="true"
            ></div>

            <!-- Halo blur yang "bernapas": cahaya menyebar keluar bingkai -->
            <div
              class="photo-halo pointer-events-none absolute -inset-[14px] overflow-hidden rounded-t-full rounded-b-[2.6rem]"
              aria-hidden="true"
            >
              <span class="photo-spin"></span>
            </div>

            <!-- Pelat putih: jeda bersih antara foto dan garis cahaya -->
            <div
              class="pointer-events-none absolute -inset-[9px] rounded-t-full rounded-b-[33px] bg-white shadow-[0_20px_50px_-15px_rgba(15,23,42,0.35)]"
              aria-hidden="true"
            ></div>

            <!-- Garis cahaya tipis dengan komet yang berputar -->
            <div class="photo-ring pointer-events-none absolute -inset-[9px] rounded-t-full rounded-b-[33px]" aria-hidden="true">
              <span class="photo-spin"></span>
            </div>

            <img
              src="/foto-profil.png"
              alt="Foto Rosida Dewi Utami"
              class="relative aspect-[4/5] w-full rounded-t-full rounded-b-3xl object-cover"
              loading="lazy"
            />

            <!-- percikan bintang -->
            <svg
              class="sparkle absolute -right-4 top-10 h-6 w-6 text-cyan-400 sm:-right-8"
              viewBox="0 0 24 24"
              fill="currentColor"
              aria-hidden="true"
            >
              <path d="M12 0c.8 6.2 5.8 11.2 12 12-6.2.8-11.2 5.8-12 12-.8-6.2-5.8-11.2-12-12 6.2-.8 11.2-5.8 12-12Z" />
            </svg>
            <svg
              class="sparkle sparkle-2 absolute -left-5 bottom-24 h-4 w-4 text-fuchsia-400 sm:-left-9"
              viewBox="0 0 24 24"
              fill="currentColor"
              aria-hidden="true"
            >
              <path d="M12 0c.8 6.2 5.8 11.2 12 12-6.2.8-11.2 5.8-12 12-.8-6.2-5.8-11.2-12-12 6.2-.8 11.2-5.8 12-12Z" />
            </svg>
            <svg
              class="sparkle sparkle-3 absolute right-6 -top-6 h-3.5 w-3.5 text-indigo-400"
              viewBox="0 0 24 24"
              fill="currentColor"
              aria-hidden="true"
            >
              <path d="M12 0c.8 6.2 5.8 11.2 12 12-6.2.8-11.2 5.8-12 12-.8-6.2-5.8-11.2-12-12 6.2-.8 11.2-5.8 12-12Z" />
            </svg>

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

/* ---------- Gugusan bintang kelap-kelip ---------- */
.star-twinkle {
  filter: drop-shadow(0 0 5px var(--c));
  animation: starTwinkle 3.4s ease-in-out infinite;
}
@keyframes starTwinkle {
  0%, 100% { opacity: 0.2; transform: scale(0.5) rotate(0deg); }
  50% { opacity: 1; transform: scale(1) rotate(90deg); }
}

/* ---------- Cahaya bingkai foto: komet cyan > indigo > fuchsia ---------- */
.photo-spin {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 230%; /* cukup besar untuk menutup bingkai 4:5 saat berputar */
  aspect-ratio: 1 / 1;
  background: conic-gradient(
    from 0deg,
    transparent 0%,
    transparent 52%,
    rgba(34, 211, 238, 0.9) 68%,
    rgba(99, 102, 241, 1) 84%,
    rgba(232, 121, 249, 1) 96%,
    transparent 100%
  );
  transform: translate(-50%, -50%) rotate(0deg);
  animation: photoSpin 7s linear infinite;
  will-change: transform;
}
@keyframes photoSpin {
  to {
    transform: translate(-50%, -50%) rotate(360deg);
  }
}

/* Garis tipis: hanya tepinya yang terlihat berkat mask */
.photo-ring {
  padding: 1.5px;
  overflow: hidden;
  background: rgba(129, 140, 248, 0.22); /* garis dasar yang redup */
  -webkit-mask: linear-gradient(#000 0 0) content-box, linear-gradient(#000 0 0);
  -webkit-mask-composite: xor;
  mask: linear-gradient(#000 0 0) content-box exclude, linear-gradient(#000 0 0);
}

/* Halo blur: bernapas pelan, lebih terang saat di-hover */
.photo-halo {
  filter: blur(22px);
  opacity: 0.45;
  animation: haloBreath 5s ease-in-out infinite;
  transition: opacity 0.4s ease;
}
.group:hover .photo-halo {
  opacity: 0.9;
  animation-play-state: paused;
}
@keyframes haloBreath {
  0%, 100% { opacity: 0.35; transform: scale(0.99); }
  50% { opacity: 0.65; transform: scale(1.02); }
}

/* Percikan bintang berkedip */
.sparkle {
  animation: twinkle 3.6s ease-in-out infinite;
  filter: drop-shadow(0 0 6px currentColor);
}
.sparkle-2 {
  animation-delay: -1.2s;
}
.sparkle-3 {
  animation-delay: -2.4s;
}
@keyframes twinkle {
  0%, 100% { opacity: 0.25; transform: scale(0.6) rotate(0deg); }
  50% { opacity: 1; transform: scale(1) rotate(45deg); }
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
  .marquee-track,
  .photo-spin,
  .photo-halo {
    animation: none;
  }
  .sparkle {
    animation: none;
    opacity: 0.8;
  }
  .star-twinkle {
    animation: none;
    opacity: 0.7;
  }
  .photo-halo {
    transition: none;
  }
}
</style>
