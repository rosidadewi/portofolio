<template>
  <div class="flex justify-center">
    <div class="relative h-64 w-64 md:h-96 md:w-96">
      <!-- Cahaya di belakang -->
      <div class="absolute inset-0 rounded-full bg-primary-500 opacity-30 blur-3xl" aria-hidden="true"></div>
      <!-- Cincin putus-putus di luar bingkai -->
      <div class="absolute -inset-3 rounded-full border border-dashed border-primary-300/70" aria-hidden="true"></div>

      <!-- Balon sapaan: muncul hanya saat karakter melambai -->
      <div
        class="hello-bubble absolute left-0 top-1 z-10 rounded-2xl bg-white px-4 py-2 text-sm font-bold text-gray-900 shadow-xl shadow-primary-900/10 ring-1 ring-gray-200 md:-left-10 md:top-6 md:px-5 md:py-2.5 md:text-base"
        aria-hidden="true"
      >
        Halo!
        <span class="absolute -bottom-1.5 right-5 h-3 w-3 rotate-45 bg-white ring-1 ring-gray-200 [clip-path:polygon(100%_0,100%_100%,0_100%)]"></span>
      </div>

      <!-- Chip melayang: tanpa backdrop-blur agar tidak dihitung ulang tiap frame -->
      <div
        class="chip chip-a absolute right-0 top-10 z-10 flex h-10 w-10 items-center justify-center rounded-2xl bg-white/95 text-primary-600 shadow-lg shadow-primary-900/10 ring-1 ring-gray-200 md:-right-6 md:h-12 md:w-12"
        aria-hidden="true"
      >
        <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round">
          <polyline points="16 18 22 12 16 6" />
          <polyline points="8 6 2 12 8 18" />
        </svg>
      </div>
      <div
        class="chip chip-b absolute bottom-12 left-0 z-10 flex h-9 w-9 items-center justify-center rounded-2xl bg-white/95 text-pink-500 shadow-lg shadow-primary-900/10 ring-1 ring-gray-200 md:-left-5 md:h-11 md:w-11"
        aria-hidden="true"
      >
        <svg viewBox="0 0 24 24" width="18" height="18" fill="currentColor">
          <path d="M12 2l2.4 6.6L21 11l-6.6 2.4L12 20l-2.4-6.6L3 11l6.6-2.4z" />
        </svg>
      </div>

      <!-- Bingkai bulat; karakter "muncul" dari luar bingkai -->
      <div
        class="relative h-full w-full overflow-hidden rounded-full bg-gradient-to-b from-primary-50 via-white to-primary-100 shadow-2xl shadow-primary-900/15 ring-4 ring-white"
      >
        <!-- Latar: ruang dalam rumah dengan jendela berkaca, tirai, kusen & lantai kayu.
             Digambar di kotak 100x100, dipotong mengikuti bingkai bulat. -->
        <svg
          viewBox="0 0 100 100"
          preserveAspectRatio="xMidYMid slice"
          class="absolute inset-0 h-full w-full"
          aria-hidden="true"
        >
          <defs>
            <linearGradient id="win-wall" x1="0" y1="0" x2="0" y2="1">
              <stop offset="0" stop-color="#fff6ee" />
              <stop offset="1" stop-color="#f6e2d0" />
            </linearGradient>
            <radialGradient id="win-light" cx="50" cy="34" r="46" gradientUnits="userSpaceOnUse">
              <stop offset="0" stop-color="#fff" stop-opacity="0.75" />
              <stop offset="1" stop-color="#fff" stop-opacity="0" />
            </radialGradient>
            <linearGradient id="win-sky" x1="0" y1="0" x2="0" y2="1">
              <stop offset="0" stop-color="#7fcdf7" />
              <stop offset="1" stop-color="#e4f5ff" />
            </linearGradient>
            <linearGradient id="win-floor" x1="0" y1="0" x2="0" y2="1">
              <stop offset="0" stop-color="#e8c8a2" />
              <stop offset="1" stop-color="#cda076" />
            </linearGradient>
            <linearGradient id="win-curtain" x1="0" y1="0" x2="1" y2="0">
              <stop offset="0" stop-color="#f9a8d4" />
              <stop offset="1" stop-color="#fbcfe8" />
            </linearGradient>
            <clipPath id="win-glass"><rect x="28" y="14" width="44" height="42" /></clipPath>
            <!-- tirai kiri; tirai kanan memakai cerminannya -->
            <g id="win-curtain-l">
              <path d="M4 8 H27 C29 28 25 58 28 84 H4 Z" fill="url(#win-curtain)" />
              <path d="M10 9 C11 35 9 60 10 84 M16 9 C18 35 15 60 17 84 M22 9 C24 35 21 60 23 84" stroke="#ec4899" stroke-opacity="0.2" stroke-width="1" fill="none" stroke-linecap="round" />
              <path d="M5 51 C13 55 21 55 27.6 51 L27.8 55.5 C21 59.5 13 59.5 5 55.5 Z" fill="#ec4899" opacity="0.85" />
            </g>
          </defs>

          <!-- dinding + cahaya lembut dari jendela -->
          <rect width="100" height="100" fill="url(#win-wall)" />
          <rect width="100" height="100" fill="url(#win-light)" />

          <!-- lantai kayu + plint -->
          <rect y="84" width="100" height="16" fill="url(#win-floor)" />
          <path d="M0 88.5 H100 M0 93 H100 M0 97.5 H100" stroke="#a97c52" stroke-opacity="0.25" stroke-width="0.4" />
          <rect y="81.5" width="100" height="2.5" fill="#fff" />
          <rect y="84" width="100" height="0.6" fill="#000" opacity="0.08" />

          <!-- jendela: pemandangan di luar -->
          <rect x="28" y="14" width="44" height="42" fill="url(#win-sky)" />
          <g clip-path="url(#win-glass)">
            <circle cx="62" cy="24" r="7" fill="#fff4b0" opacity="0.55" />
            <circle cx="62" cy="24" r="3.6" fill="#ffd93d" />
            <g class="cloud">
              <g fill="#fff" opacity="0.95">
                <ellipse cx="38" cy="25" rx="6" ry="2.4" />
                <ellipse cx="42" cy="23.4" rx="4" ry="2.4" />
                <ellipse cx="34.5" cy="24" rx="3.2" ry="1.8" />
              </g>
            </g>
            <g class="cloud cloud-b">
              <g fill="#fff" opacity="0.85">
                <ellipse cx="57" cy="40" rx="5" ry="2" />
                <ellipse cx="60" cy="38.6" rx="3.2" ry="2" />
              </g>
            </g>
            <!-- bukit & pohon di kejauhan -->
            <path d="M28 52 C36 44 44 44 52 49 C58 45 66 45 72 50 V56 H28 Z" fill="#8fd694" />
            <path d="M28 55 C38 49 50 51 60 53 C65 51 69 51 72 53 V56 H28 Z" fill="#5fbf75" />
            <rect x="34.6" y="46" width="1.2" height="5" fill="#8a5a3c" />
            <circle cx="35.2" cy="44.5" r="3.6" fill="#3fa965" />
            <!-- pantulan kaca -->
            <path d="M32 56 L44 14 H50 L38 56 Z M52 56 L62 21 H65 L55 56 Z" fill="#fff" opacity="0.22" />
          </g>

          <!-- kusen & sekat jendela -->
          <path d="M24 10 H76 V60 H24 Z M28 14 V56 H72 V14 Z" fill="#fff" fill-rule="evenodd" />
          <rect x="48.5" y="14" width="3" height="42" fill="#fff" />
          <rect x="28" y="33.5" width="44" height="3" fill="#fff" />
          <path d="M24.4 10.4 H75.6 V59.6 H24.4 Z" fill="none" stroke="#d9c6b3" stroke-width="0.8" />
          <path d="M28.4 14.4 H71.6 V55.6 H28.4 Z" fill="none" stroke="#d9c6b3" stroke-width="0.5" />
          <!-- ambang jendela -->
          <rect x="20" y="58.5" width="60" height="4" rx="1" fill="#fff" />
          <rect x="20" y="62" width="60" height="1" fill="#000" opacity="0.07" />
          <rect x="25" y="63" width="50" height="3" fill="#f3e6d8" />

          <!-- tirai kiri & kanan (cerminan) + rel -->
          <use href="#win-curtain-l" />
          <g transform="translate(100 0) scale(-1 1)"><use href="#win-curtain-l" /></g>
          <path d="M2 7 H98" stroke="#b08968" stroke-width="1.4" stroke-linecap="round" />
          <circle cx="2" cy="7" r="1.8" fill="#8b6a4f" />
          <circle cx="98" cy="7" r="1.8" fill="#8b6a4f" />
        </svg>

        <!-- pijakan lantai: diam, tempat karakter berdiri -->
        <div class="absolute bottom-[3.5%] left-1/2 h-[9%] w-[64%] -translate-x-1/2 rounded-full bg-primary-200/60 ring-1 ring-white/70" aria-hidden="true"></div>

        <!-- tanaman kecil di lantai -->
        <div class="absolute bottom-[8%] right-[25%] w-[11%]" aria-hidden="true">
          <svg viewBox="0 0 40 52" class="w-full overflow-visible">
            <g class="plant-leaves" style="transform-origin: 20px 32px;">
              <path d="M20 32 Q8 24 10 8 Q22 14 20 32Z" fill="#34d399" />
              <path d="M20 32 Q32 22 31 6 Q18 12 20 32Z" fill="#10b981" />
              <path d="M20 32 Q16 16 20 2 Q26 16 20 32Z" fill="#6ee7b7" />
            </g>
            <path d="M9 34 H31 L28 50 Q20 52 12 50 Z" fill="#fb923c" />
            <rect x="8" y="31" width="24" height="5" rx="1.5" fill="#ea580c" />
          </svg>
        </div>

        <div class="walk-in-wrapper relative h-full w-full">
          <!-- Bayangan menempel di kaki: satu lebar-lembut, satu rapat-gelap -->
          <div class="absolute bottom-[4.9%] left-1/2 h-[4.5%] w-[46%] -translate-x-1/2 rounded-full bg-gray-900/10 blur-[4px]" aria-hidden="true"></div>
          <div class="absolute bottom-[6.2%] left-1/2 h-[2%] w-[30%] -translate-x-1/2 rounded-full bg-gray-900/25 blur-[1px]" aria-hidden="true"></div>

          <!-- walk-bob: goyang naik-turun tiap langkah, terpisah dari gerak maju -->
          <div class="walk-bob h-full w-full">
            <div class="avatar-float h-full w-full">
              <!-- drop-shadow dihapus: filter pada SVG yang terus bergerak sangat berat -->
              <svg
                viewBox="0 0 200 290"
                class="h-full w-full"
                role="img"
                aria-label="Ilustrasi avatar yang berjalan masuk lalu melambai"
              >
                <defs>
                  <!-- Kulit: satu gradien untuk seluruh tubuh supaya warnanya konsisten -->
                  <linearGradient id="av-skin" gradientUnits="userSpaceOnUse" x1="0" y1="30" x2="0" y2="270">
                    <stop offset="0" stop-color="#fbd9b5" />
                    <stop offset="1" stop-color="#eeb68b" />
                  </linearGradient>
                  <linearGradient id="av-hair" x1="0" y1="0" x2="0" y2="1">
                    <stop offset="0" stop-color="#5a3a2e" />
                    <stop offset="1" stop-color="#2e1c17" />
                  </linearGradient>
                  <linearGradient id="av-top" x1="0" y1="0" x2="0" y2="1">
                    <stop offset="0" stop-color="#67e8f9" />
                    <stop offset="1" stop-color="#06b6d4" />
                  </linearGradient>
                  <linearGradient id="av-shorts" x1="0" y1="0" x2="0" y2="1">
                    <stop offset="0" stop-color="#5b7bea" />
                    <stop offset="1" stop-color="#3b4fb8" />
                  </linearGradient>
                  <clipPath id="av-mouth"><path d="M84 116 Q100 122 116 116 Q100 135 84 116 Z" /></clipPath>
                  <clipPath id="av-lens-l"><circle cx="80" cy="91" r="14.5" /></clipPath>
                  <clipPath id="av-lens-r"><circle cx="120" cy="91" r="14.5" /></clipPath>
                  <!-- Bentuk mata almond (dipakai untuk memotong iris) -->
                  <clipPath id="av-eye-l"><path d="M70 90 Q80 80 90 92 Q80 99 70 90 Z" /></clipPath>
                  <clipPath id="av-eye-r"><path d="M110 92 Q120 80 130 90 Q120 99 110 92 Z" /></clipPath>
                  <!-- Lengan baju mengembang (puff): gambar sekali, dipakai untuk kedua lengan.
                       Digambar menghadap lurus ke bawah dari bahu (66,142) -->
                  <linearGradient id="av-sleeve-fill" x1="0" y1="0" x2="0" y2="1">
                    <stop offset="0" stop-color="#8af2ff" />
                    <stop offset="1" stop-color="#17b8d4" />
                  </linearGradient>
                  <g id="av-sleeve">
                    <!-- balon lengan: membulat di bahu, mengerut di ujung -->
                    <path d="M53 162 C45 157 47 137 56 134 Q62 132 68 134 C77 137 79 157 71 162 Q62 165 53 162 Z" fill="url(#av-sleeve-fill)" />
                    <!-- kerutan kain mengarah ke ujung lengan -->
                    <path d="M57 139 C54 148 55 156 56 161 M62 137 C61 147 62 156 62 163 M67 139 C69 148 69 156 68 161" stroke="#0891b2" stroke-opacity="0.28" stroke-width="1.1" fill="none" stroke-linecap="round" />
                    <!-- kilau di sisi luar balon -->
                    <path d="M52 143 C50 149 51 154 54 158" stroke="#fff" stroke-opacity="0.4" stroke-width="2.2" fill="none" stroke-linecap="round" />
                    <!-- karet ujung lengan -->
                    <path d="M52.5 161 Q62 166 71.5 161 L71 166.5 Q62 171.5 53 166.5 Z" fill="#f472b6" />
                    <path d="M53 163.8 Q62 168.8 71.2 163.8" stroke="#fff" stroke-opacity="0.45" stroke-width="0.9" fill="none" stroke-linecap="round" />
                  </g>
                  <linearGradient id="av-shoe" x1="0" y1="0" x2="0" y2="1">
                    <stop offset="0" stop-color="#4b5563" />
                    <stop offset="1" stop-color="#1f2937" />
                  </linearGradient>
                </defs>

                <!-- Kaki kiri (jalan di awal siklus saja) -->
                <g transform="rotate(-2 85 205)">
                  <g class="leg-left" style="transform-origin: 85px 205px;">
                    <path d="M80 205 Q73 232 79 258 L92 258 Q93 232 90 205 Z" fill="url(#av-skin)" />
                    <path d="M73 256 Q84 249 95 256 L95 265 Q84 270 73 265 Z" fill="url(#av-shoe)" />
                    <path d="M74 264 Q84 269 94 264" stroke="#fff" stroke-opacity="0.45" stroke-width="1.5" fill="none" stroke-linecap="round" />
                    <path d="M80 257.5 L86 255.5 M84 260.5 L90 258.5" stroke="#fff" stroke-opacity="0.55" stroke-width="1.2" stroke-linecap="round" />
                  </g>
                </g>

                <!-- Kaki kanan -->
                <g transform="rotate(2 115 205)">
                  <g class="leg-right" style="transform-origin: 115px 205px;">
                    <path d="M120 205 Q127 232 121 258 L108 258 Q107 232 110 205 Z" fill="url(#av-skin)" />
                    <path d="M105 256 Q116 249 127 256 L127 265 Q116 270 105 265 Z" fill="url(#av-shoe)" />
                    <path d="M106 264 Q116 269 126 264" stroke="#fff" stroke-opacity="0.45" stroke-width="1.5" fill="none" stroke-linecap="round" />
                    <path d="M120 257.5 L114 255.5 M116 260.5 L110 258.5" stroke="#fff" stroke-opacity="0.55" stroke-width="1.2" stroke-linecap="round" />
                  </g>
                </g>

                <!-- Rok A-line pas badan -->
                <path
                  d="M59 198 L141 198
                     C144 214 147 226 151 238
                     C137 243 118 242 100 242
                     C82 242 63 243 49 238
                     C53 226 56 214 59 198 Z"
                  fill="url(#av-shorts)"
                />
                <!-- bayangan lipit: pita gelap luar kiri & kanan (simetris) -->
                <path d="M68 210 L84 211 L81 241 L62 238 Z M132 210 L116 211 L119 241 L138 238 Z" fill="#1e2f8f" opacity="0.1" />
                <!-- garis lipit + sorot tipis -->
                <path d="M68 210 L62.5 238 M84 211 L81 241 M116 211 L119 241 M132 210 L137.5 238" stroke="#1e2f8f" stroke-opacity="0.28" stroke-width="1.3" stroke-linecap="round" fill="none" />
                <path d="M70.5 210 L65 238 M86.5 211 L83.5 241 M113.5 211 L116.5 241 M129.5 210 L135 238" stroke="#fff" stroke-opacity="0.16" stroke-width="1" stroke-linecap="round" fill="none" />
                <!-- ikat pinggang -->
                <path d="M59 198 H141 L143.6 211 Q100 217 56.4 211 Z" fill="#3446ad" />
                <path d="M57 211 Q100 217 143 211" stroke="#fff" stroke-opacity="0.22" stroke-width="1" fill="none" stroke-linecap="round" />
                <!-- jahitan kelim -->
                <path d="M50.5 234 C64 239.5 82 238.5 100 238.5 C118 238.5 136 239.5 149.5 234" stroke="#fff" stroke-opacity="0.45" stroke-width="1.2" stroke-dasharray="3 3" stroke-linecap="round" fill="none" />

                <!-- Torso condong sedikit; kepala miring berlawanan arah: postur lebih santai -->
                <g class="torso">
                  <g class="head">
                    <!-- Rambut panjang belakang -->
                    <path
                      class="hair-back"
                      d="M100 20
                         C54 20 32 54 33 94
                         C34 122 28 150 36 176
                         C40 190 54 194 62 184
                         C58 170 60 150 64 132
                         L136 132
                         C140 150 142 170 138 184
                         C146 194 160 190 164 176
                         C172 150 166 122 167 94
                         C168 54 146 20 100 20 Z"
                      fill="url(#av-hair)"
                    />
                    <!-- helai rambut -->
                    <path
                      class="hair-back"
                      d="M46 96 C42 126 44 152 48 178 M58 110 C55 135 57 158 60 178 M154 96 C158 126 156 152 152 178 M142 110 C145 135 143 158 140 178"
                      stroke="#fff"
                      stroke-opacity="0.1"
                      stroke-width="2"
                      fill="none"
                      stroke-linecap="round"
                    />
                    <path
                      class="hair-back"
                      d="M40 110 C37 135 38 158 42 180 M160 110 C163 135 162 158 158 180"
                      stroke="#000"
                      stroke-opacity="0.12"
                      stroke-width="2"
                      fill="none"
                      stroke-linecap="round"
                    />
                  </g>

                  <!-- Badan / blouse -->
                  <path
                    d="M66 138
                       Q100 127 134 138
                       L137 168
                       Q138 187 142 204
                       Q100 211 58 204
                       Q62 187 63 168
                       Z"
                    fill="url(#av-top)"
                  />
                  <!-- bayangan tipis di bawah bahu -->
                  <path d="M66 138 Q100 149 134 138 L135 152 Q100 162 65 152 Z" fill="#0891b2" opacity="0.14" />
                  <!-- kerah V + plaket dan kancing -->
                  <path d="M86 138 L100 158 L114 138" fill="none" stroke="#f472b6" stroke-width="2" stroke-linejoin="round" stroke-linecap="round" />
                  <path d="M100 158 L100 203" stroke="#0891b2" stroke-opacity="0.3" stroke-width="1.4" stroke-linecap="round" />
                  <circle cx="100" cy="166" r="1.9" fill="#ffd93d" />
                  <circle cx="100" cy="180" r="1.9" fill="#ffd93d" />
                  <circle cx="100" cy="194" r="1.9" fill="#ffd93d" />
                  <!-- jahitan kelim -->
                  <path d="M61 199 Q100 206 139 199" fill="none" stroke="#fff" stroke-opacity="0.45" stroke-width="1.4" stroke-dasharray="3 3" stroke-linecap="round" />

                  <!-- Lengan kanan: lurus ke bawah (cerminan persis lengan kiri saat diam) -->
                  <g class="arm-idle" style="transform-origin: 134px 142px;">
                    <path d="M134 142 L147 196" fill="none" stroke="url(#av-skin)" stroke-width="10" stroke-linecap="round" />
                    <use href="#av-sleeve" transform="translate(200 0) scale(-1 1) rotate(13.5 66 142)" />
                    <g transform="translate(147 196)">
                      <ellipse cx="0" cy="0" rx="6.3" ry="6.8" fill="#f5c8a0" />
                      <ellipse cx="-5.6" cy="1.5" rx="2.4" ry="4.6" transform="rotate(25 -5.6 1.5)" fill="#f5c8a0" />
                    </g>
                  </g>

                  <!-- Leher + bayangan dagu -->
                  <path d="M90 122 Q90 138 100 140 Q110 138 110 122 Z" fill="url(#av-skin)" />
                  <path d="M90 126 Q100 140 110 126 Q100 134 90 126 Z" fill="#d89a70" opacity="0.55" />

                  <g class="head">
                    <!-- Telinga -->
                    <ellipse cx="52" cy="98" rx="5" ry="8" fill="url(#av-skin)" />
                    <ellipse cx="148" cy="98" rx="5" ry="8" fill="url(#av-skin)" />
                    <path d="M51 93 Q49 98 51 103 M149 93 Q151 98 149 103" stroke="#d99a6e" stroke-opacity="0.6" stroke-width="1.2" fill="none" stroke-linecap="round" />

                    <!-- Wajah oval halus -->
                    <path
                      d="M100 38
                         Q141 38 147 82
                         Q149 112 137 132
                         Q121 148 100 148
                         Q79 148 63 132
                         Q51 112 53 82
                         Q59 38 100 38 Z"
                      fill="url(#av-skin)"
                    />

                    <ellipse cx="100" cy="64" rx="24" ry="9" fill="#fff" opacity="0.12" />

                    <!-- Rambut depan: poni samping -->
                    <path
                      class="hair-sway"
                      d="M50 90
                         C44 44 68 24 100 24
                         C132 24 156 44 150 90
                         C150 78 146 66 140 56
                         C128 62 106 58 88 44
                         C84 54 70 60 60 68
                         C55 74 52 82 50 90 Z"
                      fill="url(#av-hair)"
                    />
                    <!-- kilau utama mengikuti lengkung poni -->
                    <path
                      class="hair-sway"
                      d="M62 46 C80 30 112 30 134 44"
                      stroke="#fff"
                      stroke-opacity="0.28"
                      stroke-width="3.2"
                      fill="none"
                      stroke-linecap="round"
                    />
                    <!-- helai poni -->
                    <path
                      class="hair-sway"
                      d="M92 30 C100 38 110 46 128 54 M100 27 C112 34 124 42 142 50"
                      stroke="#fff"
                      stroke-opacity="0.1"
                      stroke-width="1.6"
                      fill="none"
                      stroke-linecap="round"
                    />
                    <path
                      class="hair-sway"
                      d="M88 44 C90 36 90 30 88 26 M74 56 C76 46 76 38 74 32"
                      stroke="#1a0f0b"
                      stroke-opacity="0.25"
                      stroke-width="1.4"
                      fill="none"
                      stroke-linecap="round"
                    />

                    <!-- Jepit rambut -->
                    <g class="hair-sway">
                      <g transform="translate(3 -10)">
                        <path d="M68 52 L70 57 L75.5 57.4 L71.3 60.8 L72.8 66 L68 63 L63.2 66 L64.7 60.8 L60.5 57.4 L66 57 Z" fill="#f472b6" />
                        <path d="M68 55 L69 58 L66.5 58" stroke="#fff" stroke-opacity="0.7" stroke-width="1" fill="none" stroke-linecap="round" stroke-linejoin="round" />
                      </g>
                    </g>

                    <!-- Alis: terangkat sedikit saat melambai -->
                    <path class="brow" d="M72 72 Q80 67 89 71" stroke="#3a2620" stroke-width="2.5" fill="none" stroke-linecap="round" />
                    <path class="brow" d="M111 71 Q120 67 128 72" stroke="#3a2620" stroke-width="2.5" fill="none" stroke-linecap="round" />

                    <!-- Mata kiri -->
                    <g class="eye-blink" style="transform-origin: 80px 91px;">
                      <g clip-path="url(#av-eye-l)">
                        <path d="M70 90 Q80 80 90 92 Q80 99 70 90 Z" fill="#fff" />
                        <g class="pupil">
                          <circle cx="80.5" cy="90.5" r="5.4" fill="#6b4532" />
                          <circle cx="80.5" cy="90.5" r="3.6" fill="#3a2620" />
                          <circle cx="80.5" cy="90.5" r="2" fill="#120a08" />
                          <circle cx="82.6" cy="88.2" r="1.3" fill="#fff" />
                          <circle cx="78.6" cy="93" r="0.7" fill="#fff" opacity="0.6" />
                        </g>
                        <path d="M70 90 Q80 80 90 92 L90 86 L70 86 Z" fill="#000" opacity="0.1" />
                      </g>
                      <path d="M69.5 90 Q80 79.5 90.5 92" stroke="#2a1a15" stroke-width="2.4" fill="none" stroke-linecap="round" />
                      <path d="M72 93.5 Q80 98.5 88 93.5" stroke="#b9774f" stroke-opacity="0.5" stroke-width="1" fill="none" stroke-linecap="round" />
                    </g>

                    <!-- Mata kanan -->
                    <g class="eye-blink" style="transform-origin: 120px 91px;">
                      <g clip-path="url(#av-eye-r)">
                        <path d="M110 92 Q120 80 130 90 Q120 99 110 92 Z" fill="#fff" />
                        <g class="pupil">
                          <circle cx="120.5" cy="90.5" r="5.4" fill="#6b4532" />
                          <circle cx="120.5" cy="90.5" r="3.6" fill="#3a2620" />
                          <circle cx="120.5" cy="90.5" r="2" fill="#120a08" />
                          <circle cx="122.6" cy="88.2" r="1.3" fill="#fff" />
                          <circle cx="118.6" cy="93" r="0.7" fill="#fff" opacity="0.6" />
                        </g>
                        <path d="M110 92 Q120 80 130 90 L110 86 Z" fill="#000" opacity="0.1" />
                      </g>
                      <path d="M109.5 92 Q120 79.5 130.5 90" stroke="#2a1a15" stroke-width="2.4" fill="none" stroke-linecap="round" />
                      <path d="M112 93.5 Q120 98.5 128 93.5" stroke="#b9774f" stroke-opacity="0.5" stroke-width="1" fill="none" stroke-linecap="round" />
                    </g>

                    <!-- lipatan kelopak -->
                    <path d="M72 82.5 Q80 76.5 89 82" stroke="#3a2620" stroke-opacity="0.3" stroke-width="1.2" fill="none" stroke-linecap="round" />
                    <path d="M111 82 Q120 76.5 128 82.5" stroke="#3a2620" stroke-opacity="0.3" stroke-width="1.2" fill="none" stroke-linecap="round" />
                    <!-- bulu mata ujung luar -->
                    <path d="M70 90 L66.5 87.5 M130 90 L133.5 87.5" stroke="#2a1a15" stroke-width="1.8" stroke-linecap="round" />

                    <!-- Kacamata bulat -->
                    <g class="glasses">
                      <circle cx="80" cy="91" r="16" fill="#fff" fill-opacity="0.12" stroke="#1a1a1a" stroke-width="3.5" />
                      <circle cx="120" cy="91" r="16" fill="#fff" fill-opacity="0.12" stroke="#1a1a1a" stroke-width="3.5" />
                      <g clip-path="url(#av-lens-l)"><polygon class="glint" points="62,110 68,72 72,72 66,110" fill="#fff" /></g>
                      <g clip-path="url(#av-lens-r)"><polygon class="glint" points="102,110 108,72 112,72 106,110" fill="#fff" /></g>
                      <path d="M69 84 Q72 79 78 77" stroke="#fff" stroke-opacity="0.75" stroke-width="2" fill="none" stroke-linecap="round" />
                      <path d="M109 84 Q112 79 118 77" stroke="#fff" stroke-opacity="0.75" stroke-width="2" fill="none" stroke-linecap="round" />
                      <path d="M96 89 Q100 85 104 89" stroke="#1a1a1a" stroke-width="3" fill="none" />
                      <path d="M64 87 L52 83" stroke="#1a1a1a" stroke-width="3" stroke-linecap="round" />
                      <path d="M136 87 L148 83" stroke="#1a1a1a" stroke-width="3" stroke-linecap="round" />
                    </g>

                    <!-- Anting -->
                    <circle cx="53" cy="100" r="2.3" fill="#ffd93d" />
                    <circle cx="53" cy="99.3" r="0.8" fill="#fff" opacity="0.8" />
                    <circle cx="147" cy="100" r="2.3" fill="#ffd93d" />
                    <circle cx="147" cy="99.3" r="0.8" fill="#fff" opacity="0.8" />

                    <!-- Hidung tipis -->
                    <path d="M98 100 Q97 106 100 108" stroke="#d99a6e" stroke-width="1.5" fill="none" stroke-linecap="round" />

                    <ellipse cx="100" cy="109.5" rx="5" ry="2" fill="#d99a6e" opacity="0.35" />
                    <circle cx="97" cy="109.5" r="0.9" fill="#b9774f" opacity="0.7" />
                    <circle cx="103" cy="109.5" r="0.9" fill="#b9774f" opacity="0.7" />

                    <!-- Pipi blush -->
                    <ellipse class="blush" cx="68" cy="111" rx="8" ry="5" fill="#ff8fab" />
                    <ellipse class="blush" cx="132" cy="111" rx="8" ry="5" fill="#ff8fab" />

                    <!-- Senyum -->
                    <g class="smile" style="transform-origin: 100px 116px;">
                      <ellipse cx="100" cy="128" rx="9" ry="3" fill="#e07f7f" opacity="0.5" />
                      <g clip-path="url(#av-mouth)">
                        <rect x="80" y="110" width="40" height="30" fill="#9b3a3a" />
                        <ellipse cx="100" cy="127.5" rx="8" ry="3.5" fill="#e57373" />
                        <path d="M80 110 H120 V119.5 Q100 124 80 119.5 Z" fill="#fff" />
                        <path d="M80 119.5 Q100 124 120 119.5" stroke="#000" stroke-opacity="0.08" stroke-width="1.2" fill="none" />
                      </g>
                      <path d="M84 116 Q100 135 116 116" stroke="#c2645a" stroke-opacity="0.85" stroke-width="1.6" fill="none" stroke-linecap="round" />
                      <path d="M84 116 Q100 122 116 116" stroke="#c2645a" stroke-width="2.3" fill="none" stroke-linecap="round" />
                      <path d="M81.5 112.5 Q79 116.5 82 120 M118.5 112.5 Q121 116.5 118 120" stroke="#c2645a" stroke-opacity="0.45" stroke-width="1.3" fill="none" stroke-linecap="round" />
                    </g>
                  </g>

                  <!-- Garis lambaian: di samping tangan, muncul hanya saat melambai -->
                  <g class="wave-lines" transform="translate(-12 28)" fill="none" stroke="#f472b6" stroke-width="2.5" stroke-linecap="round">
                    <path d="M22 78 Q16 88 22 98" />
                    <path d="M14 72 Q4 88 14 104" />
                  </g>

                  <!-- Lengan kiri (melambai): lengan atas berputar dari bahu, lengan bawah
                       berputar dari siku, tangan berputar dari pergelangan, jari bergetar sendiri.
                       Digambar SETELAH kepala supaya selalu tampil di depan rambut dan wajah. -->
                  <g class="wave-shoulder" style="transform-origin: 66px 142px;">
                    <!-- lengan atas: bahu -> siku -->
                    <path d="M66 142 L59.5 169" fill="none" stroke="url(#av-skin)" stroke-width="10" stroke-linecap="round" />

                    <!-- lengan bawah + tangan: berputar dari siku -->
                    <g class="wave-elbow" style="transform-origin: 59.5px 169px;">
                      <path d="M59.5 169 L53 196" fill="none" stroke="url(#av-skin)" stroke-width="10" stroke-linecap="round" />

                      <!-- Tangan di ujung lengan, jari searah lengan -->
                      <g transform="translate(53 196) rotate(193.5)">
                        <g class="wave-hand">
                          <g class="wave-fingers" fill="none" stroke="#f5c8a0" stroke-width="3.4" stroke-linecap="round">
                            <path class="finger f1" style="transform-origin: -4.6px -3px;" d="M-4.6 -3 L-6.6 -12" />
                            <path class="finger f2" style="transform-origin: -1.6px -5px;" d="M-1.6 -5 L-2.2 -14.5" />
                            <path class="finger f3" style="transform-origin: 1.6px -5px;" d="M1.6 -5 L2.2 -14.5" />
                            <path class="finger f4" style="transform-origin: 4.6px -3.5px;" d="M4.6 -3.5 L6.8 -11.5" />
                          </g>
                          <ellipse cx="0" cy="0" rx="6.3" ry="6.8" fill="#f5c8a0" />
                          <ellipse cx="-5.6" cy="-1.5" rx="2.4" ry="4.6" transform="rotate(-38.5 -5.6 -1.5)" fill="#f5c8a0" />
                        </g>
                      </g>
                    </g>

                    <!-- lengan baju menutup sambungan bahu-siku -->
                    <use href="#av-sleeve" transform="rotate(13.5 66 142)" />
                  </g>
                </g>
              </svg>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* ===== WRAPPER: berjalan masuk dari kiri =====
   Kecepatan KONSTAN selama berjalan (0-23%) supaya sapuan kaki yang menapak
   cocok dengan laju badan (kaki tidak "meluncur"), lalu mengerem halus saat tiba (28%).
   Siklus langkah: 6 langkah dalam 0-28%, satu langkah = 4.667%. */
