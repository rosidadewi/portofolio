<script setup>
import { reactive, ref } from 'vue'

const form = reactive({
  name: '',
  email: '',
  message: '',
  botcheck: '', // honeypot field — harus tetap kosong, bot biasanya mengisinya
})

const isSubmitted = ref(false)
const isSending = ref(false)
const errorMessage = ref('')
const copied = ref(false)
const MAX_MESSAGE = 500

function resetForm() {
  form.name = ''
  form.email = ''
  form.message = ''
}

function showSuccess() {
  isSubmitted.value = true
  resetForm()
  setTimeout(() => {
    isSubmitted.value = false
  }, 5000)
}

async function handleSubmit() {
  // Kalau honeypot terisi, kemungkinan besar ini bot.
  // Diamkan saja seolah-olah sukses, tanpa benar-benar mengirim request.
  if (form.botcheck) {
    showSuccess()
    return
  }

  isSending.value = true
  errorMessage.value = ''

  try {
    const res = await fetch('https://api.web3forms.com/submit', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        access_key: '5ef675f0-2dd7-45fe-b72d-64f81a9fade9',
        name: form.name,
        email: form.email,
        message: form.message,
        botcheck: form.botcheck,
      }),
    })

    const data = await res.json()

    if (data.success) {
      showSuccess()
    } else {
      errorMessage.value = 'Gagal mengirim pesan. Coba lagi ya.'
    }
  } catch (err) {
    errorMessage.value = 'Terjadi kesalahan jaringan. Coba lagi.'
  } finally {
    isSending.value = false
  }
}

/* Ganti dengan informasi kontak asli kamu */
const email = 'rosidadewiutami30@gmail.com'
const location = 'Surakarta, Jawa Tengah'

async function copyEmail() {
  try {
    await navigator.clipboard.writeText(email)
    copied.value = true
    setTimeout(() => (copied.value = false), 2000)
  } catch {
    // clipboard tidak tersedia: buka aplikasi email sebagai gantinya
    window.location.href = `mailto:${email}`
  }
}

const socialLinks = [
  { icon: 'github', label: 'GitHub', href: 'https://github.com/rosidadewi' },
  { icon: 'linkedin', label: 'LinkedIn', href: 'https://www.linkedin.com/in/rosida-dewi-utami-397514290/' },
  { icon: 'instagram', label: 'Instagram', href: 'https://www.instagram.com/rosidadeu_?igsh=MTdnYXdnYzRjejBtaQ==' },
  { icon: 'whatsapp', label: 'WhatsApp', href: 'https://wa.me/6282134657795?text=Halo%2C%20saya%20ingin%20bertanya' },
]
</script>

