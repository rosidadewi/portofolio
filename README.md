# Vue Portfolio

Landing page portofolio pribadi sederhana dibangun dengan **Vue 3 + Vite + Tailwind CSS**.

## Struktur Komponen
- `Navbar.vue` — navigasi dengan menu responsive (hamburger di mobile)
- `Hero.vue` — bagian intro/perkenalan
- `About.vue` — tentang diri + statistik singkat
- `Skills.vue` — daftar skill dengan progress bar
- `Projects.vue` — grid kartu proyek
- `Contact.vue` — form kontak (v-model + reactive state)
- `Footer.vue` — footer dengan link sosial media

## Cara Menjalankan

1. Pastikan Node.js (versi 18+) sudah terinstall.
2. Install dependency:
   ```bash
   npm install
   ```
3. Jalankan server development:
   ```bash
   npm run dev
   ```
4. Buka browser di alamat yang muncul di terminal (biasanya `http://localhost:5173`).

## Build untuk Production
```bash
npm run build
```
Hasil build akan ada di folder `dist/`, siap di-deploy ke Netlify, Vercel, atau GitHub Pages.

## Kustomisasi
- Ganti teks "Nama Kamu", deskripsi, dan data di masing-masing file `.vue` di dalam `src/components/`.
- Warna tema bisa diubah di `tailwind.config.js` pada bagian `theme.extend.colors.primary`.
- Data skill & proyek ada di dalam `<script setup>` masing-masing komponen (`Skills.vue` dan `Projects.vue`) — tinggal edit array-nya.