.walk-in-wrapper {
  animation: walkIn 10s linear infinite;
  will-change: transform;
  backface-visibility: hidden;
}
@keyframes walkIn {
  0%      { transform: translate3d(-80%, 0, 0); }
  23.333% { transform: translate3d(-9%, 0, 0); animation-timing-function: cubic-bezier(0.2, 0.6, 0.4, 1); }
  28%     { transform: translate3d(0, 0, 0); }
  100%    { transform: translate3d(0, 0, 0); }
}

/* Badan turun sedikit saat kedua kaki terentang (titik terendah),
   dan kembali tegak saat kaki bersilang. Tidak pernah terangkat di atas lantai. */
.walk-bob {
  animation: walkBob 10s ease-in-out infinite;
}
@keyframes walkBob {
  0%, 4.667%, 9.333%, 14%, 18.667%, 23.333%, 28%, 100% { transform: translate3d(0, 0, 0); }
  2.333%, 7%, 11.667%, 16.333%, 21%, 25.667% { transform: translate3d(0, 3px, 0); }
}

/* Bernapas: badan memanjang sedikit dari titik kaki, jadi kaki tetap menapak (tidak melayang) */
.avatar-float {
  transform-origin: 50% 93%;
  animation: breathe 4s ease-in-out infinite;
}
@keyframes breathe {
  0%, 100% { transform: scale(1, 1); }
  50% { transform: scale(1.004, 1.014); }
}

