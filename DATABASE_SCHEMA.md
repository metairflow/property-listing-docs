# Dokumen Desain Database: Proyek RENTVERSE

**Versi: 1.0**
**Tanggal: 8 September 2025**
**Status: Disetujui**

---

## 1. Pengantar

### 1.1. Tujuan Dokumen

Dokumen ini menyediakan spesifikasi teknis yang detail untuk struktur database aplikasi RENTVERSE. Ini mencakup definisi untuk setiap tabel, kolom, tipe data, relasi, dan batasan (_constraints_). Dokumen ini akan menjadi acuan utama bagi tim backend saat mengimplementasikan skema menggunakan Prisma ORM.

### 1.2. Teknologi Database

- **Sistem Manajemen Database (DBMS)**: PostgreSQL
- **Object-Relational Mapping (ORM)**: Prisma

---

## 2. Diagram Relasi Entitas (ERD)

Diagram berikut memvisualisasikan hubungan antar semua tabel dalam database RENTVERSE.

**Ringkasan Relasi Utama:**

- Sebuah `Property` terhubung ke satu `Project` (One-to-Many).
- Sebuah `Property` diajukan oleh satu `User` (`AGENT`) dan disetujui oleh satu `User` (`ADMIN`) (One-to-Many).
- Sebuah `Property` dapat memiliki banyak `PropertyImages` (One-to-Many).
- Sebuah `Property` dan sebuah `Amenity` memiliki hubungan Many-to-Many melalui tabel `PropertyAmenities`.
- Sebuah `RentalAgreement` terhubung ke satu `Property` dan dua `Users` (`OWNER` dan `TENANT`).

---

## 3. Definisi Tabel Rinci

### 3.1. Tabel: `Users`

Menyimpan data untuk semua pengguna yang dapat berinteraksi dengan sistem, dibedakan oleh perannya.

| Nama Kolom  | Tipe Data     | Deskripsi                                         | Catatan                                    |
| :---------- | :------------ | :------------------------------------------------ | :----------------------------------------- |
| `id`        | `Int`         | Identifier unik untuk setiap pengguna.            | Kunci Utama (Primary Key), Auto-increment. |
| `fullName`  | `String`      | Nama lengkap pengguna.                            |                                            |
| `email`     | `String`      | Alamat email pengguna, digunakan untuk login.     | Unik (Unique).                             |
| `password`  | `String`      | Password pengguna.                                | **Wajib disimpan dalam format hash.**      |
| `role`      | `Enum (Role)` | Peran pengguna dalam sistem.                      | Lihat Definisi Enum di bawah.              |
| `createdAt` | `DateTime`    | Timestamp saat pengguna dibuat.                   | Dikelola otomatis oleh Prisma.             |
| `updatedAt` | `DateTime`    | Timestamp saat data pengguna terakhir diperbarui. | Dikelola otomatis oleh Prisma.             |

### 3.2. Tabel: `Projects`

Menyimpan data master untuk sebuah kawasan atau gedung properti. Data ini digunakan untuk fitur auto-fill.

| Nama Kolom       | Tipe Data  | Deskripsi                                     | Catatan                                    |
| :--------------- | :--------- | :-------------------------------------------- | :----------------------------------------- |
| `id`             | `Int`      | Identifier unik untuk setiap proyek.          | Kunci Utama (Primary Key), Auto-increment. |
| `projectName`    | `String`   | Nama resmi dari proyek properti.              | Contoh: "Tijani Raja Dewa - Apartments".   |
| `developer`      | `String`   | Nama perusahaan pengembang.                   |                                            |
| `address`        | `String`   | Alamat umum dari kawasan/gedung.              |                                            |
| `description`    | `Text`     | Deskripsi umum tentang proyek.                |                                            |
| `completionDate` | `DateTime` | Tanggal perkiraan atau aktual proyek selesai. |                                            |

### 3.3. Tabel: `Properties`

Tabel inti yang menyimpan setiap listing properti yang disewakan.

| Nama Kolom         | Tipe Data               | Deskripsi                                  | Catatan                                            |
| :----------------- | :---------------------- | :----------------------------------------- | :------------------------------------------------- |
| `id`               | `Int`                   | Identifier unik untuk setiap listing.      | Kunci Utama (Primary Key), Auto-increment.         |
| `title`            | `String`                | Judul iklan properti.                      |                                                    |
| `description`      | `Text`                  | Deskripsi lengkap dan unik untuk unit ini. |                                                    |
| `propertyType`     | `Enum (PropertyType)`   | Kategori properti.                         | Lihat Definisi Enum.                               |
| `rentalPrice`      | `Float` / `Decimal`     | Harga sewa per bulan/tahun.                |                                                    |
| `sizeSqft`         | `Int`                   | Luas properti dalam satuan kaki persegi.   |                                                    |
| `bedrooms`         | `Int`                   | Jumlah kamar tidur.                        |                                                    |
| `bathrooms`        | `Int`                   | Jumlah kamar mandi.                        |                                                    |
| `floorLevel`       | `Int`                   | Posisi lantai unit properti.               | Opsional.                                          |
| `furnishingStatus` | `Enum (Furnishing)`     | Status kelengkapan perabotan.              | Lihat Definisi Enum.                               |
| `petsAllowed`      | `Boolean`               | Apakah hewan peliharaan diizinkan.         | `true` atau `false`.                               |
| `status`           | `Enum (PropertyStatus)` | Status alur kerja listing.                 | Default: `PENDING`. Lihat Definisi Enum.           |
| `listedDate`       | `DateTime`              | Tanggal properti ini didaftarkan.          | Dikelola otomatis.                                 |
| `projectId`        | `Int`                   | Menghubungkan ke proyek induk.             | Kunci Asing (Foreign Key) -> `Projects.id`.        |
| `listedById`       | `Int`                   | ID agen yang mengajukan listing.           | Kunci Asing (Foreign Key) -> `Users.id`.           |
| `approvedById`     | `Int`                   | ID admin yang menyetujui listing.          | Opsional, Kunci Asing (Foreign Key) -> `Users.id`. |

