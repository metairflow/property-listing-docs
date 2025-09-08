# Dokumen Kontrak API: Proyek RENTVERSE

**Versi: 1.0**
**Tanggal: 8 September 2025**
**Status: Draf Awal**

---

## 1. Pengantar

### 1.1. Tujuan Dokumen

Dokumen ini berfungsi sebagai "satu sumber kebenaran" (_single source of truth_) untuk semua interaksi antar layanan dalam ekosistem RENTVERSE. Ini mendefinisikan secara ketat setiap endpoint API, termasuk metode, struktur data permintaan (_request_), dan struktur data respons (_response_). Tujuannya adalah untuk memungkinkan tim frontend dan backend bekerja secara paralel dengan pemahaman yang sama dan mengurangi risiko kesalahan integrasi.

### 1.2. Format Spesifikasi

Spesifikasi ini ditulis menggunakan **OpenAPI Specification (OAS) versi 3.0.0** dalam format **YAML**. YAML dipilih karena lebih mudah dibaca oleh manusia dibandingkan JSON.

---

## 2. Spesifikasi OpenAPI Lengkap

Berikut adalah definisi lengkap untuk semua layanan backend yang akan dibangun.

```yaml
openapi: "3.0.0"
info:
  title: RENTVERSE API
  description:
    API terpadu untuk platform RENTVERSE, mencakup Properti, Pengguna,
    dan Prediksi Harga. Didesain dengan pendekatan microservices hibrida.
  version: 1.0.0
servers:
  - url: http://localhost:8080/api
    description: Server Pengembangan Lokal via API Gateway
paths:
  /auth/register:
    post:
      summary: Mendaftarkan pengguna baru
      tags:
        - Authentication
      operationId: registerUser
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/UserRegister"
      responses:
        "201":
          description: Pengguna berhasil dibuat
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/UserResponse"
        "400":
          description: Permintaan buruk (misalnya, data tidak valid)
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/ErrorResponse"
        "409":
          description: Konflik (misalnya, email sudah ada)
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/ErrorResponse"
  /auth/login:
    post:
      summary: Login pengguna
      tags:
        - Authentication
      operationId: loginUser
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/UserLogin"
      responses:
        "200":
          description: Login berhasil, mengembalikan access token
          content:
            application/json:
              schema:
                type: object
                properties:
                  accessToken:
                    type: string
                    example: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
        "401":
          description: Kredensial tidak valid
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/ErrorResponse"
  /projects:
    get:
      summary: Mendapatkan semua proyek untuk auto-fill
      tags:
        - Projects
      operationId: getAllProjects
      security:
        - bearerAuth: []
      responses:
        "200":
          description: Daftar proyek
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/ProjectSummary"
        "401":
          description: Tidak terautentikasi
  /properties:
    post:
      summary: Mengajukan listing properti baru
      tags:
        - Properties
      operationId: createProperty
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/PropertyCreate"
      responses:
        "201":
          description: Properti berhasil dibuat
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/PropertyResponse"
        "400":
          description: Data input tidak valid
        "401":
          description: Tidak terautentikasi
  /properties/pending:
    get:
      summary: Mendapatkan semua properti yang menunggu persetujuan
      tags:
        - Properties
      operationId: getPendingProperties
      security:
        - bearerAuth: []
      responses:
        "200":
          description: Daftar properti yang menunggu persetujuan
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/PropertyResponse"
        "401":
          description: Tidak terautentikasi
        "403":
          description: Tidak memiliki izin (bukan Admin)
  /properties/{id}/status:
    patch:
      summary: Menyetujui atau menolak properti
      tags:
        - Properties
      operationId: updatePropertyStatus
      security:
        - bearerAuth: []
      parameters:
        - in: path
          name: id
          required: true
          schema:
            type: integer
          description: ID dari properti yang akan diupdate
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/StatusUpdate"
      responses:
        "200":
          description: Status properti berhasil diperbarui
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/PropertyResponse"
        "401":
          description: Tidak terautentikasi
        "403":
          description: Tidak memiliki izin (bukan Admin)
        "404":
          description: Properti tidak ditemukan
  /predict:
    post:
      summary: Memprediksi harga sewa
      tags:
        - Prediction
      operationId: predictPrice
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/PredictionRequest"
      responses:
        "200":
          description: Prediksi harga berhasil
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/PredictionResponse"
components:
  schemas:
    UserRegister:
      type: object
      required:
        - fullName
        - email
        - password
        - role
      properties:
        fullName:
          type: string
          example: Budi Santoso
        email:
          type: string
          format: email
          example: budi.s@example.com
        password:
          type: string
          format: password
          example: MySecurePassword123
        role:
          type: string
          enum: [AGENT, ADMIN, OWNER, TENANT]
          example: AGENT
    UserLogin:
      type: object
      required:
        - email
        - password
      properties:
        email:
          type: string
          format: email
          example: budi.s@example.com
        password:
          type: string
          format: password
          example: MySecurePassword123
    UserResponse:
      type: object
      properties:
        id:
          type: integer
          example: 1
        fullName:
          type: string
          example: Budi Santoso
        email:
          type: string
          example: budi.s@example.com
        role:
          type: string
          example: AGENT
    ProjectSummary:
      type: object
      properties:
        id:
          type: integer
          example: 1
        projectName:
          type: string
          example: Tijani Raja Dewa - Apartments
    PropertyCreate:
      type: object
      required:
        - title
        - propertyType
        - rentalPrice
        - sizeSqft
        - projectId
      properties:
        title:
          type: string
          example: Studio Penthouse for rent at Tijani Raja Dewa
        description:
          type: string
          example: A beautiful penthouse with a city view...
        propertyType:
          type: string
          enum: [APARTMENT, HOUSE, PENTHOUSE, STUDIO]
          example: PENTHOUSE
        rentalPrice:
          type: number
          example: 2130000
        sizeSqft:
          type: number
          example: 1120
        bedrooms:
          type: number
          example: 1
        bathrooms:
          type: number
          example: 1
        furnishingStatus:
          type: string
          enum: [UNFURNISHED, PARTIALLY_FURNISHED, FULLY_FURNISHED]
          example: FULLY_FURNISHED
        projectId:
          type: integer
          example: 1
    PropertyResponse:
      type: object
      properties:
        id:
          type: integer
          example: 101
        title:
          type: string
          example: Studio Penthouse for rent at Tijani Raja Dewa
        status:
          type: string
          enum: [PENDING, APPROVED, REJECTED, RENTED]
          example: PENDING
        rentalPrice:
          type: number
          example: 2130000
        sizeSqft:
          type: number
          example: 1120
        listedDate:
          type: string
          format: date-time
          example: "2025-09-08T10:00:00Z"
    StatusUpdate:
      type: object
      required:
        - status
      properties:
        status:
          type: string
          enum: [APPROVED, REJECTED]
          example: APPROVED
    PredictionRequest:
      type: object
      required:
        - sizeSqft
        - bedrooms
        - bathrooms
        - propertyType
        - furnishingStatus
        - location
      properties:
        sizeSqft:
          type: number
          example: 1120
        bedrooms:
          type: number
          example: 1
        bathrooms:
          type: number
          example: 1
        propertyType:
          type: string
          example: PENTHOUSE
        furnishingStatus:
          type: string
          example: FULLY_FURNISHED
        location:
          type: string
          example: Kelantan
    PredictionResponse:
      type: object
      properties:
        predictedPrice:
          type: number
          example: 2250000
        confidenceScore:
          type: number
          example: 0.85
        priceRange:
          type: array
          items:
            type: number
          example: [2100000, 2400000]
    ErrorResponse:
      type: object
      properties:
        message:
          type: string
          example: Email already exists.
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
```

## 3. Catatan Implementasi Kunci

### 3.1. Alur Autentikasi

Sistem akan menggunakan JSON Web Tokens (JWT) untuk autentikasi.

Klien (Frontend) akan mengirimkan email dan password ke endpoint /auth/login.

Server akan merespons dengan sebuah accessToken.

Untuk semua permintaan ke endpoint yang terlindungi, klien harus menyertakan token ini di dalam Authorization header dengan skema Bearer. Contoh: Authorization: Bearer <token>.

API Gateway (Kong) akan bertanggung jawab untuk memvalidasi token ini sebelum meneruskan permintaan ke layanan internal.

### 3.2. Penanganan Error

Setiap respons error akan mengikuti skema ErrorResponse yang didefinisikan di atas, yang berisi sebuah field message untuk memberikan umpan balik yang jelas kepada klien.

Kode status HTTP yang sesuai akan digunakan untuk setiap jenis error (misalnya 400 untuk input buruk, 401 untuk tidak terautentikasi, 403 untuk tidak diizinkan, 404 untuk tidak ditemukan).
