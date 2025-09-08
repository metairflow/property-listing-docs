# Dokumen Perancangan Antarmuka (Wireframe): Proyek RENTVERSE

**Versi: 1.0**
**Tanggal: 8 September 2025**
**Status: Draf Awal**

---

## 1. Pengantar

### 1.1. Tujuan Dokumen

Dokumen ini menyajikan _wireframe_ atau sketsa tata letak tingkat rendah (_low-fidelity_) untuk halaman-halaman utama aplikasi RENTVERSE. Tujuannya adalah untuk memvisualisasikan struktur, alur navigasi, dan penempatan komponen-komponen kunci sebelum masuk ke tahap desain visual (_high-fidelity_) dan pengembangan frontend. Fokus utama adalah pada fungsionalitas dan pengalaman pengguna (UX), bukan pada estetika (UI).

### 1.2. Filosofi Desain

- **Clean & Minimalist**: Antarmuka akan dirancang agar bersih, tidak berantakan, dan fokus pada tugas utama pengguna.
- **Responsif**: Semua halaman harus dapat diakses dan berfungsi dengan baik di berbagai ukuran layar, mulai dari perangkat seluler hingga desktop.
- **Intuitif**: Alur kerja harus mudah dipahami oleh pengguna tanpa memerlukan banyak instruksi.

---

## 2. Wireframe Halaman Utama

Berikut adalah sketsa untuk setiap halaman kunci dalam aplikasi.

### 2.1. Halaman Login

Halaman ini adalah gerbang masuk untuk pengguna dengan peran `AGENT` dan `ADMIN`.

```
+-------------------------------------------------------------+
|                                                             |
|                      [ Logo RENTVERSE ]                     |
|                                                             |
|                  Selamat Datang Kembali                     |
|                                                             |
|      [ Input Teks: Alamat Email ]                           |
|                                                             |
|      [ Input Teks: Password ]                               |
|                                                             |
|      [ Tombol: LOGIN ]                                      |
|                                                             |
|      Belum punya akun? [ Link: Daftar di sini ]             |
|                                                             |
+-------------------------------------------------------------+
```

### 2.2. Halaman Dasbor Agen & Formulir Pengajuan Properti

Ini adalah halaman utama bagi Agen, di mana mereka bisa langsung mengajukan properti baru.

```
+-------------------------------------------------------------+
| [ Logo RENTVERSE ]      [ Dasbor ]      [ Profil Agen ]     |
+-------------------------------------------------------------+
|                                                             |
|               Daftarkan Properti Baru |
|                                                             |
|   [ Dropdown: Pilih Proyek (Auto-fill) ▼ ]                  |
|                                                             |
|   [ Input Teks: Judul Iklan Properti ]                      |
|                                                             |
|   [ Text Area: Alamat Lengkap ]                             |
|                                                             |
|   [ Dropdown: Tipe Properti ▼ ] [ Input Angka: Luas (m²) ]  |
|                                                             |
|   [ Input Angka: Jml Kamar Tidur ] [ Input Angka: Jml Kamar Mandi ]|
|                                                             |
|   [ Dropdown: Status Perabotan ▼ ]                          |
|                                                             |
|   +-------------------------------------------------------+ |
|   | SIMULATOR HARGA (Muncul setelah data di atas diisi) | |
|   | Rekomendasi Harga: Rp 2.250.000 (Keyakinan: 85%)    | |
|   +-------------------------------------------------------+ |
|                                                             |
|   [ Input Angka: Harga Sewa yang Ditawarkan ]               |
|                                                             |
|   [ Text Area: Deskripsi Properti ]                         |
|                                                             |
|   [ Tombol: Upload Gambar ]                                 |
|   [ Pratinjau Gambar 1 ] [ Pratinjau Gambar 2 ]             |
|                                                             |
|   [           Tombol: KIRIM UNTUK PERSETUJUAN           ]   |
|                                                             |
+-------------------------------------------------------------+
```

### 2.3. Halaman Dasbor Admin

Halaman ini adalah pusat kendali bagi Admin untuk mengelola listing properti.

```
+-------------------------------------------------------------+
| [ Logo RENTVERSE ]      [ Dasbor Admin ]      [ Profil Admin ]|
+-------------------------------------------------------------+
|                                                             |
|                  Properti Menunggu Persetujuan |
|                                                             |
|   +-------------------------------------------------------+ |
|   | [Filter by Agent ▼]      [ Cari berdasarkan Nama... ] | |
|   +-------------------------------------------------------+ |
|                                                             |
|   | Properti           | Diajukan Oleh | Tanggal      | Aksi |
|   |--------------------|---------------|--------------|------|
|   | Studio Penthouse.. | Budi Santoso  | 07 Sep 2025  | [Lihat] [Setujui] [Tolak] |
|   | Rumah Modern di..  | Ana Lestari   | 07 Sep 2025  | [Lihat] [Setujui] [Tolak] |
|                                                             |
|   [ < Prev ]       [ Halaman 1 dari 5 ]       [ Next > ]    |
|                                                             |
+-------------------------------------------------------------+
```

### 2.4. Halaman Detail Properti (Tampilan Publik)

Halaman yang dilihat oleh calon penyewa setelah properti disetujui.

```
+-------------------------------------------------------------+
| [ Logo RENTVERSE ] [ Cari ] [ Sewa ] [ Jual ] [ Login ]     |
+-------------------------------------------------------------+
|                                                             |
|   Studio Penthouse for rent at Tijani Raja Dewa |
|   Panji, Kota Bharu, Kelantan |
|                                                             |
|   +--------------------------+ +--------------------------+ |
|   |                          | | [ Gambar Kecil 1 ]       | |
|   |                          | | [ Gambar Kecil 2 ]       | |
|   |     [ Gambar Utama ]     | | [ Gambar Kecil 3 ]       | |
|   |                          | | [ Lihat Semua Foto ]     | |
|   +--------------------------+ +--------------------------+ |
|                                                             |
|   | Tentang Properti Ini | | Rp 2.130.000 / bulan | |
|   | [ Deskripsi Lengkap... ] | |                          | |
|   |                          | | [ Formulir Kontak Agen ] | |
|   | Fasilitas | | [ Nama Agen ]          | |
|   | - Kolam Renang           | | [ Tombol: Hubungi ]    | |
|   | - Parkir Tertutup        | |                          | |
|   | - Keamanan 24 Jam        | |                          | |
|                                                             |
+-------------------------------------------------------------+
```

---

_Dokumen ini akan menjadi acuan bagi tim desain UI/UX dan tim frontend dalam tahap pengembangan awal._
