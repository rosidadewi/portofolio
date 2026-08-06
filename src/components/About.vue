<script setup>
import { ref, computed, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue'
import { projects } from './data/projects'
import { skills } from './data/skills'

// Dihitung otomatis dari data project (sumber tunggal: src/data/projects.js)
const totalProjects = computed(() => projects.length)

// Dihitung otomatis dari data skill (sumber tunggal: src/data/skills.js)
// supaya angka "Teknologi Dikuasai" selalu sinkron dengan jumlah kartu di Skills.vue
const totalTechnologies = computed(() => skills.length)

const stats = computed(() => [
  { label: 'Proyek Selesai', value: totalProjects.value, suffix: '+' },
  { label: 'Tahun Belajar', value: 3, suffix: '+' },
  { label: 'Teknologi Dikuasai', value: totalTechnologies.value, suffix: '+' },
])

const displayValues = ref(stats.value.map(() => 0))
const sectionRef = ref(null)
let observer = null

function resetValues() {
  displayValues.value = stats.value.map(() => 0)
}

function animateCount(index, target, duration = 1500) {
  const start = 0
  const startTime = performance.now()

  function tick(now) {
    const progress = Math.min((now - startTime) / duration, 1)
    const eased = 1 - Math.pow(1 - progress, 3)
    displayValues.value[index] = Math.floor(start + (target - start) * eased)

    if (progress < 1) {
      requestAnimationFrame(tick)
    } else {
      displayValues.value[index] = target
    }
  }

  requestAnimationFrame(tick)
}

function startAnimation() {
  resetValues()
  stats.value.forEach((stat, i) => animateCount(i, stat.value))
}

function setupObserver() {
  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          startAnimation()
        } else {
          // reset supaya siap animasi ulang saat masuk viewport lagi
          resetValues()
        }
      })
    },
    { threshold: 0.3 }
  )

  if (sectionRef.value) {
    observer.observe(sectionRef.value)
  }
}

function destroyObserver() {
  if (observer) {
    observer.disconnect()
    observer = null
  }
}

onMounted(() => {
  setupObserver()
})

onUnmounted(() => {
  destroyObserver()
})

// dipanggil setiap kali komponen kembali aktif (misalnya habis swipe balik ke page ini)
onActivated(() => {
  resetValues()
  setupObserver()
})

// dipanggil setiap kali komponen di-nonaktifkan (misalnya di-swipe ke page lain)
onDeactivated(() => {
  destroyObserver()
})
</script>

<template>
  <section id="about" class="py-20 px-6 bg-white" ref="sectionRef">
    <div class="max-w-6xl mx-auto">
      <h2 class="section-title">Tentang Saya</h2>
      <p class="section-subtitle">
        Kenali lebih dekat siapa saya dan apa yang saya kerjakan
      </p>

      <div class="grid md:grid-cols-2 gap-12 items-center">
        <div class="rounded-2xl aspect-square overflow-hidden">
          <img
            src="/foto-profil.png"
            alt="Foto Rosida Dewi Utami"
            class="w-full h-full object-cover"
          />
        </div>

        <div>
          <p class="text-gray-600 mb-4 leading-relaxed">
            Saya adalah seorang Full-Stack Developer yang berfokus pada pembuatan aplikasi web 
            yang bersih, modern, dan mudah digunakan, mulai dari sisi antarmuka pengguna (Front-End)
            hingga logika dan arsitektur di sisi server (Back-End). Saya senang mempelajari framework
            dan teknologi baru serta menerapkan praktik terbaik dalam pengembangan web secara
            menyeluruh.
          </p>

          <div class="grid grid-cols-3 gap-4">
            <div
              v-for="(stat, index) in stats"
              :key="stat.label"
              class="text-center"
            >
              <p class="text-2xl md:text-3xl font-bold text-primary-600">
                {{ displayValues[index] }}{{ stat.suffix }}
              </p>
              <p class="text-sm text-gray-500">{{ stat.label }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