/* ===== KAKI: siklus 9.333% (2 langkah), fase berlawanan antara kiri & kanan.
   Sudut naik = kaki menapak (menyapu ke belakang badan), sudut turun = kaki
   mengayun ke depan dan DIANGKAT sedikit (translateY) agar tidak menggesek lantai. ===== */
.leg-left {
  animation: walkLeftLeg 10s linear infinite;
}
@keyframes walkLeftLeg {
  0%      { transform: translateY(0) rotate(2deg); }
  2.333%  { transform: translateY(0) rotate(20deg); }
  4.667%  { transform: translateY(-4px) rotate(2deg); }
  7%      { transform: translateY(0) rotate(-16deg); }
  9.333%  { transform: translateY(0) rotate(2deg); }
  11.667% { transform: translateY(0) rotate(20deg); }
  14%     { transform: translateY(-4px) rotate(2deg); }
  16.333% { transform: translateY(0) rotate(-16deg); }
  18.667% { transform: translateY(0) rotate(2deg); }
  21%     { transform: translateY(0) rotate(20deg); }
  23.333% { transform: translateY(-4px) rotate(2deg); }
  25.667% { transform: translateY(0) rotate(-16deg); }
  28%, 100% { transform: translateY(0) rotate(0deg); }
}

.leg-right {
  animation: walkRightLeg 10s linear infinite;
}
@keyframes walkRightLeg {
  0%      { transform: translateY(0) rotate(2deg); }
  2.333%  { transform: translateY(0) rotate(-16deg); }
  4.667%  { transform: translateY(0) rotate(2deg); }
  7%      { transform: translateY(0) rotate(20deg); }
  9.333%  { transform: translateY(-4px) rotate(2deg); }
  11.667% { transform: translateY(0) rotate(-16deg); }
  14%     { transform: translateY(0) rotate(2deg); }
  16.333% { transform: translateY(0) rotate(20deg); }
  18.667% { transform: translateY(-4px) rotate(2deg); }
  21%     { transform: translateY(0) rotate(-16deg); }
  23.333% { transform: translateY(0) rotate(2deg); }
  25.667% { transform: translateY(0) rotate(20deg); }
  28%, 100% { transform: translateY(0) rotate(0deg); }
}

