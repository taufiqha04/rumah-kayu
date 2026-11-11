# Ruang Kayu — Prototype Konfigurator Furniture 3D

Prototype website konfigurator furniture custom berbasis HTML, CSS, dan JavaScript dalam satu file. Pelanggan dapat mengubah ukuran, material, warna, finishing, dan komponen; melihat hasilnya langsung dalam 3D; membuka pintu dan menarik laci; lalu mengirim permintaan penawaran lengkap dengan pratinjau.

Prototype ini mengacu pada `PRD — Website Konfigurator Furniture 3D Custom.md`, dengan lingkup yang dipersempit ke halaman konfigurator saja.

---

## Menjalankan

Prototype memerlukan WebGL 2 dan koneksi internet (Three.js dan Google Fonts dimuat dari CDN).

```bash
cd "/home/padikering/Documents/Demo/website 3D"
python3 -m http.server 8000
```

Buka `http://localhost:8000`.

Server lokal diperlukan agar modul ES, penyimpanan `localStorage`, dan pembaruan hash URL berjalan normal. Membuka `index.html` langsung lewat `file://` tidak disarankan.

---

## Fitur

### Konfigurasi produk

Tiga model parametrik:

| Model | Tingkat | Rentang ukuran (cm) | Komponen |
| --- | --- | --- | --- |
| Meja Makan Rangga | Dasar | L 120–240 · T 68–78 · D 70–100 | tebal daun meja, bentuk kaki, material |
| Lemari Pakaian Arun | Menengah | L 80–240 · T 180–240 · D 45–65 | pintu, rak, laci, model pintu, material |
| Kabinet TV Bima | Menengah | L 120–240 · T 40–70 · D 35–50 | pintu, laci, relung terbuka, rak, material |

- Ukuran memakai slider dan kolom angka yang selalu sinkron.
- Nilai di luar batas ditolak dengan pesan yang jelas; nilai tidak sesuai kelipatan dibulatkan.
- Geometri dibangun ulang dari aturan produk, bukan model yang diregangkan.
- Aturan otomatis: minimal pintu dari lebar maksimum 60 cm per daun, jarak antar rak minimal 25 cm, tinggi laci, dan ruang pintu minimum.
- Opsi yang tidak valid dinonaktifkan beserta alasannya, misalnya pintu kaca pada daun yang terlalu sempit.
- Material dan warna diatur per bagian (badan, pintu, kaki/plinth, gagang) dengan pilihan kayu, cat, dan logam.
- Finishing Matte, Satin, dan Gloss memengaruhi tingkat kilap material.

### 3D interaktif

- Orbit, zoom, dan pan dengan mouse maupun sentuhan (satu jari memutar, dua jari zoom).
- Batas zoom dan sudut kamera agar model tidak hilang.
- Tombol kembali ke sudut awal.
- **Pintu dapat dibuka dan ditutup** dengan animasi engsel. Klik pintu, klik lagi untuk menutup.
- **Laci dapat ditarik keluar dan didorong masuk**, lengkap dengan badan laci.
- Tombol **Buka semua / Tutup semua** di toolbar.
- `Esc` menutup semua pintu dan laci.
- Kursor berubah menjadi pointer saat diarahkan ke pintu atau laci.
- Klik badan, rak, atau komponen lain untuk menyorot dan melihat ukurannya.
- Garis ukur lebar, tinggi, dan kedalaman dengan label sentimeter.
- State pintu/laci tetap terbuka saat material atau ukuran diubah.

### Ringkasan dan riwayat

- Ringkasan spesifikasi lengkap diperbarui otomatis.
- Ringkasan mini selalu terlihat di atas tombol CTA.
- Undo/redo hingga 30 langkah, termasuk `Ctrl/Cmd+Z` dan `Shift+Ctrl/Cmd+Z`.
- Reset konfigurasi dengan dialog konfirmasi.

### Tautan desain

- Konfigurasi diserialisasi ke hash URL (`#d=...`).
- Tombol **Salin tautan** menyalin tautan yang dapat dibuka di peramban lain untuk memulihkan produk, ukuran, material, finishing, dan komponen.
- Tautan juga disertakan pada permintaan penawaran.

### Permintaan penawaran

- Snapshot PNG diambil otomatis dari tampilan 3D.
- Form: nama, nomor WhatsApp, kota, email (opsional), catatan.
- Validasi inline dan honeypot anti-spam sederhana.
- Permintaan disimpan ke `localStorage` (`rk_requests`) bersama spesifikasi, konfigurasi JSON, tautan desain, dan thumbnail pratinjau.
- Setelah terkirim, tampil kode permintaan, ringkasan, tautan desain, dan tombol **Buka WhatsApp** dengan pesan yang sudah terisi.
- Snapshot penawaran otomatis menutup semua pintu dan laci agar pratinjau rapi.

### Responsif dan ketahanan

- Desktop: model 3D di kiri, panel pengaturan di kanan.
- Tablet: model di atas, panel di bawah.
- Mobile: model di atas, panel berupa bottom sheet yang bisa ditarik, CTA selalu terjangkau.
- Target sentuh diperbesar pada layar kecil.
- Bila WebGL 2 tidak tersedia: tampil panel fallback, konfigurasi tetap bisa diatur, dan penawaran tetap bisa dikirim tanpa pratinjau.
- Bila CDN gagal dimuat: tampil layar error dengan tombol muat ulang.
- Preferensi `prefers-reduced-motion` dihormati.