<template>
  <section id="contact" class="relative overflow-hidden bg-gray-50 px-6 py-24 md:py-32">
    <!-- latar: grid halus + dua cahaya aksen -->
    <div class="bg-grid pointer-events-none absolute inset-0 text-gray-900/[0.06]" aria-hidden="true"></div>
    <div class="pointer-events-none absolute -left-24 top-10 h-72 w-72 rounded-full bg-primary-200/50 blur-3xl" aria-hidden="true"></div>
    <div class="pointer-events-none absolute -right-24 bottom-0 h-80 w-80 rounded-full bg-primary-100/70 blur-3xl" aria-hidden="true"></div>

    <div class="relative mx-auto max-w-6xl">
      <div class="max-w-2xl">
        <h2 class="text-4xl font-extrabold tracking-tight text-gray-900 md:text-6xl">Hubungi Saya</h2>
        <p class="mt-4 text-base leading-relaxed text-gray-600 md:text-lg">
          Punya proyek atau pertanyaan? Kirim pesan lewat form di bawah ini, saya senang mendengarnya.
        </p>
      </div>

      <!-- Satu kartu besar, dua sisi -->
      <div class="mt-14 grid overflow-hidden rounded-[2rem] bg-white shadow-2xl shadow-primary-900/10 ring-1 ring-gray-200 lg:grid-cols-5">
        <!-- Sisi kiri: info kontak -->
        <aside class="panel relative flex flex-col p-8 text-white md:p-10 lg:col-span-2">
          <div class="relative">
            <span class="inline-flex items-center gap-2 rounded-full bg-white/10 px-3 py-1.5 text-sm font-medium ring-1 ring-inset ring-white/20 backdrop-blur">
              <span class="relative flex h-2.5 w-2.5">
                <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-emerald-300 opacity-75"></span>
                <span class="relative inline-flex h-2.5 w-2.5 rounded-full bg-emerald-300"></span>
              </span>
              Terbuka untuk kolaborasi
            </span>

            <p class="mt-10 text-sm text-white/70">Email</p>
            <button
              type="button"
              class="group mt-1.5 flex w-full items-center justify-between gap-3 rounded-2xl bg-white/10 px-4 py-3.5 text-left ring-1 ring-inset ring-white/20 backdrop-blur transition hover:bg-white/20 focus:outline-none focus-visible:ring-2 focus-visible:ring-white"
              :aria-label="copied ? 'Email tersalin' : 'Salin alamat email'"
              @click="copyEmail"
            >
              <span class="break-all text-sm font-semibold sm:text-base">{{ email }}</span>
              <span class="flex shrink-0 items-center gap-1.5 text-xs font-medium text-white/80" aria-live="polite">
                <svg v-if="!copied" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="9" y="9" width="13" height="13" rx="2" />
                  <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1" />
                </svg>
                <svg v-else viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <polyline points="20 6 9 17 4 12" />
                </svg>
                {{ copied ? 'Tersalin' : 'Salin' }}
              </span>
            </button>

            <p class="mt-6 flex items-center gap-2 text-sm text-white/80">
              <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                <path d="M20 10c0 6-8 12-8 12s-8-6-8-12a8 8 0 0 1 16 0Z" />
                <circle cx="12" cy="10" r="3" />
              </svg>
              {{ location }}
            </p>
          </div>

          <div class="relative mt-12 lg:mt-auto lg:pt-12">
            <p class="mb-3 text-sm text-white/70">Atau temui saya di</p>
            <div class="grid grid-cols-2 gap-2">
              <a
                v-for="social in socialLinks"
                :key="social.label"
                :href="social.href"
                target="_blank"
                rel="noopener noreferrer"
                class="flex items-center gap-2.5 rounded-xl bg-white/10 px-3 py-2.5 text-sm font-medium ring-1 ring-inset ring-white/15 backdrop-blur transition hover:bg-white hover:text-gray-900 focus:outline-none focus-visible:ring-2 focus-visible:ring-white"
              >
                <svg v-if="social.icon === 'github'" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <path d="M15 22v-3.87a3.37 3.37 0 0 0-.94-2.61c3.14-.35 6.44-1.54 6.44-7A5.44 5.44 0 0 0 19 4.77 5.07 5.07 0 0 0 18.91.65S17.73.35 15 2.48a13.38 13.38 0 0 0-7 0C5.27.35 4.09.65 4.09.65A5.07 5.07 0 0 0 4 4.77a5.44 5.44 0 0 0-1.5 3.75c0 5.42 3.3 6.61 6.44 7A3.37 3.37 0 0 0 8 18.13V22" />
                </svg>
                <svg v-else-if="social.icon === 'linkedin'" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <path d="M16 8a6 6 0 0 1 6 6v7h-4v-7a2 2 0 0 0-2-2 2 2 0 0 0-2 2v7h-4v-7a6 6 0 0 1 6-6Z" />
                  <rect x="2" y="9" width="4" height="12" />
                  <circle cx="4" cy="4" r="2" />
                </svg>
                <svg v-else-if="social.icon === 'instagram'" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="2" y="2" width="20" height="20" rx="5" />
                  <path d="M16 11.37A4 4 0 1 1 12.63 8 4 4 0 0 1 16 11.37Z" />
                  <line x1="17.5" y1="6.5" x2="17.51" y2="6.5" />
                </svg>
                <svg v-else viewBox="0 0 24 24" width="16" height="16" fill="currentColor" aria-hidden="true">
                  <path d="M17.6 6.32A8.86 8.86 0 0 0 12.05 4C7.2 4 3.26 7.9 3.26 12.7c0 1.6.42 3.1 1.15 4.4L3 21l4.02-1.32a8.9 8.9 0 0 0 4.99 1.5h.01c4.85 0 8.79-3.9 8.79-8.7a8.6 8.6 0 0 0-2.61-6.16Zm-5.55 13.4h-.01a7.4 7.4 0 0 1-3.77-1.03l-.27-.16-2.8.92.94-2.72-.18-.28a7.24 7.24 0 0 1-1.12-3.85c0-4 3.28-7.26 7.31-7.26 1.95 0 3.79.76 5.17 2.13a7.19 7.19 0 0 1 2.14 5.13c0 4-3.28 7.26-7.31 7.26Zm4.01-5.44c-.22-.11-1.3-.64-1.5-.71-.2-.07-.35-.11-.5.11-.15.22-.57.71-.7.86-.13.14-.26.16-.48.05-.22-.11-.93-.34-1.77-1.1-.65-.58-1.1-1.3-1.22-1.52-.13-.22-.01-.34.1-.45.1-.1.22-.26.33-.39.11-.13.15-.22.22-.37.07-.15.04-.28-.02-.39-.06-.11-.5-1.2-.68-1.65-.18-.43-.36-.37-.5-.38h-.43c-.15 0-.39.06-.59.28-.2.22-.78.76-.78 1.85s.8 2.15.91 2.3c.11.15 1.57 2.4 3.81 3.36.53.23.95.37 1.27.47.53.17 1.02.15 1.4.09.43-.06 1.3-.53 1.48-1.04.18-.51.18-.94.13-1.03-.05-.09-.2-.15-.42-.26Z" />
                </svg>
                {{ social.label }}
              </a>
            </div>
            <p class="mt-6 text-sm leading-relaxed text-white/70">
              Biasanya saya membalas pesan dalam 1–2 hari kerja.
            </p>
          </div>
        </aside>

        <!-- Sisi kanan: form -->
        <form
          @submit.prevent="handleSubmit"
          class="relative space-y-5 p-8 md:p-10 lg:col-span-3"
          :aria-busy="isSending"
        >
          <!-- Honeypot field: tersembunyi dari manusia, jebakan untuk bot -->
          <div class="hidden" aria-hidden="true">
            <label for="botcheck">Jangan isi kolom ini</label>
            <input
              id="botcheck"
              v-model="form.botcheck"
              type="text"
              name="botcheck"
              tabindex="-1"
              autocomplete="off"
            />
          </div>

          <div class="grid gap-5 sm:grid-cols-2">
            <div class="float">
              <input
                id="name"
                v-model="form.name"
                type="text"
                required
                autocomplete="name"
                placeholder=" "
              />
              <label for="name">Nama</label>
            </div>
            <div class="float">
              <input
                id="email"
                v-model="form.email"
                type="email"
                required
                autocomplete="email"
                placeholder=" "
              />
              <label for="email">Email</label>
            </div>
          </div>

          <div class="float">
            <textarea
              id="message"
              v-model="form.message"
              required
              rows="7"
              :maxlength="MAX_MESSAGE"
              placeholder=" "
              class="resize-none"
            ></textarea>
            <label for="message">Pesan</label>
            <span class="absolute bottom-3 right-4 text-xs tabular-nums text-gray-400" aria-hidden="true">
              {{ form.message.length }}/{{ MAX_MESSAGE }}
            </span>
          </div>

          <button
            type="submit"
            :disabled="isSending"
            class="send-btn flex w-full items-center justify-center gap-2 rounded-2xl px-5 py-4 text-sm font-semibold text-white transition focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-500 focus-visible:ring-offset-2 active:scale-[0.98] disabled:cursor-not-allowed disabled:opacity-70"
          >
            <svg v-if="isSending" class="animate-spin" viewBox="0 0 24 24" width="16" height="16" fill="none" aria-hidden="true">
              <circle cx="12" cy="12" r="9" stroke="currentColor" stroke-width="3" stroke-opacity="0.25" />
              <path d="M21 12a9 9 0 0 0-9-9" stroke="currentColor" stroke-width="3" stroke-linecap="round" />
            </svg>
            <span>{{ isSending ? 'Mengirim...' : 'Kirim Pesan' }}</span>
            <svg v-if="!isSending" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
              <line x1="22" y1="2" x2="11" y2="13" />
              <polygon points="22 2 15 22 11 13 2 9 22 2" />
            </svg>
          </button>

          <!-- Status error -->
          <transition name="pop">
            <div
              v-if="errorMessage"
              role="alert"
              class="flex items-center gap-3 rounded-xl bg-red-50 px-4 py-3 text-sm text-red-700 ring-1 ring-inset ring-red-200"
            >
              <span class="flex h-7 w-7 shrink-0 items-center justify-center rounded-full bg-red-500 text-white">
                <svg viewBox="0 0 24 24" width="14" height="14" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <line x1="18" y1="6" x2="6" y2="18" />
                  <line x1="6" y1="6" x2="18" y2="18" />
                </svg>
              </span>
              {{ errorMessage }}
            </div>
          </transition>

          <!-- Status sukses: menutupi form dengan centang beranimasi -->
          <transition name="pop">
            <div
              v-if="isSubmitted"
              role="status"
              class="absolute inset-0 z-10 flex flex-col items-center justify-center gap-4 bg-white/95 p-8 text-center backdrop-blur-sm"
            >
              <span class="flex h-16 w-16 items-center justify-center rounded-full bg-emerald-500 text-white shadow-lg shadow-emerald-500/30">
                <svg viewBox="0 0 24 24" width="30" height="30" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <polyline class="check" points="20 6 9 17 4 12" />
                </svg>
              </span>
              <div>
                <p class="text-xl font-bold text-gray-900">Pesan berhasil terkirim!</p>
                <p class="mt-1 text-sm text-gray-600">Terima kasih sudah menghubungi saya.</p>
              </div>
              <button
                type="button"
                class="rounded-xl px-4 py-2 text-sm font-semibold text-primary-700 transition hover:bg-primary-50 focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-500"
                @click="isSubmitted = false"
              >
                Kirim pesan lain
              </button>
            </div>
          </transition>
        </form>
      </div>
    </div>
  </section>
