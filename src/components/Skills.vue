<script setup>
import { ref, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue'
import { skills } from './data/skills'

const RADIUS = 20
const CIRC = 2 * Math.PI * RADIUS

const sectionRef = ref(null)
const displayLevels = ref(skills.map(() => 0))
const hasAnimated = ref(false)
let observer = null
let timeouts = []
let rafIds = []

function clearPendingTimeouts() {
  timeouts.forEach((t) => clearTimeout(t))
  timeouts = []
  rafIds.forEach((id) => cancelAnimationFrame(id))
  rafIds = []
}

function resetLevels() {
  displayLevels.value = skills.map(() => 0)
  hasAnimated.value = false
}

function animateValue(index, target, duration = 1300) {
  const startTime = performance.now()

  function tick(now) {
    const progress = Math.min((now - startTime) / duration, 1)
    const eased = 1 - Math.pow(1 - progress, 3)
    displayLevels.value[index] = Math.round(eased * target)

    if (progress < 1) {
      rafIds.push(requestAnimationFrame(tick))
    } else {
      displayLevels.value[index] = target
    }
  }

  rafIds.push(requestAnimationFrame(tick))
}

function startAnimation() {
  if (hasAnimated.value) return
  hasAnimated.value = true

  skills.forEach((skill, i) => {
    const t = setTimeout(() => {
      animateValue(i, skill.level)
    }, i * 130)
    timeouts.push(t)
  })
}

function setupObserver() {
  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          startAnimation()
        } else {
          clearPendingTimeouts()
          resetLevels()
        }
      })
    },
    { threshold: 0.3 }
  )

  if (sectionRef.value) observer.observe(sectionRef.value)
}

function destroyObserver() {
  if (observer) {
    observer.disconnect()
    observer = null
  }
}

function dashOffset(level) {
  return CIRC - (level / 100) * CIRC
}

onMounted(() => {
  setupObserver()
})

onUnmounted(() => {
  clearPendingTimeouts()
  destroyObserver()
})

onActivated(() => {
  clearPendingTimeouts()
  resetLevels()
  setupObserver()
})

onDeactivated(() => {
  clearPendingTimeouts()
  destroyObserver()
})
</script>

