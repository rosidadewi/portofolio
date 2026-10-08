<script setup>
import { ref, watch, nextTick, onMounted, onUnmounted } from 'vue'

// Ganti dengan path fotomu, mis. file di folder public/ atau hasil import
const photo = '/foto-profil.png'

const isOpen = ref(false)
const isScrolled = ref(false)
const progress = ref(0)
const activeSection = ref('#hero')

// icon = path SVG (viewBox 20x20) yang dipakai di menu mobile
const menu = [
  { label: 'Beranda', href: '#hero', icon: 'M3 9.5 10 3l7 6.5V17a1 1 0 0 1-1 1h-3.5v-5h-5v5H4a1 1 0 0 1-1-1V9.5Z' },
  { label: 'Tentang', href: '#about', icon: 'M10 10a3.5 3.5 0 1 0 0-7 3.5 3.5 0 0 0 0 7ZM3.5 17a6.5 6.5 0 0 1 13 0' },
  { label: 'Skill', href: '#skills', icon: 'M11 2 4 11h5l-1 7 7-9h-5l1-7Z' },
  { label: 'Pengalaman', href: '#experiencework', icon: 'M3 7h14v9H3V7Zm4-3h6v3H7V4ZM3 11h14' },
  { label: 'Proyek', href: '#projects', icon: 'M2.5 5.5A1.5 1.5 0 0 1 4 4h4l2 2h6a1.5 1.5 0 0 1 1.5 1.5V15A1.5 1.5 0 0 1 16 16.5H4A1.5 1.5 0 0 1 2.5 15V5.5Z' },
  { label: 'Kontak', href: '#contact', icon: 'M3 5h14v10H3V5Zm0 1 7 5 7-5' },
]

/* ---------- Indikator aktif yang bergeser mengikuti menu ---------- */
const linkEls = {}
const indicator = ref({ x: 0, w: 0, visible: false, animated: false })

function setLinkRef(href, el) {
  if (el) linkEls[href] = el
}

function updateIndicator() {
  const el = linkEls[activeSection.value]
  if (!el) return
  const wasVisible = indicator.value.visible
  indicator.value = {
    x: el.offsetLeft,
    w: el.offsetWidth,
    visible: true,
    // placement pertama tanpa animasi supaya tidak meluncur dari 0
    animated: wasVisible,
  }
}

/* Cahaya sorot yang mengikuti kursor di dalam track menu */
function onTrackMove(e) {
  const el = e.currentTarget
  const r = el.getBoundingClientRect()
  el.style.setProperty('--mx', `${e.clientX - r.left}px`)
  el.style.setProperty('--my', `${e.clientY - r.top}px`)
}

/* ---------- Scroll spy ---------- */
let observer = null
let rafId = null

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
    { rootMargin: '-40% 0px -50% 0px', threshold: 0 }
  )

  sections.forEach((section) => observer.observe(section))
}

function updateScrollState() {
  const y = window.scrollY
  isScrolled.value = y > 16
  const max = document.documentElement.scrollHeight - window.innerHeight
  progress.value = max > 0 ? Math.min(1, y / max) : 0

  // Di dasar halaman, section terakhir bisa terlalu pendek untuk terdeteksi
  if (max > 0 && max - y < 4) {
    activeSection.value = menu[menu.length - 1].href
  }
}

function handleScroll() {
  if (rafId) cancelAnimationFrame(rafId)
  rafId = requestAnimationFrame(updateScrollState)
}

function closeMenu() {
  isOpen.value = false
}

function handleKeydown(e) {
  if (e.key === 'Escape') closeMenu()
}

/* Kunci scroll halaman saat menu mobile terbuka */
watch(isOpen, (open) => {
  document.body.style.overflow = open ? 'hidden' : ''
})

watch(activeSection, updateIndicator)

onMounted(() => {
  setupScrollSpy()
  updateScrollState()
  nextTick(updateIndicator)
  // font web yang terlambat dimuat mengubah lebar menu
  document.fonts?.ready.then(updateIndicator)

  window.addEventListener('scroll', handleScroll, { passive: true })
  window.addEventListener('resize', updateIndicator)
  window.addEventListener('keydown', handleKeydown)
})

onUnmounted(() => {
  if (observer) observer.disconnect()
  if (rafId) cancelAnimationFrame(rafId)
  document.body.style.overflow = ''
  window.removeEventListener('scroll', handleScroll)
  window.removeEventListener('resize', updateIndicator)
  window.removeEventListener('keydown', handleKeydown)
})
</script>

