<template>
  <div class="flex justify-center">
    <div class="relative w-64 h-64 md:w-96 md:h-96">
      <!-- Glow background -->
      <div class="absolute inset-0 bg-primary-500 rounded-full blur-3xl opacity-20"></div>

      <!-- Clip area supaya karakter "muncul" dari luar frame -->
      <div class="relative w-full h-full overflow-hidden rounded-full">
        <div class="walk-in-wrapper w-full h-full">
          <div class="avatar-float w-full h-full">
            <svg viewBox="0 0 200 290" class="w-full h-full drop-shadow-2xl">

              <!-- Rambut panjang belakang -->
              <path
                class="hair-sway"
                d="M37 90 Q26 36 100 24 Q174 36 163 90 L167 165 Q158 195 148 175 Q152 130 100 130 Q48 130 52 175 Q42 195 33 165 Z"
                fill="#3a2620"
              />

              <!--
                Tangan kanan (secara anatomi tangan kanan si karakter, tampil di
                sisi KIRI penonton) yang melambai.
                Titik pangkal (64,138) presisi ke bahu badan supaya tidak ada celah.

                Bentuknya (atribut d) SEKARANG DIANIMASIKAN, bukan cuma diputar:
                - Bentuk diam/tegap = CERMINAN PERSIS dari tangan statis di pinggul
                  (path "Tangan diam di pinggang" di bawah, di-mirror x' = 200-x),
                  sehingga sebelum & sesudah melambai kedua tangan benar-benar
                  sejajar dan simetris.
                - Bentuk terangkat = kepalan lembut untuk pose melambai.
                <g> di luar hanya menangani rotasi kecil: ayunan saat jalan dan
                goyangan saat melambai — bukan lagi rotasi besar yang membuat
                bentuk kepalan terlihat aneh saat "diam".
              -->
              <g class="wave-arm-swing" style="transform-origin: 64px 138px;">
                <path
                  class="wave-arm-shape"
                  d="M64 138 Q47 144 46 164 Q46 177 54 180 Q58 182 61 178 Q64 181 68 178 Q71 175 68 171 Q71 161 70 149 Z"
                  fill="#f3c9a0"
                />
              </g>

              <!-- Kaki kiri (jalan di awal siklus saja) -->
              <g class="leg-left" style="transform-origin: 85px 205px;">
                <path d="M80 205 Q76 230 79 258 L92 258 Q90 230 90 205 Z" fill="#f3c9a0" />
                <path d="M73 256 Q84 249 95 256 L95 265 Q84 270 73 265 Z" fill="#374151" />
              </g>

              <!-- Kaki kanan -->
              <g class="leg-right" style="transform-origin: 115px 205px;">
                <path d="M120 205 Q124 230 121 258 L108 258 Q110 230 110 205 Z" fill="#f3c9a0" />
                <path d="M105 256 Q116 249 127 256 L127 265 Q116 270 105 265 Z" fill="#374151" />
              </g>

              <!-- Badan / blouse -->
              <path
                d="M64 138
                   Q100 122 136 138
                   Q142 155 138 172
                   Q148 185 146 205
                   L54 205
                   Q52 185 62 172
                   Q58 155 64 138 Z"
                fill="#22d3ee"
              />
              <path d="M64 138 Q100 150 136 138 Q138 155 133 172 L67 172 Q62 155 64 138 Z" fill="#22d3ee" opacity="0.5" />
              <path d="M86 138 L100 158 L114 138" fill="none" stroke="#f472b6" stroke-width="2" />

              <!-- Tangan diam di pinggang (sisi kiri penonton), tidak dianimasikan -->
              <path
                d="M136 138 Q153 144 154 164 Q154 177 146 180
                   Q142 182 139 178
                   Q136 181 132 178
                   Q129 175 132 171
                   Q129 161 130 149 Z"
                fill="#f3c9a0"
              />

              <!-- Leher -->
              <path d="M90 122 Q90 138 100 140 Q110 138 110 122 Z" fill="#f3c9a0" />

              <!-- Wajah oval halus -->
              <path
                d="M100 38
                   Q141 38 147 82
                   Q149 112 137 132
                   Q121 148 100 148
                   Q79 148 63 132
                   Q51 112 53 82
                   Q59 38 100 38 Z"
                fill="#f3c9a0"
              />

              <!-- Rambut depan rapi -->
              <path
                class="hair-sway"
                d="M50 82 Q52 32 100 28 Q148 32 150 82 Q140 50 100 52 Q75 52 64 68 Q56 78 50 82 Z"
                fill="#3a2620"
              />

              <!-- Alis -->
              <path d="M72 72 Q80 67 89 71" stroke="#3a2620" stroke-width="2.5" fill="none" stroke-linecap="round" />
              <path d="M111 71 Q120 67 128 72" stroke="#3a2620" stroke-width="2.5" fill="none" stroke-linecap="round" />

              <!-- Mata kiri -->
              <g class="eye-blink" style="transform-origin: 80px 91px;">
                <ellipse cx="80" cy="91" rx="6.5" ry="7.5" fill="white" />
                <circle cx="80" cy="92" r="4" fill="#3a2620" />
                <circle cx="82" cy="89" r="1.3" fill="white" />
              </g>

              <!-- Mata kanan -->
              <g class="eye-blink" style="transform-origin: 120px 91px;">
                <ellipse cx="120" cy="91" rx="6.5" ry="7.5" fill="white" />
                <circle cx="120" cy="92" r="4" fill="#3a2620" />
                <circle cx="122" cy="89" r="1.3" fill="white" />
              </g>

              <!-- Kacamata bulat hitam -->
              <g class="glasses">
                <circle cx="80" cy="91" r="16" fill="none" stroke="#1a1a1a" stroke-width="3.5" />
                <circle cx="120" cy="91" r="16" fill="none" stroke="#1a1a1a" stroke-width="3.5" />
                <path d="M96 89 Q100 85 104 89" stroke="#1a1a1a" stroke-width="3" fill="none" />
                <path d="M64 87 L52 83" stroke="#1a1a1a" stroke-width="3" stroke-linecap="round" />
                <path d="M136 87 L148 83" stroke="#1a1a1a" stroke-width="3" stroke-linecap="round" />
              </g>

              <!-- Anting -->
              <circle cx="53" cy="100" r="2.3" fill="#ffd93d" />
              <circle cx="147" cy="100" r="2.3" fill="#ffd93d" />

              <!-- Hidung tipis -->
              <path d="M98 100 Q97 106 100 108" stroke="#e0ab7f" stroke-width="1.5" fill="none" stroke-linecap="round" />

              <!-- Pipi blush -->
              <ellipse cx="68" cy="111" rx="7.5" ry="5" fill="#ff9eb5" opacity="0.4" />
              <ellipse cx="132" cy="111" rx="7.5" ry="5" fill="#ff9eb5" opacity="0.4" />

              <!-- Senyum riang -->
              <path
                class="smile-move"
                d="M82 115 Q100 131 118 115"
                stroke="#c2645a"
                stroke-width="3.5"
                fill="none"
                stroke-linecap="round"
              />

            </svg>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
</script>

<style scoped>
/* ===== WRAPPER: berjalan masuk dari kiri, langsung menghadap depan ===== */
.walk-in-wrapper {
  animation: walkIn 8s ease-out infinite;
  transform-origin: center center;
}
@keyframes walkIn {
  0%   { transform: translateX(-130%); }
  10%  { transform: translateX(-90%); }
  20%  { transform: translateX(-40%); }
  28%  { transform: translateX(0%); }
  100% { transform: translateX(0%); }
}

.avatar-float {
  animation: floatY 3s ease-in-out infinite;
}
@keyframes floatY {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-8px); }
}