</template>

<style scoped>
/* Grid halus, memudar ke arah tepi */
.bg-grid {
  background-image:
    linear-gradient(to right, currentColor 1px, transparent 1px),
    linear-gradient(to bottom, currentColor 1px, transparent 1px);
  background-size: 48px 48px;
  -webkit-mask-image: radial-gradient(ellipse at 50% 30%, #000 20%, transparent 75%);
  mask-image: radial-gradient(ellipse at 50% 30%, #000 20%, transparent 75%);
}

/* Panel kiri: gradien warna primer + titik halus + kilau */
.panel {
  background:
    radial-gradient(circle at 100% 0%, rgb(255 255 255 / 0.22), transparent 45%),
    radial-gradient(rgb(255 255 255 / 0.12) 1.2px, transparent 1.2px) 0 0 / 20px 20px,
    linear-gradient(155deg, var(--color-primary-500, #6366f1), var(--color-primary-700, #4338ca) 60%, var(--color-primary-900, #312e81));
}

/* Input dengan label melayang */
.float {
  position: relative;
}
.float input,
.float textarea {
  width: 100%;
  border-radius: 1rem;
  background-color: #f9fafb;
  padding: 1.5rem 1rem 0.55rem;
  font-size: 0.95rem;
  color: #111827;
  box-shadow: inset 0 0 0 1px #e5e7eb;
  transition: box-shadow 0.2s ease, background-color 0.2s ease;
}
.float textarea {
  padding-bottom: 1.75rem;
}
.float input:hover,
.float textarea:hover {
  background-color: #fff;
}
.float input:focus,
.float textarea:focus {
  outline: none;
  background-color: #fff;
  box-shadow: inset 0 0 0 2px var(--color-primary-500, #6366f1);
}
.float label {
  position: absolute;
  left: 1rem;
  top: 1.05rem;
  font-size: 0.95rem;
  color: #6b7280;
  transform-origin: left top;
  pointer-events: none;
  transition: transform 0.2s ease, color 0.2s ease;
}
.float input:focus + label,
.float input:not(:placeholder-shown) + label,
.float textarea:focus + label,
.float textarea:not(:placeholder-shown) + label {
  transform: translateY(-0.6rem) scale(0.78);
}
.float input:focus + label,
.float textarea:focus + label {
  color: var(--color-primary-600, #4f46e5);
}

/* Tombol kirim */
.send-btn {
  background: linear-gradient(135deg, var(--color-primary-500, #6366f1), var(--color-primary-700, #4338ca));
  box-shadow: 0 10px 24px -8px var(--color-primary-600, #4f46e5);
}
.send-btn:hover:not(:disabled) {
  filter: brightness(1.08);
  box-shadow: 0 14px 28px -8px var(--color-primary-600, #4f46e5);
}

/* Centang digambar saat sukses */
.check {
  stroke-dasharray: 30;
  stroke-dashoffset: 30;
  animation: draw 0.5s 0.15s ease-out forwards;
}
@keyframes draw {
  to {
    stroke-dashoffset: 0;
  }
}

.pop-enter-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}
.pop-leave-active {
  transition: opacity 0.2s ease;
}
.pop-enter-from {
  opacity: 0;
  transform: translateY(-6px) scale(0.98);
}
.pop-leave-to {
  opacity: 0;
}

@media (prefers-reduced-motion: reduce) {
  .animate-spin,
  .animate-ping {
    animation: none;
  }
  .check {
    animation: none;
    stroke-dashoffset: 0;
  }
  .float input,
  .float textarea,
  .float label,
  .pop-enter-active,
  .pop-leave-active {
    transition: none;
  }
}
</style>