<template>
  <section id="skills" class="relative py-20 px-6 bg-white overflow-hidden" ref="sectionRef">
    <!-- Dekorasi blob gradien lembut, konsisten dengan Projects.vue -->
    <div class="pointer-events-none absolute -top-24 -right-24 w-[26rem] h-[26rem] rounded-full bg-primary-100/60 blur-3xl -z-10"></div>
    <div class="pointer-events-none absolute -bottom-32 -left-20 w-[22rem] h-[22rem] rounded-full bg-blue-100/50 blur-3xl -z-10"></div>

    <div class="max-w-6xl mx-auto">
      <span class="inline-flex items-center gap-2 text-xs font-semibold tracking-wide text-primary-700 bg-primary-50 border border-primary-100 px-3 py-1 rounded-full mb-3">
        <span class="w-1.5 h-1.5 rounded-full bg-primary-500"></span>
        Kemampuan
      </span>

      <h2 class="section-title">Skill &amp; Teknologi</h2>
      <p class="section-subtitle">
        Tools dan bahasa pemrograman yang saya gunakan sehari-hari — dibaca langsung dari file konfigurasi saya.
      </p>

      <!-- Jendela editor kode, styling disamakan dengan card di Projects.vue -->
      <div class="mt-6 rounded-2xl border border-gray-100 shadow-sm overflow-hidden bg-white transition-all duration-500 ease-out hover:shadow-2xl">
        <!-- Titlebar -->
        <div class="flex items-center gap-4 px-4 py-3 bg-gray-50 border-b border-gray-100">
          <div class="flex gap-1.5">
            <span class="w-2.5 h-2.5 rounded-full bg-red-400"></span>
            <span class="w-2.5 h-2.5 rounded-full bg-amber-400"></span>
            <span class="w-2.5 h-2.5 rounded-full bg-green-400"></span>
          </div>

          <span class="inline-flex items-center gap-1.5 text-xs font-mono text-primary-700 bg-primary-50 px-2.5 py-1 rounded-md">
            <svg viewBox="0 0 24 24" class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="1.6">
              <path d="M8 4l-4 8 4 8m8-16l4 8-4 8" />
            </svg>
            skills.json
          </span>

          <span class="ml-auto text-xs font-mono text-gray-400">read-only</span>
        </div>

        <!-- Isi kode -->
        <div class="overflow-x-auto">
          <div class="py-5 min-w-[34rem]">
            <div class="flex items-center gap-4 px-5 py-1.5 font-mono text-sm text-gray-400">
              <span class="w-5 text-right select-none text-gray-300">1</span>
              <span><span class="text-primary-600">const</span> <span class="text-purple-600">skills</span> <span class="text-gray-400">= {</span></span>
            </div>

            <div
              v-for="(skill, index) in skills"
              :key="skill.key"
              class="group flex items-center justify-between gap-4 px-5 py-2 mx-1 rounded-lg transition-all duration-500 ease-out hover:bg-primary-50/50"
              :class="hasAnimated ? 'opacity-100 translate-x-0' : 'opacity-0 -translate-x-3'"
              :style="{ transitionDelay: `${index * 110}ms`, '--accent': skill.color }"
            >
              <span class="font-mono text-sm text-gray-600 whitespace-pre flex items-center gap-4">
                <span class="w-5 text-right select-none text-gray-300">{{ index + 2 }}</span>
                <span>
                  <span class="text-gray-400">&nbsp;&nbsp;</span><span class="text-primary-600">"{{ skill.key }}"</span><span class="text-gray-400">: {</span>
                  <span class="text-purple-600"> level</span><span class="text-gray-400">:</span>
                  <span class="text-amber-600 font-semibold"> {{ displayLevels[index] }}</span><span class="text-gray-400"> },</span>
                </span>
              </span>

              <span class="flex items-center gap-2.5 flex-shrink-0">
                <span class="relative w-9 h-9 flex-shrink-0">
                  <svg viewBox="0 0 52 52" class="w-full h-full ring-svg">
                    <circle class="fill-none stroke-gray-100" cx="26" cy="26" :r="RADIUS" stroke-width="4" />
                    <circle
                      class="fill-none ring-fill"
                      cx="26"
                      cy="26"
                      :r="RADIUS"
                      stroke-width="4"
                      stroke-linecap="round"
                      :stroke-dasharray="CIRC"
                      :stroke-dashoffset="dashOffset(displayLevels[index])"
                    />
                  </svg>
                  <svg viewBox="0 0 24 24" class="absolute inset-0 m-auto w-3.5 h-3.5 skill-icon" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round">
                    <path :d="skill.icon" />
                  </svg>
                </span>
                <span class="text-xs font-medium text-gray-500 whitespace-nowrap hidden sm:inline transition-colors duration-300 group-hover:text-gray-700">
                  {{ skill.name }}
                </span>
              </span>
            </div>

            <div class="flex items-center gap-4 px-5 py-1.5 font-mono text-sm text-gray-400">
              <span class="w-5 text-right select-none text-gray-300">{{ skills.length + 2 }}</span>
              <span class="text-gray-400">}<span class="caret"></span></span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
/* Hal-hal yang tidak bisa diwakili utility Tailwind: CSS var per-item, blink caret, rotasi ring */
.ring-svg {
  transform: rotate(-90deg);
}
.ring-fill {
  stroke: var(--accent);
  transition: stroke-dashoffset 0.3s ease-out;
  filter: drop-shadow(0 0 3px color-mix(in srgb, var(--accent) 40%, transparent));
}
.skill-icon {
  color: var(--accent);
}

.caret {
  display: inline-block;
  width: 0.5rem;
  height: 1rem;
  margin-left: 0.3rem;
  background: #059669;
  vertical-align: -0.15rem;
  animation: blink 1.1s steps(1) infinite;
}
@keyframes blink {
  0%, 49% { opacity: 1; }
  50%, 100% { opacity: 0; }
}

@media (prefers-reduced-motion: reduce) {
  .ring-fill {
    transition: none !important;
  }
  .caret {
    animation: none;
    opacity: 1;
  }
}
</style>