/* ===== LENGAN MELAMBAI (3 sendi: bahu, siku, pergelangan) =====
   Bahu mengangkat lengan ke samping, siku menjadi penggerak utama lambaian,
   pergelangan menyusul dengan jeda, dan tiap jari bergetar sendiri.
   Hanya memakai transform dan opacity, jadi mulus juga di Safari. */

/* Bahu: ayun tipis saat berjalan, lalu lengan atas terangkat ke samping
   (hampir mendatar) dan sedikit naik-turun mengikuti irama lambaian */
.wave-shoulder {
  animation: waveShoulder 10s ease-in-out infinite;
}
@keyframes waveShoulder {
  0%      { transform: rotate(0deg); }
  2.333%  { transform: rotate(-6deg); }
  7%      { transform: rotate(6deg); }
  11.667% { transform: rotate(-6deg); }
  16.333% { transform: rotate(6deg); }
  21%     { transform: rotate(-6deg); }
  25.667% { transform: rotate(6deg); }
  28%, 34% { transform: rotate(0deg); }

  42%  { transform: rotate(64deg); }
  50%  { transform: rotate(58deg); }
  58%  { transform: rotate(66deg); }
  66%  { transform: rotate(58deg); }
  74%  { transform: rotate(64deg); }

  84%, 100% { transform: rotate(0deg); }
}

