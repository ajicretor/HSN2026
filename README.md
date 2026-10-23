# MWC NU Kecamatan Citeureup

Situs resmi **MWC NU Kecamatan Citeureup** — *Mengabdi untuk Umat, Berkhidmat untuk Bangsa, Citeureup di Hareup.*

Alamat: **https://nuciteureup.online/**

Situs ini memuat profil dan kegiatan MWC NU, struktur dan tokoh pengurus, rangkaian **Hari Santri Nasional (HSN) 2026**, serta alat bantu panitia HSN: verifikasi sertifikat lomba, verifikasi donasi, dan monitoring progres kepanitiaan.

---

## Isi repositori

| File | Fungsi |
|---|---|
| `index.html` | Beranda: Tentang, Kegiatan, HSN, Struktur, Tokoh, dan pintu masuk verifikasi |
| `verifikasi.html` | Verifikasi keaslian **sertifikat lomba** (kode `HSN-XXXX-XXXX`), tampilan sertifikat, unduh PDF/PNG, cetak |
| `donasi.html` | Verifikasi **donatur** dan sertifikat donasi, unduh PDF/PNG, cetak |
| `monitoring.html` | Dasbor progres seksi kepanitiaan HSN 2026 (baca publik, ubah dengan kode panitia) |
| `vercel.json` | Header keamanan dan pengalihan alamat di Vercel |
| `logo.png` | Tidak dipakai lagi. Semua logo sudah ditanam di dalam HTML, aman dihapus |

Setiap halaman bersifat **mandiri (single file)**: gaya, skrip, logo, foto, dan ikon ditanam langsung di dalam HTML. Tidak ada proses *build* dan tidak ada dependensi yang perlu dipasang.

## Teknologi

- HTML, CSS, dan JavaScript murni (tanpa framework)
- **Google Sheets + Google Apps Script** sebagai sumber data (verifikasi dan monitoring)
- **Vercel** sebagai hosting, dengan domain `nuciteureup.online`
- Font dari Google Fonts: **Plus Jakarta Sans** (isi teks) dan **Cinzel** (judul), serta Playfair Display italik untuk nama di sertifikat

## Cara kerja singkat

**Verifikasi sertifikat dan donasi**
1. Pengunjung memasukkan kode atau memindai QR di sertifikat. QR membuka `verifikasi.html?kode=...` atau `donasi.html?kode=...` dengan kode terisi otomatis.
2. Halaman memanggil Apps Script (`action=verify` atau `action=verifyDonatur`) dan mencocokkan kode dengan arsip panitia.
3. Jika cocok, halaman menampilkan data penerima dan menggambar sertifikat dari template. Unduh PNG, PDF, dan cetak dibuat di sisi browser lewat canvas, tanpa server.

**Monitoring**
- Data seksi dan tugas dibaca dari Google Sheets dan diperbarui otomatis tiap 30 detik.
- Perubahan (tambah, hapus, centang tugas, ubah nama koordinator) hanya diterima jika disertai **kode panitia**.

## Konfigurasi

Alamat Web App Apps Script ada di konstanta `API` pada:

- `verifikasi.html`
- `donasi.html`
- `monitoring.html`

Jika Apps Script dideploy ulang dan alamatnya berubah, perbarui konstanta ini di ketiga file. Alamat tujuan verifikasi di beranda diatur oleh `VERIFY_SERTIFIKAT`, `VERIFY_DONASI`, dan `ALLOWED_HOST` di `index.html` (nilai `ALLOWED_HOST` berupa nama host saja, tanpa `https://` dan tanpa `www`).

> Jangan menaruh kode panitia atau kunci rahasia di repositori ini. Kode panitia hanya diketik oleh pengurus di browser dan divalidasi oleh Apps Script.

## Menjalankan secara lokal

Buka langsung `index.html` di browser, atau jalankan server statis sederhana:

```bash
python3 -m http.server 8000
# lalu buka http://localhost:8000
```

Fitur yang bergantung pada Apps Script (verifikasi dan monitoring) tetap memanggil layanan asli, jadi butuh koneksi internet.

## Deploy

1. Unggah atau *commit* perubahan ke cabang `main`. Vercel otomatis membangun dan menayangkan.
2. Tunggu status deploy selesai, lalu muat ulang paksa di browser (Ctrl+F5).

### Domain dan pengalihan

- Domain utama: `nuciteureup.online` (tanpa `www`).
- Di Vercel (**Project → Settings → Domains**): jadikan `nuciteureup.online` domain utama, lalu arahkan `www.nuciteureup.online` ke `nuciteureup.online` (308).
- `vercel.json` mengalihkan `hsn2026.vercel.app` ke `nuciteureup.online`, dan halaman HTML juga mengalihkan alamat `*.vercel.app` sebagai cadangan.
- Jangan membuat dua aturan yang saling berlawanan (misalnya tanpa `www` ke `www` di dashboard, sementara kode mengarahkan sebaliknya). Itu menyebabkan `ERR_TOO_MANY_REDIRECTS`.

### Header keamanan

`vercel.json` menetapkan `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`, dan `Strict-Transport-Security` untuk semua halaman. `index.html` juga memuat Content-Security-Policy; jika menambah sumber luar (font, skrip, gambar), sesuaikan kebijakan itu.

## Memperbarui konten

- **Menu dan bagian halaman:** tiap bagian di `index.html` berupa `<section id="...">` (`tentang`, `kegiatan`, `hsn`, `struktur`, `tokoh`, `verifikasi`). Menu atas mengikuti `id` ini.
- **Kegiatan:** kartu berfoto memakai atribut `data-gal`. Foto dan keterangannya adalah elemen `<img data-full data-cap="...">` di dalam kartu, dan dibuka lewat galeri layar penuh.
- **Tokoh dan struktur:** ubah kartu di bagian `#tokoh` dan daftar di `#struktur`. Foto memakai gambar persegi yang dipotong bulat; ukuran sekitar 200 px sudah cukup.
- **Foto:** karena semua file mandiri, foto ditanam sebagai data URI (base64). Perkecil dan kompres foto sebelum ditanam agar halaman tetap ringan.
- **Font sertifikat:** font di dalam sertifikat disesuaikan dengan template gambar. Mengubahnya dapat menggeser posisi teks di PNG, PDF, dan hasil cetak.

## Tampilan di perangkat

Halaman dirancang responsif untuk desktop, tablet, Android, dan iPhone: menu hamburger di layar kecil, ukuran huruf dan kotak menyesuaikan, area aman notch iPhone dihormati, dan sertifikat dapat diketuk untuk diperbesar.

## Lisensi

MIT License. © 2026 Aji.