<template>
  <header class="font-ui pointer-events-none fixed inset-x-0 top-0 z-50 px-4 pt-3 md:pt-5">
    <!-- Garis progres baca -->
    <div
      class="progress fixed inset-x-0 top-0 h-0.5 origin-left bg-gradient-to-r from-cyan-400 via-indigo-500 to-fuchsia-500"
      :style="{ transform: `scaleX(${progress})` }"
      aria-hidden="true"
    ></div>

    <!-- Latar redup di belakang menu mobile; klik untuk menutup -->
    <transition name="fade">
      <div
        v-if="isOpen"
        class="pointer-events-auto fixed inset-0 -z-10 bg-slate-950/60 backdrop-blur-sm md:hidden"
        aria-hidden="true"
        @click="closeMenu"
      ></div>
    </transition>

    <!-- Cangkang navbar: melayang di tengah dan menyempit saat di-scroll -->
    <div
      class="shell relative mx-auto"
      :class="isScrolled ? 'max-w-4xl' : 'max-w-5xl'"
    >
      <!-- Cahaya aurora di bawah navbar -->
      <div
        class="aurora pointer-events-none absolute inset-x-10 -bottom-2 -z-10 h-7 rounded-full bg-gradient-to-r from-cyan-500/50 via-indigo-500/60 to-fuchsia-500/50 blur-2xl"
        aria-hidden="true"
      ></div>

      <!-- Halo cahaya yang melingkari tepi navbar -->
      <div
        class="halo pointer-events-none absolute -inset-[3px] -z-10 overflow-hidden rounded-full"
        aria-hidden="true"
      >
        <span class="halo-spin"></span>
      </div>

      <!-- Bingkai: border gradasi yang berputar pelan + glow berdenyut -->
      <div
        class="frame edge-glow pointer-events-auto relative overflow-hidden rounded-full bg-white/10 p-px"
        :class="isScrolled ? 'is-scrolled' : ''"
      >
        <span class="spin-border" aria-hidden="true"></span>

        <nav
          class="bar relative z-10 flex items-center justify-between gap-3 rounded-full pl-2.5 pr-2 backdrop-blur-xl md:grid md:grid-cols-[1fr_auto_1fr]"
          :class="
            isScrolled
              ? 'bg-[#0a1020]/95 py-1.5'
              : 'bg-[#0b1224]/90 py-2'
          "
          aria-label="Navigasi utama"
        >
          <!-- Brand (kiri) -->
          <a
            href="#hero"
            class="group flex items-center gap-3 justify-self-start rounded-full py-0.5 pr-3 focus:outline-none focus-visible:ring-2 focus-visible:ring-cyan-400"
            @click="closeMenu"
          >
            <!-- Foto dengan cincin gradasi -->
            <span
              class="logo-ring block rounded-full bg-gradient-to-tr from-cyan-400 via-indigo-500 to-fuchsia-500 p-[2px]"
            >
              <img
                :src="photo"
                alt="Foto profil Rosida"
                width="36"
                height="36"
                class="logo block h-9 w-9 rounded-full object-cover ring-2 ring-[#0b1224]"
              />
            </span>
            <span class="flex flex-col leading-none">
              <span class="brand-text text-[17px] font-extrabold tracking-tight">Portofolio</span>
              <span class="mt-1 hidden text-[10px] font-semibold uppercase tracking-[0.22em] text-cyan-300/80 sm:block">
                Rosida
              </span>
            </span>
          </a>

          <!-- Menu desktop (tengah): track dengan sorot kursor dan pil aktif -->
          <ul
            class="track relative hidden items-center justify-self-center rounded-full bg-white/[0.04] p-1 text-[13px] font-semibold ring-1 ring-inset ring-white/10 md:flex"
            @mousemove="onTrackMove"
          >
            <span
              class="indicator pointer-events-none absolute bottom-1 left-0 top-1 overflow-hidden rounded-full bg-gradient-to-r from-indigo-500 via-violet-500 to-fuchsia-500 shadow-[0_6px_20px_-4px_rgba(99,102,241,0.65),inset_0_1px_0_rgba(255,255,255,0.3)]"
              :class="{ 'is-animated': indicator.animated }"
              :style="{
                width: `${indicator.w}px`,
                transform: `translateX(${indicator.x}px)`,
                opacity: indicator.visible ? 1 : 0,
              }"
              aria-hidden="true"
            ></span>

            <li v-for="item in menu" :key="item.href">
              <a
                :ref="(el) => setLinkRef(item.href, el)"
                :href="item.href"
                class="link relative z-10 block rounded-full px-3 py-1.5 focus:outline-none focus-visible:ring-2 focus-visible:ring-cyan-400 lg:px-3.5"
                :class="
                  activeSection === item.href
                    ? 'text-white'
                    : 'text-slate-400 hover:text-white'
                "
                :aria-current="activeSection === item.href ? 'true' : undefined"
              >
                {{ item.label }}
              </a>
            </li>
          </ul>

          <!-- Penyeimbang kolom kanan di desktop agar menu tepat di tengah -->
          <div class="hidden md:block" aria-hidden="true"></div>

          <!-- Hamburger mobile -->
          <button
            type="button"
            class="flex h-10 w-10 flex-col items-center justify-center gap-1.5 rounded-full bg-white/[0.06] ring-1 ring-inset ring-white/10 transition hover:bg-white/10 focus:outline-none focus-visible:ring-2 focus-visible:ring-cyan-400 md:hidden"
            :aria-expanded="isOpen"
            aria-controls="mobile-menu"
            aria-label="Buka atau tutup menu"
            @click="isOpen = !isOpen"
          >
            <span
              class="h-0.5 w-5 origin-center rounded-full bg-white transition-all duration-300"
              :class="{ 'translate-y-2 rotate-45': isOpen }"
            ></span>
            <span
              class="h-0.5 w-3.5 self-end rounded-full bg-cyan-400 transition-all duration-300"
              :class="isOpen ? 'scale-0 opacity-0' : 'mr-[0.65rem]'"
            ></span>
            <span
              class="h-0.5 w-5 origin-center rounded-full bg-white transition-all duration-300"
              :class="{ '-translate-y-2 -rotate-45': isOpen }"
            ></span>
          </button>
        </nav>
      </div>

      <!-- Menu mobile: kartu melengkung dengan ikon -->
      <transition name="menu">
        <div
          v-if="isOpen"
          id="mobile-menu"
          class="pointer-events-auto absolute inset-x-0 top-full mt-3 origin-top rounded-[30px] bg-gradient-to-b from-white/25 via-white/5 to-indigo-400/25 p-px shadow-2xl shadow-indigo-950/60 md:hidden"
        >
          <div class="rounded-[29px] bg-[#0a1020]/95 p-2.5 backdrop-blur-xl">
            <ul class="flex flex-col gap-1">
              <li
                v-for="(item, index) in menu"
                :key="item.href"
                class="menu-item"
                :style="{ animationDelay: `${index * 40}ms` }"
              >
                <a
                  :href="item.href"
                  class="flex items-center gap-3 rounded-[20px] px-3 py-2.5 text-[15px] font-semibold transition-colors focus:outline-none focus-visible:ring-2 focus-visible:ring-cyan-400"
                  :class="
                    activeSection === item.href
                      ? 'bg-gradient-to-r from-indigo-500 via-violet-500 to-fuchsia-500 text-white shadow-lg shadow-indigo-500/30'
                      : 'text-slate-300 hover:bg-white/[0.06] hover:text-white'
                  "
                  :aria-current="activeSection === item.href ? 'true' : undefined"
                  @click="closeMenu"
                >
                  <!-- Kotak ikon -->
                  <span
                    class="flex h-9 w-9 items-center justify-center rounded-xl transition-colors"
                    :class="
                      activeSection === item.href
                        ? 'bg-white/20 text-white'
                        : 'bg-white/[0.06] text-slate-400'
                    "
                  >
                    <svg
                      class="h-[18px] w-[18px]"
                      viewBox="0 0 20 20"
                      fill="none"
                      stroke="currentColor"
                      stroke-width="1.7"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      aria-hidden="true"
                    >
                      <path :d="item.icon" />
                    </svg>
                  </span>
                  <span class="flex-1">{{ item.label }}</span>
                  <svg
                    class="h-4 w-4 transition-all duration-300"
                    :class="activeSection === item.href ? 'translate-x-0 opacity-100' : '-translate-x-1 opacity-0'"
                    viewBox="0 0 20 20"
                    fill="none"
                    stroke="currentColor"
                    stroke-width="2"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    aria-hidden="true"
                  >
                    <path d="M4 10h12M11 5l5 5-5 5" />
                  </svg>
                </a>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
  </header>
