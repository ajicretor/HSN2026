# MWC NU Kecamatan Citeureup

Situs resmi **MWC NU Kecamatan Citeureup** — *Mengabdi untuk Umat, Berkhidmat untuk Bangsa, Citeureup di Hareup.*

Alamat: **https://nuciteureup.online/**

Situs ini memuat profil dan kegiatan MWC NU, struktur dan tokoh pengurus, rangkaian **Hari Santri Nasional (HSN) 2026**, serta alat bantu panitia HSN: verifikasi sertifikat lomba, verifikasi donasi, dan monitoring progres kepanitiaan.

---

## Isi repositori

| File | Fungsi |
|---|---|
| `index.html` | Beranda: Tentang, Kegiatan (galeri foto), HSN, Struktur, Banom NU, Tokoh (dengan profil), dan pintu masuk verifikasi |
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
- Font dari Google Fonts: **Plus Jakarta Sans** (isi teks) dan **Poppins** (judul), serta Cinzel dan Playfair Display italik khusus di dalam sertifikat (jangan dihapus dari `verifikasi.html`)

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

- **Menu dan bagian halaman:** tiap bagian di `index.html` berupa `<section id="...">` (`tentang`, `kegiatan`, `hsn`, `struktur`, `banom`, `tokoh`, `verifikasi`). Menu atas mengikuti `id` ini.
- **Kegiatan:** setiap kegiatan adalah kartu `<article class="kg kgp" data-gal="nama">` (saat ini: Naharul Ijtima’, Musyawarah Kerja I, Lailatul Ijtima’, Ngopi NU). Isi kartu:
  - tombol `.kph` berisi foto sampul dan label jumlah foto (`.kcnt`, misalnya "4 foto"),
  - foto galeri berupa `<img hidden data-full alt="..." data-cap="...">`; `data-cap` menjadi keterangan di tampilan layar penuh,
  - judul `<h3>`, baris tanggal dan tempat `.kmeta`, dan paragraf narasi singkat.

  Untuk menambah atau mengganti foto, ubah elemen `<img data-full>` lalu sesuaikan label jumlah foto. Tanpa foto, kartu cukup memakai `<article class="kg">` dengan label `<span class="soon">Info jadwal menyusul</span>`.
- **Tokoh dan profil:** kartu tokoh ada di `#tokoh` (`<div class="tk">`). Kartu yang punya profil diberi kelas `tkp` dan bisa diklik untuk membuka jendela profil (foto, nama, jabatan, narasi). Saat ini profil tersedia untuk KH. Zaenal Abidin, KH. Muhtadin, KH. R. Roby Samsi, Duduh Abdullah Alwahdi, dan Septa Aji. Untuk menambah profil tokoh lain, di dalam kartu tambahkan:
  - `role="button" tabindex="0" aria-haspopup="dialog"` dan kelas `tkp` pada `<div class="tk">`,
  - `<span class="tkm">Lihat profil</span>`,
  - `<img hidden data-pfimg alt="..." src="...">` untuk foto di jendela profil,
  - `<div hidden class="pfd" data-name="..." data-role="..."><p>...</p></div>`; setiap `<p>` menjadi satu paragraf narasi.
- **Struktur:** ubah daftar di `#struktur`.
- **Banom NU:** bagian `#banom` berisi kartu `<div class="bnc">` (nama `<h3>`, sasaran `.bl`, deskripsi `<p>`). Tambah, hapus, atau ubah kartu sesuai Banom yang aktif di Kecamatan Citeureup. Di dasar tiap kartu ada kolom ketua (`<div class="kt kt0">`) yang masih berisi "Nama ketua menyusul". Untuk mengisinya, ganti dengan:
  ```html
  <div class="kt tkp" role="button" tabindex="0" aria-haspopup="dialog" aria-label="Lihat profil NAMA">
    <span class="av"><img alt="NAMA" src="data:image/jpeg;base64,..."></span>
    <div class="kb"><span class="ktl">Ketua PAC</span><b>NAMA</b><span class="tkm">Lihat profil</span></div>
    <img hidden data-pfimg alt="NAMA" src="data:image/jpeg;base64,...">
    <div hidden class="pfd" data-name="NAMA" data-role="Ketua PAC ... Kecamatan Citeureup"><p>Narasi profil.</p></div>
  </div>
  ```
  Profil terbuka di jendela yang sama dengan profil tokoh. Jika menambah bagian baru, daftarkan juga `id`-nya di menu atas dan di array `secs` pada skrip `index.html` agar menu aktif mengikuti posisi baca.
- **Foto:** karena semua file mandiri, foto ditanam sebagai data URI (base64). Perkecil dan kompres foto sebelum ditanam agar halaman tetap ringan, tetapi jangan terlalu kecil: foto di bawah sekitar 500 px tampak buram di tampilan layar penuh. Foto tokoh untuk kartu berbentuk persegi dan dipotong bulat (sekitar 200 px sudah cukup); foto untuk jendela profil sebaiknya potret.
- **Font sertifikat:** font di dalam sertifikat (Cinzel dan Playfair Display) disesuaikan dengan template gambar. Mengubahnya dapat menggeser posisi teks di PNG, PDF, dan hasil cetak. Font judul halaman memakai Poppins.

## Tampilan di perangkat

Halaman dirancang responsif untuk desktop, tablet, Android, dan iPhone: menu hamburger di layar kecil, ukuran huruf dan kotak menyesuaikan, area aman notch iPhone dihormati, dan sertifikat dan foto kegiatan dapat diketuk untuk diperbesar, dan profil tokoh terbuka sebagai jendela yang bisa digulir.

## Lisensi

MIT License. © 2026 Aji.