### 3.4. Tabel: `PropertyImages`

Menyimpan galeri gambar untuk setiap properti.

| Nama Kolom     | Tipe Data | Deskripsi                                        | Catatan                                       |
| :------------- | :-------- | :----------------------------------------------- | :-------------------------------------------- |
| `id`           | `Int`     | Identifier unik untuk setiap gambar.             | Kunci Utama (Primary Key), Auto-increment.    |
| `propertyId`   | `Int`     | Menghubungkan gambar ke properti yang relevan.   | Kunci Asing (Foreign Key) -> `Properties.id`. |
| `imageUrl`     | `String`  | URL publik dari file gambar.                     |                                               |
| `displayOrder` | `Int`     | Urutan tampilan gambar di galeri (1, 2, 3, ...). | Opsional, default: 0.                         |

### 3.5. Tabel: `Amenities`

Tabel master untuk semua jenis fasilitas yang tersedia.

| Nama Kolom | Tipe Data | Deskripsi                               | Catatan                                    |
| :--------- | :-------- | :-------------------------------------- | :----------------------------------------- |
| `id`       | `Int`     | Identifier unik untuk setiap fasilitas. | Kunci Utama (Primary Key), Auto-increment. |
| `name`     | `String`  | Nama fasilitas.                         | Unik. Contoh: "Kolam Renang", "Parkir".    |
| `icon`     | `String`  | Nama atau URL ikon fasilitas.           | Opsional.                                  |

### 3.6. Tabel: `PropertyAmenities`

Tabel penghubung (_join table_) untuk relasi Many-to-Many antara `Properties` dan `Amenities`.

| Nama Kolom   | Tipe Data | Deskripsi                         | Catatan                           |
| :----------- | :-------- | :-------------------------------- | :-------------------------------- |
| `propertyId` | `Int`     | Menghubungkan ke `Properties.id`. | Bagian dari Kunci Utama Gabungan. |
| `amenityId`  | `Int`     | Menghubungkan ke `Amenities.id`.  | Bagian dari Kunci Utama Gabungan. |

### 3.7. Tabel: `RentalAgreements`

Menyimpan data untuk setiap kontrak sewa yang dibuat (untuk Challenge 3).

| Nama Kolom        | Tipe Data                | Deskripsi                                  | Catatan                                       |
| :---------------- | :----------------------- | :----------------------------------------- | :-------------------------------------------- |
| `id`              | `Int`                    | Identifier unik untuk setiap kontrak.      | Kunci Utama (Primary Key), Auto-increment.    |
| `propertyId`      | `Int`                    | Properti yang disewakan.                   | Kunci Asing (Foreign Key) -> `Properties.id`. |
| `tenantId`        | `Int`                    | Pengguna yang menjadi penyewa.             | Kunci Asing (Foreign Key) -> `Users.id`.      |
| `ownerId`         | `Int`                    | Pengguna yang menjadi pemilik.             | Kunci Asing (Foreign Key) -> `Users.id`.      |
| `startDate`       | `DateTime`               | Tanggal mulai periode sewa.                |                                               |
| `endDate`         | `DateTime`               | Tanggal berakhir periode sewa.             |                                               |
| `rentalAmount`    | `Float` / `Decimal`      | Biaya sewa yang disepakati dalam kontrak.  |                                               |
| `status`          | `Enum (AgreementStatus)` | Status kontrak saat ini.                   | Lihat Definisi Enum.                          |
| `generatedPdfUrl` | `String`                 | URL ke file PDF kontrak yang telah dibuat. | Opsional.                                     |

---

## 4. Definisi Tipe Enum

- **`Role`**:

  - `AGENT`: Pengguna yang dapat mengajukan dan mengelola listing properti.
  - `ADMIN`: Pengguna dengan hak akses penuh, termasuk menyetujui listing.
  - `OWNER`: Pengguna yang memiliki properti.
  - `TENANT`: Pengguna yang menyewa properti.

- **`PropertyType`**:

  - `APARTMENT`, `HOUSE`, `PENTHOUSE`, `STUDIO`, `COMMERCIAL`

- **`Furnishing`**:

  - `UNFURNISHED`: Tanpa perabotan.
  - `PARTIALLY_FURNISHED`: Sebagian perabotan.
  - `FULLY_FURNISHED`: Lengkap dengan perabotan.

- **`PropertyStatus`**:

  - `PENDING`: Menunggu persetujuan dari Admin.
  - `APPROVED`: Disetujui dan dapat ditampilkan secara publik.
  - `REJECTED`: Ditolak oleh Admin.
  - `RENTED`: Sudah disewakan.

- **`AgreementStatus`**:
  - `ACTIVE`: Kontrak sedang berjalan.
  - `EXPIRED`: Masa berlaku kontrak telah habis.
  - `TERMINATED`: Kontrak dihentikan sebelum waktunya.