/* Siku: lengan bawah ditekuk ke atas, lalu mengayun kiri-kanan
   dengan amplitudo yang makin kecil menjelang selesai */
.wave-elbow {
  animation: waveElbow 10s ease-in-out infinite;
}
@keyframes waveElbow {
  0%, 34% { transform: rotate(0deg); }
  42%  { transform: rotate(82deg); }
  46%  { transform: rotate(104deg); }
  50%  { transform: rotate(64deg); }
  54%  { transform: rotate(106deg); }
  58%  { transform: rotate(66deg); }
  62%  { transform: rotate(102deg); }
  66%  { transform: rotate(70deg); }
  70%  { transform: rotate(96deg); }
  74%  { transform: rotate(82deg); }
  78%  { transform: rotate(82deg); }
  84%, 100% { transform: rotate(0deg); }
}

/* Pergelangan: mengibas dengan jeda (puncaknya sedikit setelah puncak siku),
   sehingga tangan terasa lemas dan mengikuti, tidak kaku */
.wave-hand {
  animation: waveWrist 10s ease-in-out infinite;
}
@keyframes waveWrist {
  0%, 42%, 78%, 100% { transform: rotate(0deg); }
  47% { transform: rotate(16deg); }
  51% { transform: rotate(-18deg); }
  55% { transform: rotate(18deg); }
  59% { transform: rotate(-18deg); }
  63% { transform: rotate(16deg); }
  67% { transform: rotate(-14deg); }
  71% { transform: rotate(12deg); }
  75% { transform: rotate(-4deg); }
}