</template>

<style scoped>
/* Font: pastikan link Google Fonts sudah dipasang di index.html */
.font-ui {
  font-family: 'Plus Jakarta Sans', ui-sans-serif, system-ui, -apple-system, 'Segoe UI', sans-serif;
}

/* Brand dengan gradasi teks putih ke biru muda */
.brand-text {
  background: linear-gradient(90deg, #ffffff 0%, #c7d2fe 100%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

/* Navbar menyempit halus saat di-scroll */
.shell {
  transition: max-width 0.45s cubic-bezier(0.3, 0.7, 0.2, 1);
}
.frame {
  transition: box-shadow 0.3s ease;
}
.bar {
  color-scheme: dark;
  transition: background-color 0.3s ease, padding 0.3s ease;
}
.link {
  transition: color 0.2s ease;
}

/* Border gradasi berputar */
.spin-border {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 100%;
  aspect-ratio: 1 / 1;
  background: conic-gradient(
    from 0deg,
    transparent 0%,
    transparent 62%,
    rgba(34, 211, 238, 0.95) 76%,
    rgba(129, 140, 248, 0.95) 88%,
    rgba(232, 121, 249, 0.95) 96%,
    transparent 100%
  );
  transform: translate(-50%, -50%) rotate(0deg);
  animation: spinBorder 7s linear infinite;
  will-change: transform;
}
@keyframes spinBorder {
  to {
    transform: translate(-50%, -50%) rotate(360deg);
  }
}

/* Halo: gradasi berputar yang di-blur sehingga cahayanya menyebar keluar tepi */
.halo {
  filter: blur(9px);
  opacity: 0.75;
}
.halo-spin {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 100%;
  aspect-ratio: 1 / 1;
  background: conic-gradient(
    from 0deg,
    rgba(34, 211, 238, 0.9),
    rgba(99, 102, 241, 0.9),
    rgba(232, 121, 249, 0.9),
    rgba(99, 102, 241, 0.9),
    rgba(34, 211, 238, 0.9)
  );
  transform: translate(-50%, -50%) rotate(0deg);
  animation: spinBorder 9s linear infinite;
  will-change: transform;
}

/* Glow tipis yang mengikuti bentuk navbar dan berdenyut pelan */
.edge-glow {
  box-shadow:
    0 0 0 1px rgba(165, 180, 252, 0.18),
    0 0 14px rgba(99, 102, 241, 0.45),
    0 0 36px rgba(34, 211, 238, 0.18);
  animation: edgePulse 4.5s ease-in-out infinite;
}
.edge-glow.is-scrolled {
  box-shadow:
    0 0 0 1px rgba(165, 180, 252, 0.28),
    0 0 18px rgba(99, 102, 241, 0.6),
    0 0 44px rgba(232, 121, 249, 0.2);
}
@keyframes edgePulse {
  0%,
  100% {
    filter: brightness(0.9);
  }
  50% {
    filter: brightness(1.2);
  }
}

/* Cahaya aurora bernapas pelan */
.aurora {
  animation: auroraPulse 5s ease-in-out infinite;
}
@keyframes auroraPulse {
  0%,
  100% {
    opacity: 0.45;
    transform: scaleX(0.92);
  }
  50% {
    opacity: 0.85;
    transform: scaleX(1);
  }
}

/* Foto profil */
.logo-ring {
  transition: transform 0.35s cubic-bezier(0.3, 0.7, 0.2, 1);
}
.group:hover .logo-ring {
  transform: rotate(-8deg) scale(1.06);
}

/* Sorot cahaya mengikuti kursor di track menu */
.track::before {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  background: radial-gradient(
    90px circle at var(--mx, 50%) var(--my, 50%),
    rgba(165, 180, 252, 0.2),
    transparent 70%
  );
  opacity: 0;
  transition: opacity 0.25s ease;
  pointer-events: none;
}
.track:hover::before {
  opacity: 1;
}

/* Progres mengikuti scroll, jadi tanpa transisi agar tidak tertinggal */
.progress {
  will-change: transform;
}

/* Indikator aktif + kilau yang menyapu */
.indicator {
  will-change: transform, width;
}
.indicator.is-animated {
  transition: transform 0.4s cubic-bezier(0.3, 0.7, 0.2, 1),
    width 0.4s cubic-bezier(0.3, 0.7, 0.2, 1), opacity 0.2s ease;
}
.indicator::after {
  content: '';
  position: absolute;
  inset: 0;
  width: 50%;
  background: linear-gradient(100deg, transparent, rgba(255, 255, 255, 0.35), transparent);
  transform: translateX(-150%) skewX(-18deg);
  animation: shimmer 3.4s ease-in-out infinite;
}
@keyframes shimmer {
  0%,
  55% {
    transform: translateX(-150%) skewX(-18deg);
  }
  100% {
    transform: translateX(320%) skewX(-18deg);
  }
}

/* Latar redup */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.25s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Kartu menu mobile */
.menu-enter-active,
.menu-leave-active {
  transition: opacity 0.25s ease, transform 0.25s ease;
}
.menu-enter-from,
.menu-leave-to {
  opacity: 0;
  transform: translateY(-8px) scale(0.97);
}

/* Item muncul satu-satu */
.menu-item {
  opacity: 0;
  transform: translateX(-10px);
  animation: menuItemIn 0.3s ease forwards;
}
@keyframes menuItemIn {
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@media (prefers-reduced-motion: reduce) {
  .shell,
  .frame,
  .bar,
  .link,
  .logo-ring,
  .track::before,
  .indicator.is-animated,
  .fade-enter-active,
  .fade-leave-active,
  .menu-enter-active,
  .menu-leave-active {
    transition: none;
  }
  .spin-border,
  .aurora,
  .halo-spin,
  .edge-glow,
  .indicator::after {
    animation: none;
  }
  .group:hover .logo-ring {
    transform: none;
  }
  .menu-item {
    animation: none;
    opacity: 1;
    transform: none;
  }
}
</style>