/* ===== KAKI: jalan hanya selama fase masuk (0-28%) ===== */
.leg-left {
  animation: walkLeftLeg 8s ease-in-out infinite;
}
@keyframes walkLeftLeg {
  0%   { transform: rotate(0deg); }
  7%   { transform: rotate(16deg); }
  14%  { transform: rotate(-12deg); }
  21%  { transform: rotate(16deg); }
  28%, 100% { transform: rotate(0deg); }
}

.leg-right {
  animation: walkRightLeg 8s ease-in-out infinite;
}
@keyframes walkRightLeg {
  0%   { transform: rotate(0deg); }
  7%   { transform: rotate(-12deg); }
  14%  { transform: rotate(16deg); }
  21%  { transform: rotate(-12deg); }
  28%, 100% { transform: rotate(0deg); }
}

/*
  ===== TANGAN KANAN YANG MELAMBAI =====
  Pendekatan baru: alih-alih memutar SATU bentuk kepalan (yang membuat pose
  "diam" terlihat aneh karena bentuknya memang digambar untuk pose terangkat),
  sekarang ada DUA animasi terpisah yang jalan bersamaan:

  1. .wave-arm-shape  -> animasi bentuk (atribut d) dari path itu sendiri.
     Bentuk diam = cerminan persis tangan statis di pinggul, jadi otomatis
     SEJAJAR & simetris dengan tangan kiri saat tegap. Bentuk terangkat =
     kepalan lembut untuk melambai. Ini yang menjamin "tegap sejajar" di
     sebelum & sesudah melambai, bukan lagi soal sudut rotasi.

  2. .wave-arm-swing  -> rotasi KECIL saja di sekitar 0deg: dipakai untuk
     ayunan wajar saat berjalan (0-28%) dan goyangan saat melambai (42-77%).
     Di luar itu rotasinya 0deg, karena bentuk "tegap"-nya sudah benar dari
     morph d di atas, tidak perlu dibantu rotasi besar lagi.
*/
.wave-arm-shape {
  animation: waveArmShape 8s ease-in-out infinite;
}
@keyframes waveArmShape {
  /* Tegap (cerminan tangan pinggul) selama jalan & jeda sebelum melambai */
  0%, 34% {
    d: path("M64 138 Q47 144 46 164 Q46 177 54 180 Q58 182 61 178 Q64 181 68 178 Q71 175 68 171 Q71 161 70 149 Z");
  }
  /* Naik ke bentuk kepalan terangkat, siap melambai */
  42% {
    d: path("M64 138 Q42 123 37 97 Q35 87 42 84 Q47 82 51 88 Q54 84 59 87 Q62 83 66 88 Q68 100 70 120 Z");
  }
  /* Tetap di bentuk terangkat selama goyang melambai */
  77% {
    d: path("M64 138 Q42 123 37 97 Q35 87 42 84 Q47 82 51 88 Q54 84 59 87 Q62 83 66 88 Q68 100 70 120 Z");
  }
  /* Turun kembali & tetap tegap (sejajar tangan kiri) sampai siklus berakhir */
  88%, 100% {
    d: path("M64 138 Q47 144 46 164 Q46 177 54 180 Q58 182 61 178 Q64 181 68 178 Q71 175 68 171 Q71 161 70 149 Z");
  }
}

