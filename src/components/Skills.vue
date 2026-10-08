<script setup>
import { ref, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue'
import { skills } from './data/skills'

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
  <section id="skills" class="relative py-24 px-6 bg-white overflow-hidden" ref="sectionRef">
    <div class="pointer-events-none absolute inset-0 -z-10 aurora"></div>

    <div class="max-w-5xl mx-auto">

      <h2 class="section-title">Skill &amp; Teknologi</h2>
      <p class="section-subtitle max-w-2xl">
        Tools dan bahasa pemrograman yang saya gunakan sehari-hari — dibaca langsung dari file konfigurasi saya.
      </p>

      <div class="relative mt-12">
        <div class="pointer-events-none absolute -inset-x-6 -inset-y-8 -z-10 window-glow"></div>

        <div class="window rounded-3xl overflow-hidden ring-1 ring-white/10 shadow-2xl shadow-slate-900/40">
          <!-- Titlebar -->
          <div class="relative flex items-center gap-4 px-5 h-12 border-b border-white/[0.06] bg-white/[0.03]">
            <div class="flex gap-2">
              <span class="w-3 h-3 rounded-full bg-[#ff5f57]"></span>
              <span class="w-3 h-3 rounded-full bg-[#febc2e]"></span>
              <span class="w-3 h-3 rounded-full bg-[#28c840]"></span>
            </div>

            <span class="absolute inset-x-0 mx-auto w-fit inline-flex items-center gap-2 text-xs font-mono text-slate-400">
              <svg viewBox="0 0 24 24" class="w-3.5 h-3.5 text-primary-400" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round">
                <path d="M8 4l-4 8 4 8m8-16l4 8-4 8" />
              </svg>
              skills.json
            </span>

            <span class="ml-auto text-[11px] font-mono text-slate-500 border border-white/10 rounded-full px-2.5 py-0.5">read-only</span>
          </div>

          <!-- Isi kode: gutter + kode + panel visual -->
          <div class="overflow-x-auto">
            <div class="relative min-w-[38rem] py-3">
              <!-- Gutter nomor baris -->
              <div class="pointer-events-none absolute inset-y-0 left-0 w-12 bg-white/[0.025] border-r border-white/[0.06]"></div>

              <div class="line grid grid-cols-[3rem_1fr_16rem] items-center font-mono text-sm text-slate-500 h-9">
                <span class="text-right pr-4 select-none text-slate-700 relative">1</span>
                <span class="pl-5"><span class="text-sky-400">const</span> <span class="text-violet-400">skills</span> = {</span>
                <span class="h-full border-l border-white/[0.06]"></span>
              </div>

              <div
                v-for="(skill, index) in skills"
                :key="skill.key"
                class="line skill-row group grid grid-cols-[3rem_1fr_16rem] items-center transition-all duration-500 ease-out hover:bg-white/[0.04]"
                :class="hasAnimated ? 'opacity-100 translate-x-0' : 'opacity-0 -translate-x-3'"
                :style="{ transitionDelay: `${index * 110}ms`, '--accent': skill.color }"
              >
                <span class="text-right pr-4 font-mono text-sm select-none text-slate-700 transition-colors group-hover:text-slate-300 relative">
                  {{ index + 2 }}
                </span>

                <span class="pl-5 py-3.5 font-mono text-sm text-slate-300 whitespace-pre">
                  <span class="text-slate-600">&nbsp;&nbsp;</span><span class="text-emerald-300">"{{ skill.key }}"</span><span class="text-slate-500">: {</span><span class="text-violet-400"> level</span><span class="text-slate-500">:</span><span class="text-amber-300 font-semibold tabular-nums"> {{ displayLevels[index] }}</span><span class="text-slate-500"> },</span>
                </span>

                <!-- Panel visual -->
                <span class="flex items-center gap-3 self-stretch pl-5 pr-6 border-l border-white/[0.06]">
                  <span class="icon-tile grid place-items-center w-9 h-9 rounded-xl flex-shrink-0">
                    <svg viewBox="0 0 24 24" class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round" stroke-linejoin="round">
                      <path :d="skill.icon" />
                    </svg>
                  </span>
                  <span class="flex-1 min-w-0">
                    <span class="block text-sm font-medium leading-none mb-2 text-slate-300 truncate transition-colors duration-300 group-hover:text-white">
                      {{ skill.name }}
                    </span>
                    <span class="block h-1.5 rounded-full bg-white/10 overflow-hidden">
                      <span class="bar-fill block h-full rounded-full" :style="{ width: displayLevels[index] + '%' }"></span>
                    </span>
                  </span>
                </span>
              </div>

              <div class="line grid grid-cols-[3rem_1fr_16rem] items-center font-mono text-sm text-slate-500 h-9">
                <span class="text-right pr-4 select-none text-slate-700 relative">{{ skills.length + 2 }}</span>
                <span class="pl-5">}<span class="caret"></span></span>
                <span class="h-full border-l border-white/[0.06]"></span>
              </div>
            </div>
          </div>

          <!-- Status bar -->
          <div class="flex items-center gap-4 px-5 h-8 border-t border-white/[0.06] bg-white/[0.03] text-[11px] font-mono text-slate-500">
            <span class="inline-flex items-center gap-1.5">
              <span class="w-1.5 h-1.5 rounded-full bg-emerald-400"></span>
              {{ skills.length }} skills
            </span>
            <span class="ml-auto">JSON</span>
            <span class="hidden sm:inline">UTF-8</span>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.aurora {
  background:
    radial-gradient(40rem 24rem at 90% -5%, rgb(16 185 129 / 0.14), transparent 60%),
    radial-gradient(36rem 22rem at -5% 105%, rgb(99 102 241 / 0.12), transparent 60%),
    radial-gradient(28rem 18rem at 50% 50%, rgb(56 189 248 / 0.06), transparent 70%);
}

.window-glow {
  background: radial-gradient(60% 55% at 50% 50%, rgb(16 185 129 / 0.26), rgb(99 102 241 / 0.16) 60%, transparent 80%);
  filter: blur(48px);
  opacity: 0.8;
}

.window {
  background:
    linear-gradient(180deg, rgb(255 255 255 / 0.04), transparent 120px),
    #0b1020;
}

.skill-row {
  position: relative;
}
.skill-row::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 2px;
  background: var(--accent);
  opacity: 0;
  transition: opacity 0.3s ease;
}
.skill-row:hover::before {
  opacity: 1;
}

.bar-fill {
  background: linear-gradient(90deg, color-mix(in srgb, var(--accent) 55%, transparent), var(--accent));
  box-shadow: 0 0 10px color-mix(in srgb, var(--accent) 55%, transparent);
  transition: width 0.3s ease-out;
}

.icon-tile {
  color: var(--accent);
  background: color-mix(in srgb, var(--accent) 14%, transparent);
  box-shadow: inset 0 0 0 1px color-mix(in srgb, var(--accent) 28%, transparent);
}

.caret {
  display: inline-block;
  width: 0.5rem;
  height: 1rem;
  margin-left: 0.3rem;
  background: #34d399;
  vertical-align: -0.15rem;
  animation: blink 1.1s steps(1) infinite;
}
@keyframes blink {
  0%, 49% { opacity: 1; }
  50%, 100% { opacity: 0; }
}

@media (prefers-reduced-motion: reduce) {
  .bar-fill,
  .skill-row::before {
    transition: none !important;
  }
  .caret {
    animation: none;
    opacity: 1;
  }
}
</style>
