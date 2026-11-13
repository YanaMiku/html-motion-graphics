# 🎬 HTML Motion Graphics — AI Agent Skill (`html-motion-graphics`)

> **Keterampilan (Agent Skill) untuk menghasilkan video Motion Graphics sinematik, Opener Produk, Kinetic Typography, dan Explainer dalam format `index.html` tunggal (standalone) berbasis GSAP & WebGL.**

[![Agent Skills Standard](https://img.shields.io/badge/Standard-Agent_Skills_Spec-blue.svg)](https://agentskills.io)
[![Output](https://img.shields.io/badge/Output-Single--File_index.html-emerald.svg)](#)
[![Engine](https://img.shields.io/badge/Engine-GSAP_3_%2B_SVG_Filters_%2B_Three.js-orange.svg)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## 📖 Mengapa Skill Ini Dibuat?

Ketika diminta membuat animasi atau video promosi berbasis web, AI umumnya terjebak dalam **pola presentasi PowerPoint (PPT)**: teks terlalu panjang, tata letak kartu berjejer statis, latar belakang polos yang membosankan, dan transisi fade-in/out yang kaku.

Skill **`html-motion-graphics`** mengubah cara AI Agent berpikir—dari sekadar menyusun *layout web* menjadi seorang **Motion Designer & Senior Director**:
* **Hukum Anti-PPT**: Satu ketukan (*beat*) = satu pernyataan singkat ($\le 5$ kata), bukan paragraf dokumen.
* **Unified `#world` Camera Rig**: Latar belakang dan subjek dibungkus dalam satu rig kamera terpadu (`1920x1080` atau `1080x1920` dengan *safe-area* Reels/TikTok/Shorts), sehingga *zoom*, *pan*, dan *push-through* terasa menyatu layaknya kamera film.
* **Directional Motion Blur & Grain**: Menggunakan SVG `feGaussianBlur` berarah sumbu terpisah dan tekstur *grain* deterministik agar gerakan terasa organik seperti After Effects.
* **100% Deterministik & Standalone**: Seluruh CSS dan ES Module ditulis *inline* dalam satu file `index.html` yang bisa langsung dibuka dua kali (`file://`) tanpa server lokal, bebas CORS, serta mendukung *timeline scrubbing* presisi frame untuk ekspor ke MP4.

---

## 📁 Struktur Repositori

Repositori ini dirancang ringkas (*self-contained*) sehingga seluruh instruksi inti sudah terangkum di dalam satu file `SKILL.md`:

```text
html-motion-graphics/
├── SKILL.md       # File utama instruksi skill (wajib ada di root folder skill)
├── README.md      # Dokumentasi repositori ini
└── LICENSE        # Lisensi (MIT)
```

> **Catatan:** Pastikan file utama tetap bernama **`SKILL.md`** dengan huruf kapital karena standar *Agent Skills* memindai nama file tersebut secara spesifik.

---

## 🚀 Cara Pemasangan di Gemini AI (Keterampilan / Keahlian)

Anda dapat memasang skill ini di **Gemini** (baik melalui fitur *Skills / Keterampilan* di Gemini Web/Workspace, *Gems*, maupun *Gemini CLI*).

### Metode 1: Melalui Menu Keterampilan / Keahlian (*Skills*) di Gemini Web
Jika antarmuka Gemini Anda memiliki menu **Keterampilan / Keahlian (*Skills*)**:
1. Unduh atau *clone* repositori ini ke komputer Anda (atau kompres folder `html-motion-graphics/` menjadi file `.zip` yang di dalamnya berisi `SKILL.md`).
2. Buka **Gemini** → masuk ke menu **Keterampilan / Keahlian (*Skills*)** (biasanya berada di bilah samping kiri atau menu pengaturan alat/ekstensi).
3. Klik **Buat / Unggah Keterampilan (*Upload / Import Skill*)**.
4. Pilih file `SKILL.md`, folder `html-motion-graphics`, atau file `.zip` tersebut.
5. Pastikan status keterampilan **Aktif**. Gemini akan otomatis memicu skill ini setiap kali Anda meminta pembuatan *motion graphics*, *opener*, *kinetic typography*, atau *explainer* HTML.

### Metode 2: Melalui Gemini CLI (`gemini-cli`)
Jika Anda menggunakan **Gemini CLI** di terminal:
1. *Clone* repositori ini langsung ke direktori *global skills* atau direktori proyek Anda:
   ```bash
   # Untuk pemasangan global (tersedia di semua proyek)
   mkdir -p ~/.gemini/skills
   git clone https://github.com/USERNAME_ANDA/html-motion-graphics.git ~/.gemini/skills/html-motion-graphics

   # ATAU untuk pemasangan khusus satu proyek saja
   mkdir -p .gemini/skills
   git clone https://github.com/USERNAME_ANDA/html-motion-graphics.git .gemini/skills/html-motion-graphics
   ```
2. Jalankan `gemini` di terminal, lalu ketik `/skills` untuk memastikan `html-motion-graphics` sudah terdaftar dan aktif.

### Metode 3: Menggunakan Fitur *Gems* (Alternatif Tanpa Fitur Skills)
Jika akun Gemini Anda menggunakan **Gems**:
1. Buka **Gem Manager** → klik **New Gem**.
2. Beri nama Gem: `HTML Motion Graphics Director`.
3. Salin seluruh isi file `SKILL.md` ke kolom **Instructions**.
4. Klik **Save**.

---

## 💻 Cara Penggunaan (Prompting & Workflow)

Setelah skill aktif, cukup berikan perintah desain video Anda. AI Agent akan terlebih dahulu menyusun **Style Brief & Konsep** (menentukan palet, rasio, gaya gerak, dan struktur babak) sebelum menuliskan kode `index.html`.

### Contoh Prompt Siap Pakai

* **Opener Produk SaaS (16:9):**
  > *"Buatkan motion graphic opener berdurasi 12 detik dalam format single-file `index.html` untuk peluncuran aplikasi analitik AI bernama **PulseFlow**, rasio 16:9, dengan konsep tur produk berselang klaim dan animasi UI interaktif."*

* **Video Promosi Vertikal Reels / TikTok / Shorts (9:16):**
  > *"Buatkan motion graphic promo 15 detik rasio 9:16 (safe-area sosial media) untuk brand kopi kekinian **Kopi Kinetik**. Gunakan gaya pop flat energik dengan tipografi kinetik yang ritmis."*

* **Explainer Infografik / Aksi Kontinu:**
  > *"Buatkan video explainer 30 detik format `index.html` tentang cara kerja enkripsi end-to-end dengan gaya vektor aksi kontinu tanpa caption panjang."*

### Pintasan Keyboard & Mode URL pada Hasil `index.html`

Setiap file `index.html` yang dihasilkan oleh skill ini dilengkapi kontrol bawaan untuk memudahkan pratinjau maupun proses *rendering* ke video:

| Kontrol / Parameter URL | Fungsi |
| :--- | :--- |
| **`Space`** | Play / Pause animasi |
| **`R`** | Ulangi (*Restart*) animasi dari detik `0.00` |
| **`index.html?debug=1`** | Menampilkan **Panel Scrubbing Developer** (slider waktu & penunjuk detik presisi untuk inspeksi tiap frame) |
| **`index.html?clean=1`** | Menyembunyikan seluruh UI kontrol dan mematikan autoplay (khusus untuk *headless browser* saat ekspor MP4) |

---

## 🎥 Ekspor `index.html` Menjadi Video MP4 (Opsional)

Karena seluruh animasi dibangun secara deterministik melalui `window.OPENER.seek(t)`, Anda dapat merender `index.html` menjadi file video **MP4 60fps** tanpa *frame drop* menggunakan Puppeteer/Playwright dan FFmpeg:

```bash
# Contoh penggabungan urutan frame PNG menjadi MP4 berkualitas tinggi (CRF 16)
ffmpeg -framerate 60 -i frames/f%05d.png -c:v libx264 -pix_fmt yuv420p -crf 16 output.mp4
```

---

## 🤝 Kompatibilitas Platform AI Lain

Selain Gemini AI, repositori ini mengikuti spesifikasi terbuka **Agent Skills**, sehingga dapat langsung dipasang pada:
* **Claude Code / Claude Desktop**: Salin folder ke `~/.claude/skills/html-motion-graphics/` atau `.claude/skills/`.
* **Cursor / Windsurf / Cline**: Referensikan `SKILL.md` melalui `.cursorrules` atau folder `.agents/skills/`.

---

## 📄 Lisensi

Proyek ini didistribusikan di bawah lisensi **MIT**. Silakan gunakan, modifikasi, dan kembangkan untuk kebutuhan proyek pribadi maupun komersial Anda.