/* Jari: muncul saat melambai, tiap jari bergetar sendiri dengan jeda berbeda */
.wave-fingers {
  opacity: 0;
  animation: fingersShow 10s ease-in-out infinite;
}
@keyframes fingersShow {
  0%, 38%, 84%, 100% { opacity: 0; }
  42%, 78% { opacity: 1; }
}
.finger {
  animation: fingerFlutter 0.9s ease-in-out infinite alternate;
}
.f1 { animation-delay: -0.00s; }
.f2 { animation-delay: -0.22s; }
.f3 { animation-delay: -0.45s; }
.f4 { animation-delay: -0.67s; }
@keyframes fingerFlutter {
  from { transform: rotate(-8deg); }
  to   { transform: rotate(8deg); }
}

/* Garis lambaian berkedip selaras dengan goyangan tangan; tersembunyi di luar fase itu */
.wave-lines {
  opacity: 0;
  animation: waveLines 10s ease-in-out infinite;
}
@keyframes waveLines {
  0%, 43% { opacity: 0; }
  47%, 52% { opacity: 1; }
  56% { opacity: 0.25; }
  60%, 64% { opacity: 1; }
  68% { opacity: 0.25; }
  72%, 76% { opacity: 1; }
  80%, 100% { opacity: 0; }
}