---

## Kontrol 3D

| Aksi | Mouse | Sentuh |
| --- | --- | --- |
| Putar model | Seret | Satu jari |
| Zoom | Scroll | Cubit dua jari |
| Geser pandangan | Klik kanan seret | Dua jari |
| Buka/tutup pintu | Klik pintu | Ketuk pintu |
| Tarik/dorong laci | Klik laci | Ketuk laci |
| Pilih komponen | Klik badan/rak | Ketuk badan/rak |
| Tutup semua bukaan | `Esc` atau tombol toolbar | Tombol toolbar |
| Sudut awal | Tombol **Sudut awal** | Tombol **Sudut awal** |

---

## Struktur file

```
website 3D/
├── index.html                                   # seluruh prototype
├── README.md
└── PRD — Website Konfigurator Furniture 3D Custom.md
```

`index.html` terdiri dari:

1. **CSS** — design token, layout desktop/tablet/mobile, komponen panel, modal, dan bottom sheet.
2. **Markup** — topbar, viewport 3D, panel konfigurasi, modal penawaran, modal reset.
3. **JavaScript modul** — data produk, scene Three.js, geometri parametrik, interaksi, panel, riwayat, tautan, dan alur penawaran.

Data contoh yang bisa langsung diubah:

| Bagian | Lokasi di `index.html` |
| --- | --- |
| Nomor WhatsApp | konstanta `WA_NUMBER` |
| Material dan warna | objek `MATERIALS` |
| Finishing | konstanta `FINISHES` |
| Model, ukuran, komponen, bagian | objek `MODELS` dan `MODEL_PARTS` |
| Aturan validasi | fungsi `evaluate()` |
| Geometri tiap model | `buildMeja()`, `buildArun()`, `buildBima()` |
| Pintu dan laci dinamis | `createDoorGroup()`, `createDrawerGroup()` |
| Warna dan tipografi | variabel CSS di `:root` |
| Nama brand | topbar, `document.title`, dan `README` ini |

---

## Menambah model baru

1. Tambahkan definisi di `MODELS`: nama, rentang ukuran, bagian/material, defaults, dan komponen.
2. Tambahkan builder geometri dan daftarkan di `BUILDERS`.
3. Tambahkan aturan kombinasi di `evaluate()` dan batas komponen yang sesuai.
4. Tambahkan label bagian di `PART_LABELS` bila diperlukan.
5. Tambahkan tombol model di `#modelTabs`.
6. Jika model memiliki pintu/laci, gunakan `createDoorGroup()` dan `createDrawerGroup()` agar interaksi buka/tutup langsung berfungsi.

---

## Teknologi

- **HTML, CSS, JavaScript** — tanpa framework dan tanpa proses build.
- **Three.js r169** — WebGL renderer, OrbitControls, CSS2DRenderer, RoomEnvironment.
- **Google Fonts** — Fraunces (display) dan Manrope (UI).
- **Tekstur prosedural** — grain kayu dibuat di canvas, tanpa file gambar.
- **localStorage** — simulasi penyimpanan permintaan di sisi peramban.

Tidak ada aset 3D eksternal. Seluruh geometri dibangun dari parameter di peramban.

---

## Batasan prototype

- Tidak ada backend. Permintaan hanya tersimpan di `localStorage` peramban dan tidak dikirim ke server.
- Panel admin, katalog, beranda, halaman statis, autentikasi, dan integrasi Laravel belum termasuk.
- Mode AR, foto ruangan, estimasi harga, penyimpanan desain permanen, pembayaran, dan unduhan PDF belum termasuk.
- Drag handle langsung pada model untuk mengubah ukuran belum diimplementasikan; interaksi ukuran dilakukan lewat slider dan kolom angka.
- Nomor WhatsApp masih placeholder.
- Belum ada rate limiting atau captcha; keduanya perlu ditangani di backend.
- Konfigurasi tidak menyimpan state buka/tutup pintu di URL.
- Tautan desain bergantung pada URL halaman yang sedang dibuka.

---

## Pengujian yang sudah dilakukan

Diuji dengan Chromium headless (Playwright) pada ukuran 1440×900, 1280×800, 1024×768, 834×1112, dan 390×844, termasuk kondisi WebGL dimatikan.

- Perubahan ukuran, pembulatan, dan penolakan nilai di luar batas.
- Penyesuaian otomatis jumlah pintu, rak, dan laci.
- Opsi kaca nonaktif beserta alasannya saat daun pintu terlalu sempit.
- Buka/tutup pintu, tarik/dorong laci, buka semua, dan `Esc`.
- Perubahan material/finishing saat pintu terbuka.
- Undo/redo, reset, dan undo setelah reset.
- Pemulihan konfigurasi dari tautan desain.
- Snapshot, validasi form, penyimpanan permintaan, dan tautan WhatsApp.
- Bottom sheet mobile, tombol CTA, dan fallback tanpa WebGL.
- Tidak ada error console pada alur utama.

---

## Rujukan

- `PRD — Website Konfigurator Furniture 3D Custom.md` — kebutuhan produk, aturan, metrik, dan rencana pengembangan.
- [Three.js Documentation](https://threejs.org/docs/) — API scene, material, dan kontrol.
