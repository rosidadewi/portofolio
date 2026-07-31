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

async function handleSubmit() {
  // Kalau honeypot terisi, kemungkinan besar ini bot.
  // Diamkan saja seolah-olah sukses, tanpa benar-benar mengirim request.
  if (form.botcheck) {
    isSubmitted.value = true
    form.name = ''
    form.email = ''
    form.message = ''
    setTimeout(() => {
      isSubmitted.value = false
    }, 4000)
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
      isSubmitted.value = true
      form.name = ''
      form.email = ''
      form.message = ''
      setTimeout(() => {
        isSubmitted.value = false
      }, 4000)
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
const contactInfo = [
  { icon: 'mail', label: 'rosidadewiutami30@gmail.com' },
  { icon: 'map', label: 'Surakarta, Jawa Tengah' },
]

const socialLinks = [
  { icon: 'github', label: 'GitHub', href: 'https://github.com/rosidadewi' },
  { icon: 'linkedin', label: 'LinkedIn', href: 'https://www.linkedin.com/in/rosida-dewi-utami-397514290/' },
  { icon: 'instagram', label: 'Instagram', href: 'https://www.instagram.com/rosidadeu_?igsh=MTdnYXdnYzRjejBtaQ==' },
  { icon: 'whatsapp', label: 'WhatsApp', href: 'https://wa.me/6282134657795?text=Halo%2C%20saya%20ingin%20bertanya'},
]
</script>

<template>
  <section id="contact" class="relative py-20 px-6 bg-gray-50 overflow-hidden">
    <!-- Dekorasi blob gradien lembut -->
    <div class="pointer-events-none absolute -top-24 -right-16 w-[24rem] h-[24rem] rounded-full bg-primary-100/60 blur-3xl -z-10"></div>
    <div class="pointer-events-none absolute -bottom-28 -left-16 w-[20rem] h-[20rem] rounded-full bg-blue-100/50 blur-3xl -z-10"></div>

    <div class="max-w-5xl mx-auto">
      <span class="inline-flex items-center gap-2 text-xs font-semibold tracking-wide text-primary-700 bg-primary-50 border border-primary-100 px-3 py-1 rounded-full mb-3">
        <span class="w-1.5 h-1.5 rounded-full bg-primary-500"></span>
        Mari Terhubung
      </span>

      <h2 class="section-title">Hubungi Saya</h2>
      <p class="section-subtitle">
        Punya proyek atau pertanyaan? Kirim pesan lewat form di bawah ini.
      </p>

      <div class="mt-10 grid lg:grid-cols-5 gap-8 items-start">
        <!-- Panel info kontak -->
        <div class="lg:col-span-2 space-y-6">
          <div class="bg-white rounded-2xl border border-gray-100 shadow-sm p-6">
            <div class="flex items-center gap-2 mb-4">
              <span class="relative flex h-2.5 w-2.5">
                <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span>
                <span class="relative inline-flex rounded-full h-2.5 w-2.5 bg-emerald-500"></span>
              </span>
              <span class="text-sm font-semibold text-gray-700">Terbuka untuk kolaborasi</span>
            </div>

            <div class="space-y-3">
              <div v-for="item in contactInfo" :key="item.label" class="flex items-center gap-3">
                <span class="w-9 h-9 rounded-full bg-primary-50 text-primary-600 flex items-center justify-center shrink-0">
                  <svg v-if="item.icon === 'mail'" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <rect x="2" y="4" width="20" height="16" rx="2" />
                    <path d="m22 6-10 7L2 6" />
                  </svg>
                  <svg v-else viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M20 10c0 6-8 12-8 12s-8-6-8-12a8 8 0 0 1 16 0Z" />
                    <circle cx="12" cy="10" r="3" />
                  </svg>
                </span>
                <span class="text-sm text-gray-600">{{ item.label }}</span>
              </div>
            </div>

            <div class="border-t border-gray-100 mt-5 pt-5 flex items-center gap-3">
              <a
                v-for="social in socialLinks"
                :key="social.label"
                :href="social.href"
                target="_blank"
                rel="noopener"
                :aria-label="social.label"
                class="w-9 h-9 rounded-full bg-gray-50 hover:bg-primary-600 border border-gray-100 text-gray-500 hover:text-white flex items-center justify-center transition-all duration-300 hover:-translate-y-0.5"
              >
                <svg v-if="social.icon === 'github'" viewBox="0 0 24 24" width="15" height="15" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M15 22v-3.87a3.37 3.37 0 0 0-.94-2.61c3.14-.35 6.44-1.54 6.44-7A5.44 5.44 0 0 0 19 4.77 5.07 5.07 0 0 0 18.91.65S17.73.35 15 2.48a13.38 13.38 0 0 0-7 0C5.27.35 4.09.65 4.09.65A5.07 5.07 0 0 0 4 4.77a5.44 5.44 0 0 0-1.5 3.75c0 5.42 3.3 6.61 6.44 7A3.37 3.37 0 0 0 8 18.13V22" />
                </svg>
                <svg v-else-if="social.icon === 'linkedin'" viewBox="0 0 24 24" width="15" height="15" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M16 8a6 6 0 0 1 6 6v7h-4v-7a2 2 0 0 0-2-2 2 2 0 0 0-2 2v7h-4v-7a6 6 0 0 1 6-6Z" />
                  <rect x="2" y="9" width="4" height="12" />
                  <circle cx="4" cy="4" r="2" />
                </svg>
                <svg v-else-if="social.icon === 'instagram'" viewBox="0 0 24 24" width="15" height="15" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <rect x="2" y="2" width="20" height="20" rx="5" />
                  <path d="M16 11.37A4 4 0 1 1 12.63 8 4 4 0 0 1 16 11.37Z" />
                  <line x1="17.5" y1="6.5" x2="17.51" y2="6.5" />
                </svg>
                <svg v-else viewBox="0 0 24 24" width="15" height="15" fill="currentColor">
                  <path d="M17.6 6.32A8.86 8.86 0 0 0 12.05 4C7.2 4 3.26 7.9 3.26 12.7c0 1.6.42 3.1 1.15 4.4L3 21l4.02-1.32a8.9 8.9 0 0 0 4.99 1.5h.01c4.85 0 8.79-3.9 8.79-8.7a8.6 8.6 0 0 0-2.61-6.16Zm-5.55 13.4h-.01a7.4 7.4 0 0 1-3.77-1.03l-.27-.16-2.8.92.94-2.72-.18-.28a7.24 7.24 0 0 1-1.12-3.85c0-4 3.28-7.26 7.31-7.26 1.95 0 3.79.76 5.17 2.13a7.19 7.19 0 0 1 2.14 5.13c0 4-3.28 7.26-7.31 7.26Zm4.01-5.44c-.22-.11-1.3-.64-1.5-.71-.2-.07-.35-.11-.5.11-.15.22-.57.71-.7.86-.13.14-.26.16-.48.05-.22-.11-.93-.34-1.77-1.1-.65-.58-1.1-1.3-1.22-1.52-.13-.22-.01-.34.1-.45.1-.1.22-.26.33-.39.11-.13.15-.22.22-.37.07-.15.04-.28-.02-.39-.06-.11-.5-1.2-.68-1.65-.18-.43-.36-.37-.5-.38h-.43c-.15 0-.39.06-.59.28-.2.22-.78.76-.78 1.85s.8 2.15.91 2.3c.11.15 1.57 2.4 3.81 3.36.53.23.95.37 1.27.47.53.17 1.02.15 1.4.09.43-.06 1.3-.53 1.48-1.04.18-.51.18-.94.13-1.03-.05-.09-.2-.15-.42-.26Z" />
                </svg>
              </a>
            </div>
          </div>

          <p class="text-sm text-gray-500 leading-relaxed px-1">
            Biasanya saya membalas pesan dalam 1–2 hari kerja.
          </p>
        </div>

        <!-- Form -->
        <form @submit.prevent="handleSubmit" class="lg:col-span-3 bg-white rounded-2xl shadow-sm border border-gray-100 p-8 space-y-5 relative">

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

          <div>
            <label for="name" class="block text-sm font-medium text-gray-700 mb-1.5">Nama</label>
            <div class="relative">
              <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="absolute left-3.5 top-1/2 -translate-y-1/2 text-gray-400">
                <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2" />
                <circle cx="12" cy="7" r="4" />
              </svg>
              <input
                id="name"
                v-model="form.name"
                type="text"
                required
                placeholder="Nama kamu"
                class="w-full pl-10 pr-4 py-3 rounded-lg border border-gray-200 focus:outline-none focus:ring-2 focus:ring-primary-500 focus:border-transparent transition-shadow"
              />
            </div>
          </div>

          <div>
            <label for="email" class="block text-sm font-medium text-gray-700 mb-1.5">Email</label>
            <div class="relative">
              <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="absolute left-3.5 top-1/2 -translate-y-1/2 text-gray-400">
                <rect x="2" y="4" width="20" height="16" rx="2" />
                <path d="m22 6-10 7L2 6" />
              </svg>
              <input
                id="email"
                v-model="form.email"
                type="email"
                required
                placeholder="email@contoh.com"
                class="w-full pl-10 pr-4 py-3 rounded-lg border border-gray-200 focus:outline-none focus:ring-2 focus:ring-primary-500 focus:border-transparent transition-shadow"
              />
            </div>
          </div>

          <div>
            <label for="message" class="block text-sm font-medium text-gray-700 mb-1.5">Pesan</label>
            <div class="relative">
              <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="absolute left-3.5 top-3.5 text-gray-400">
                <path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z" />
              </svg>
              <textarea
                id="message"
                v-model="form.message"
                required
                rows="4"
                placeholder="Tulis pesan kamu di sini..."
                class="w-full pl-10 pr-4 py-3 rounded-lg border border-gray-200 focus:outline-none focus:ring-2 focus:ring-primary-500 focus:border-transparent resize-none transition-shadow"
              ></textarea>
            </div>
          </div>

          <button
            type="submit"
            :disabled="isSending"
            class="btn-primary w-full text-center flex items-center justify-center gap-2 disabled:opacity-70 disabled:cursor-not-allowed"
          >
            <svg
              v-if="isSending"
              class="animate-spin"
              viewBox="0 0 24 24"
              width="16"
              height="16"
              fill="none"
            >
              <circle cx="12" cy="12" r="9" stroke="currentColor" stroke-width="3" stroke-opacity="0.25" />
              <path d="M21 12a9 9 0 0 0-9-9" stroke="currentColor" stroke-width="3" stroke-linecap="round" />
            </svg>
            <span>{{ isSending ? 'Mengirim...' : 'Kirim Pesan' }}</span>
            <svg v-if="!isSending" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <line x1="22" y1="2" x2="11" y2="13" />
              <polygon points="22 2 15 22 11 13 2 9 22 2" />
            </svg>
          </button>

          <!-- Status sukses -->
          <transition name="pop">
            <div
              v-if="isSubmitted"
              class="flex items-center gap-3 bg-emerald-50 border border-emerald-100 text-emerald-700 text-sm rounded-xl px-4 py-3"
            >
              <span class="w-7 h-7 rounded-full bg-emerald-500 text-white flex items-center justify-center shrink-0">
                <svg viewBox="0 0 24 24" width="14" height="14" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
                  <polyline points="20 6 9 17 4 12" />
                </svg>
              </span>
              Pesan berhasil terkirim! Terima kasih sudah menghubungi saya.
            </div>
          </transition>

          <!-- Status error -->
          <transition name="pop">
            <div
              v-if="errorMessage"
              class="flex items-center gap-3 bg-red-50 border border-red-100 text-red-600 text-sm rounded-xl px-4 py-3"
            >
              <span class="w-7 h-7 rounded-full bg-red-500 text-white flex items-center justify-center shrink-0">
                <svg viewBox="0 0 24 24" width="14" height="14" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
                  <line x1="18" y1="6" x2="6" y2="18" />
                  <line x1="6" y1="6" x2="18" y2="18" />
                </svg>
              </span>
              {{ errorMessage }}
            </div>
          </transition>
        </form>
      </div>
    </div>
  </section>
</template>

<style scoped>
.pop-enter-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}
.pop-leave-active {
  transition: opacity 0.2s ease;
}
.pop-enter-from {
  opacity: 0;
  transform: translateY(-6px) scale(0.97);
}
.pop-leave-to {
  opacity: 0;
}

@media (prefers-reduced-motion: reduce) {
  .animate-spin,
  .animate-ping {
    animation: none;
  }
}
</style>