/* Balon sapaan selaras dengan fase melambai (42-77%) */
.hello-bubble {
  opacity: 0;
  transform-origin: 85% 100%;
  will-change: transform, opacity;
  animation: helloBubble 10s ease-in-out infinite;
}
@keyframes helloBubble {
  0%, 42% { opacity: 0; transform: translateY(6px) scale(0.85); }
  47%, 76% { opacity: 1; transform: translateY(0) scale(1); }
  82%, 100% { opacity: 0; transform: translateY(-4px) scale(0.95); }
}

/* Chip dekoratif: muncul setelah karakter tiba, lalu bergoyang pelan */
.chip {
  will-change: transform, opacity;
  animation: chipIn 0.6s ease-out 3s backwards, chipBob 4s ease-in-out infinite;
}
.chip-b {
  animation-delay: 3.3s, 1s;
}
@keyframes chipIn {
  from { opacity: 0; }
}
@keyframes chipBob {
  0%, 100% { transform: translateY(0) rotate(-3deg); }
  50% { transform: translateY(-7px) rotate(3deg); }
}

/* Ekspresi: alis naik & pipi memerah selama melambai (42-77%) */
.brow {
  animation: browRaise 10s ease-in-out infinite;
}
@keyframes browRaise {
  0%, 40% { transform: translateY(0); }
  46%, 76% { transform: translateY(-2.5px); }
  82%, 100% { transform: translateY(0); }
}
.blush {
  opacity: 0.4;
  animation: blushGlow 10s ease-in-out infinite;
}
@keyframes blushGlow {
  0%, 40% { opacity: 0.4; }
  46%, 76% { opacity: 0.7; }
  82%, 100% { opacity: 0.4; }
}

