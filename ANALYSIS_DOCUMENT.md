# Dokumen Analisis dan Perancangan Sistem: Proyek RENTVERSE

**Versi: 1.0**
**Tanggal: 8 September 2025**
**Status: Draf Awal**

---

## 1. Ringkasan Proyek

### 1.1. Tujuan

Proyek RENTVERSE bertujuan untuk membangun sebuah platform properti terpadu yang menyatukan tiga fungsionalitas utama dari tantangan yang diberikan:

1.  **Manajemen Listing Properti**: Termasuk alur pengajuan oleh agen dan persetujuan oleh admin.
2.  **Simulator Harga Sewa**: Memberikan rekomendasi harga sewa secara _real-time_ menggunakan model Machine Learning.
3.  **Generator Kontrak Sewa**: Mengotomatisasi pembuatan dokumen perjanjian sewa.

### 1.2. Arsitektur yang Diusulkan

Aplikasi ini akan dibangun menggunakan pendekatan arsitektur **microservices hibrida**. Pendekatan ini dipilih untuk menyeimbangkan antara kecepatan pengembangan awal dan skalabilitas jangka panjang. Logika bisnis inti akan disatukan dalam satu layanan monolitik (_Core Service_), sementara fungsionalitas yang sangat terspesialisasi (seperti prediksi harga) akan diisolasi menjadi layanan mikro (_microservice_) sendiri.

---

## 2. Analisis Fungsional (User Stories)

Berikut adalah rincian fungsionalitas utama dari sudut pandang setiap peran pengguna (aktor).

### 2.1. Pengguna Publik (Calon Penyewa)

- **Sebagai pengguna publik**, saya ingin mencari dan memfilter properti berdasarkan lokasi, tipe, dan harga, agar saya dapat menemukan properti yang relevan dengan cepat.
- **Sebagai pengguna publik**, saya ingin melihat halaman detail properti yang menampilkan galeri foto, deskripsi lengkap, fasilitas, dan informasi harga, agar saya bisa membuat keputusan sewa.
- **Sebagai pengguna publik**, saya ingin dapat mengisi formulir kontak untuk bertanya lebih lanjut tentang properti kepada agen.

### 2.2. Agen Properti (Peran: `AGENT`)

- **Sebagai agen**, saya ingin dapat mendaftar dan login ke dalam sistem, agar saya bisa mengakses dasbor pribadi saya.
- **Sebagai agen**, saya ingin dapat mengajukan properti baru melalui formulir yang interaktif, agar proses input data menjadi efisien.
- **Sebagai agen**, saya ingin menggunakan fitur **auto-fill** yang mengambil data dari proyek yang sudah ada, untuk mempercepat pengisian data umum.
- **Sebagai agen**, saat mengisi formulir, saya ingin mendapatkan **rekomendasi harga sewa** dari sistem, agar saya bisa menetapkan harga yang kompetitif.
- **Sebagai agen**, saya ingin melihat daftar properti yang telah saya ajukan beserta statusnya (Menunggu Persetujuan, Disetujui, Ditolak).

### 2.3. Admin (Peran: `ADMIN`)

- **Sebagai admin**, saya ingin dapat login ke dasbor admin khusus.
- **Sebagai admin**, saya ingin melihat daftar semua properti yang diajukan oleh agen dan masih menunggu persetujuan.
- **Sebagai admin**, saya ingin dapat meninjau detail setiap properti yang diajukan.
- **Sebagai admin**, saya ingin memiliki kemampuan untuk **menyetujui** atau **menolak** sebuah listing properti, agar kualitas konten yang tampil di publik terjaga.

---

## 3. Analisis Arsitektur Sistem

Sistem akan terdiri dari beberapa komponen utama yang bekerja secara independen namun terkoordinasi.

- **Frontend (Next.js)**: Satu aplikasi antarmuka pengguna yang akan digunakan oleh semua aktor. Bertindak sebagai _gateway_ visual ke seluruh sistem.
- **API Gateway (Kong)**: Satu titik masuk (_single entry point_) untuk semua permintaan dari frontend. Bertugas untuk merutekan permintaan ke layanan yang tepat, serta menangani keamanan dan otentikasi awal.
- **Core Service (Express.js)**: Layanan monolitik yang menangani mayoritas logika bisnis, termasuk:
  - Manajemen Pengguna (Auth Service)
  - Manajemen Properti (Property Service)
  - Manajemen Dokumen (Document Service)
- **Prediction Service (FastAPI)**: Layanan mikro spesialis yang terisolasi, khusus untuk menangani komputasi prediksi harga sewa.

---

## 4. Desain Database

Database akan menggunakan **PostgreSQL** dengan **Prisma** sebagai ORM (Object-Relational Mapping).

### 4.1. Skema Tabel

| Nama Tabel          | Deskripsi                                             |
| :------------------ | :---------------------------------------------------- |
| `Users`             | Menyimpan data semua pengguna dan perannya.           |
| `Projects`          | Data master untuk proyek properti (sumber auto-fill). |
| `Properties`        | Data untuk setiap listing properti individual.        |
| `PropertyImages`    | Menyimpan URL gambar untuk galeri properti.           |
| `Amenities`         | Data master untuk semua jenis fasilitas.              |
| `PropertyAmenities` | Tabel penghubung antara Properti dan Fasilitas.       |
| `RentalAgreements`  | Data untuk kontrak sewa (Challenge 3).                |

### 4.2. Relasi Antar Tabel

Relasi utama adalah sebagai berikut:

- Sebuah `Property` terhubung ke satu `Project`.
- Sebuah `Property` diajukan oleh satu `User` (Agent) dan disetujui oleh satu `User` (Admin).
- Sebuah `Property` bisa memiliki banyak `PropertyImages` dan `PropertyAmenities`.

---

## 5. Tumpukan Teknologi (Tech Stack)

| Komponen                     | Teknologi yang Diusulkan      |
| :--------------------------- | :---------------------------- |
| **Frontend**                 | Next.js (React), Tailwind CSS |
| **API Gateway**              | Kong                          |
| **Core Service**             | Node.js, Express.js, Prisma   |
| **Prediction Service**       | Python, FastAPI, Scikit-learn |
| **Database**                 | SQlite                        |
| **Containerization**         | Docker, Docker Compose        |
| **Orchestration (Produksi)** | Kubernetes                    |

---

_Dokumen ini akan diperbarui seiring dengan perkembangan proyek._