.wave-arm-swing {
  animation: waveArmSwing 8s ease-in-out infinite;
}
@keyframes waveArmSwing {
  /* Ayunan wajar saat jalan (bentuk masih tegap di fase ini) */
  0%   { transform: rotate(0deg); }
  7%   { transform: rotate(10deg); }
  14%  { transform: rotate(-8deg); }
  21%  { transform: rotate(10deg); }
  28%, 38% { transform: rotate(0deg); } /* berhenti jalan, jeda tegap, lalu terangkat tanpa rotasi ekstra */

  /* Goyangan saat melambai (bentuk sudah jadi kepalan terangkat di fase ini) */
  47%  { transform: rotate(16deg); }
  52%  { transform: rotate(-6deg); }
  57%  { transform: rotate(16deg); }
  62%  { transform: rotate(-6deg); }
  67%  { transform: rotate(16deg); }
  72%  { transform: rotate(-6deg); }
  77%  { transform: rotate(16deg); }

  /* Kembali ke 0deg begitu bentuk mulai turun ke tegap lagi */
  82%, 100% { transform: rotate(0deg); }
}

.hair-sway {
  animation: sway 5s ease-in-out infinite;
  transform-origin: 100px 40px;
}
@keyframes sway {
  0%, 100% { transform: rotate(0deg); }
  50% { transform: rotate(1deg); }
}

.smile-move {
  animation: smileMove 2.5s ease-in-out infinite;
}
@keyframes smileMove {
  0%, 100% { d: path("M82 115 Q100 131 118 115"); }
  50% { d: path("M82 116 Q100 134 118 116"); }
}

.eye-blink {
  animation: blink 4.5s ease-in-out infinite;
}
@keyframes blink {
  0%, 90%, 100% { transform: scaleY(1); }
  93%, 96% { transform: scaleY(0.1); }
}
</style>