/* Postur: torso tegak dan simetris (goyang tipis ke kiri-kanan sama besar); kepala tetap sedikit miring */
.torso {
  transform-origin: 100px 205px;
  animation: torsoSway 6s ease-in-out infinite;
}
@keyframes torsoSway {
  0%, 100% { transform: rotate(-0.5deg) translateY(0); }
  50% { transform: rotate(0.5deg) translateY(-0.8px); }
}
.head {
  transform-origin: 100px 138px;
  transform: rotate(-4deg);
  animation: headTilt 10s ease-in-out infinite;
}
@keyframes headTilt {
  0%      { transform: rotate(-4deg); }
  2.333%  { transform: rotate(-2.5deg); }
  7%      { transform: rotate(-5.5deg); }
  11.667% { transform: rotate(-2.5deg); }
  16.333% { transform: rotate(-5.5deg); }
  21%     { transform: rotate(-2.5deg); }
  25.667% { transform: rotate(-5.5deg); }
  28%     { transform: rotate(-4deg); }
  31%   { transform: rotate(-1.5deg) translateY(1.2px); } /* mengangguk menyapa saat tiba */
  36%   { transform: rotate(-4deg); }
  46%, 76% { transform: rotate(-7deg); }
  84%   { transform: rotate(-4deg); }
  92%   { transform: rotate(-2.5deg); }
  100%  { transform: rotate(-4deg); }
}

/* Pupil melirik ke arah tangan (kiri penonton) selama melambai */
.pupil {
  animation: lookAtHand 10s ease-in-out infinite;
}
@keyframes lookAtHand {
  0%, 14% { transform: translateX(1.2px); }
  26%, 40% { transform: translateX(0); }
  46%, 76% { transform: translateX(-1.6px); }
  84%, 88% { transform: translateX(0); }
  92%, 96% { transform: translateX(0.9px); }
  100% { transform: translateX(1.2px); }
}

/* Kilap melintas di lensa sekali per siklus, tepat setelah karakter tiba (28%) */
.glint {
  opacity: 0;
  animation: glint 10s ease-in-out infinite;
}
@keyframes glint {
  0%, 28% { transform: translateX(-2px); opacity: 0; }
  30% { opacity: 0.7; }
  36%, 100% { transform: translateX(36px); opacity: 0; }
}

/* Awan di luar jendela bergeser sangat pelan */
.cloud {
  animation: cloudDrift 24s ease-in-out infinite alternate;
}
.cloud-b {
  animation-duration: 32s;
  animation-direction: alternate-reverse;
}
@keyframes cloudDrift {
  from { transform: translateX(-3px); }
  to   { transform: translateX(9px); }
}

/* Daun bergoyang sangat pelan */
.plant-leaves {
  animation: leafSway 5s ease-in-out infinite;
}
@keyframes leafSway {
  0%, 100% { transform: rotate(-2deg); }
  50% { transform: rotate(2.5deg); }
}

.hair-back {
  animation: swayBack 6s ease-in-out infinite;
  transform-origin: 100px 60px;
}
@keyframes swayBack {
  0%, 100% { transform: rotate(-1.4deg); }
  50% { transform: rotate(1.6deg); }
}

/* Lengan di pinggang ikut bernapas sedikit */
.arm-idle {
  animation: armIdle 6s ease-in-out infinite;
}
@keyframes armIdle {
  0%, 100% { transform: rotate(0deg); }
  50% { transform: rotate(-1.4deg); }
}

.hair-sway {
  animation: sway 5s ease-in-out infinite;
  transform-origin: 100px 40px;
}
@keyframes sway {
  0%, 100% { transform: rotate(0deg); }
  50% { transform: rotate(1deg); }
}

/* Senyum: sedikit mengembang saat melambai dan saat menyapa, kembali kalem setelahnya */
.smile {
  animation: smileLive 10s ease-in-out infinite;
}
@keyframes smileLive {
  0%, 28% { transform: scale(1, 1); }
  31% { transform: scale(1.05, 1.08); }
  36%, 40% { transform: scale(1, 1); }
  46%, 76% { transform: scale(1.07, 1.12); }
  84%, 100% { transform: scale(1, 1); }
}

.eye-blink {
  animation: blink 4.5s ease-in-out infinite;
}
@keyframes blink {
  0%, 90%, 100% { transform: scaleY(1); }
  93%, 96% { transform: scaleY(0.1); }
}

/* Hormati preferensi "kurangi gerakan": karakter diam, tangan tegap di samping badan */
@media (prefers-reduced-motion: reduce) {
  .walk-in-wrapper,
  .walk-bob,
  .avatar-float,
  .leg-left,
  .leg-right,
  .wave-shoulder,
  .wave-elbow,
  .wave-hand,
  .wave-fingers,
  .finger,
  .wave-lines,
  .hair-sway,
  .smile,
  .eye-blink,
  .hello-bubble,
  .brow,
  .blush,
  .pupil,
  .head,
  .torso,
  .hair-back,
  .arm-idle,
  .glint,
  .plant-leaves,
  .cloud,
  .chip {
    animation: none;
  }
}
</style>