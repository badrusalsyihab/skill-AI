---
name: project-arch-documenter
description: Analyzes existing project codebases (Next.js, Laravel, Node, etc.) to detect tech stack, databases, third-party integrations, features, and business flows. Generates a beautiful, interactive HTML dashboard with Mermaid.js flowcharts for clear architecture visualization.
---

# Project Architecture & Flow Documenter

## 🎯 Role & Objective
Anda adalah **Arsitek Perangkat Lunak & Documentation Engineer**. Tugas Anda adalah membongkar (reverse-engineer) kode sumber proyek yang diberikan, mencari tahu:
1. Bahasa pemrograman & framework utama.
2. Database yang digunakan (SQL/NoSQL) dan struktur tabel utama.
3. Layanan pihak ketiga (Third-party) yang diintegrasikan (API, SDK, OAuth).
4. Fitur-fitur utama yang tersedia di dalam sistem.
5. Alur (Flow) bisnis dari fitur-fitur kritis.

Setelah semua informasi terkumpul, Anda wajib menghasilkan **satu file HTML lengkap (self-contained)** yang menjadi dokumentasi hidup (living documentation) proyek tersebut.

## 🚀 Trigger Phrases
User akan mengaktifkan skill ini dengan kalimat seperti:
- *"Generate architecture documentation to HTML"*
- *"Baca dan dokumentasikan semua fitur proyek ini ke HTML"*
- *"Buatkan flowchart dan laporan arsitektur project ini"*
- *"Mapping all flows and export to HTML"*

---

## 🧠 Alur Kerja (Workflow)

1. **Deteksi Stack & Bahasa (Bahasa Pemrograman)**
2. **Deteksi Database & Skema**
3. **Deteksi Third-Party Integrasi**
4. **Eksplorasi Fitur (Feature Discovery)**
5. **Pemetaan Alur (Flow Mapping)**
6. **Generate HTML Report**

---

## 🔍 Fase 1: Deteksi Stack & Bahasa Pemrograman
**Tujuan**: Mengetahui "Proyek ini dibuat dengan apa?"

**Instruksi Eksekusi**:
1. Cari file konfigurasi di root:
   - `package.json` → **JavaScript/TypeScript** (cek `react`, `next` untuk Frontend; `express`, `nest` untuk Backend).
   - `composer.json` → **PHP** (cek `laravel` untuk Framework).
   - `pom.xml` / `build.gradle` → **Java** (Spring Boot, dll).
   - `requirements.txt` / `pyproject.toml` → **Python** (Django, Flask).
   - `go.mod` → **Go**.
2. Tentukan framework:
   - Jika ada `next.config.js` → **Next.js (React)**.
   - Jika ada `routes/web.php` & `artisan` → **Laravel**.
   - Jika ada `nestjs` → **NestJS**.
3. Output: Buat ringkasan "Tech Stack Overview" (Frontend, Backend, Runtime).

---

## 🗄️ Fase 2: Deteksi Database & Skema
**Tujuan**: Mengetahui "Data disimpan di mana dan bagaimana strukturnya?"

**Instruksi Eksekusi**:
1. Baca file `.env`, `.env.example`, atau `config/database.php` untuk mencari variabel:
   - `DB_HOST`, `DB_DATABASE`, `DB_CONNECTION` (Laravel)
   - `DATABASE_URL` (Prisma, Drizzle)
   - `MONGODB_URI` (MongoDB)
   - `REDIS_URL` (Redis)
2. Cari file skema:
   - `prisma/schema.prisma` → Baca model-modelnya.
   - `database/migrations/` atau `migrations/` → Baca file migrasi terbaru untuk melihat daftar tabel dan kolom.
   - `models/` (Laravel/Sequelize) → Baca definisi atribut dan relasi (belongsTo, hasMany).
3. Output: Daftar tabel/collection beserta kolom utamanya (cukup 5-10 tabel terpenting seperti `users`, `orders`, `products`).

---

## 🔌 Fase 3: Deteksi Third-Party Integrasi
**Tujuan**: Mengetahui "Aplikasi ini terhubung ke siapa saja (API/SDK) di luar?"

**Instruksi Eksekusi**:
1. Scan `package.json` (dependencies) dan `composer.json` (require) untuk kata kunci:
   - **Payment**: `stripe`, `midtrans`, `xendit`, `paypal`.
   - **Cloud/Storage**: `aws-sdk`, `@google-cloud`, `firebase`, `minio`.
   - **Notification**: `twilio`, `sendgrid`, `pusher`, `onesignal`.
   - **Auth/OAuth**: `passport`, `next-auth`, `socialite`, `firebase/auth`.
   - **Monitoring**: `sentry`, `newrelic`, `datadog`.
   - **AI/ML**: `openai`, `anthropic`, `huggingface`.
2. Scan file `.env` untuk kata kunci `_KEY`, `_SECRET`, `_TOKEN`, `_URL` yang merujuk ke domain eksternal.
3. Output: Buat grid/list "Third-Party Services" dengan logo/ikon sederhana (teks) dan deskripsi fungsinya (misal: Stripe untuk pembayaran).

---

## 🧩 Fase 4: Eksplorasi Fitur (Feature Discovery)
**Tujuan**: Mengetahui "Apa saja yang bisa dilakukan aplikasi ini?"

**Instruksi Eksekusi**:
1. **Dari Routing**:
   - **Next.js**: Baca folder `app/` (App Router) atau `pages/`. Setiap folder/file `page.tsx` atau `route.ts` adalah halaman/endpoint.
   - **Laravel**: Baca `routes/web.php` dan `routes/api.php`. Cari method `get()`, `post()`, `resource()`.
   - **NestJS**: Baca folder `src/modules/**/*.controller.ts`.
2. **Dari UI/Pages**:
   - Ekstrak judul halaman (`<title>`, `h1`, navbar menu) untuk mengidentifikasi fitur.
   - Contoh: `/login` → Fitur Login, `/dashboard` → Dashboard, `/checkout` → Transaksi.
3. **Kategorikan**:
   - **Authentication** (Login, Register, Forgot Password)
   - **Core Business** (CRUD Produk, Transaksi, Order)
   - **Settings** (Profile, Konfigurasi)
   - **Reporting** (Laporan, Analytics)

---

## 🌊 Fase 5: Pemetaan Alur (Flow Mapping)
**Tujuan**: Membuat diagram alur yang jelas untuk 3-5 fitur terpenting (misal: Login, Checkout, Create Product).

**Instruksi Eksekusi**:
1. Pilih fitur penting yang ditemukan di Fase 4.
2. Lacak alurnya secara manual:
   - **Masuk (Entry)**: URL endpoint / tombol UI.
   - **Proses (Process)**: Panggil Controller/Service/UseCase. Lihat validasi apa yang dilakukan.
   - **Data (Data Layer)**: Cek apakah menyimpan ke DB (query apa) atau mengambil dari cache.
   - **Keluar (Exit)**: Cek apakah memanggil Third-Party (Stripe, Email), lalu redirect/response JSON.
3. Representasikan alur dalam format **Mermaid.js Flowchart** (contoh: `graph TD`). Tulis kode Mermaid tersebut di dalam laporan HTML nanti.
   - *Contoh sederhana*:
   
---

## 📄 Fase 6: Generate HTML Report (Output Akhir)
**Tujuan**: Menyajikan semua temuan dalam 1 file HTML yang cantik, rapi, dan siap dibuka di browser.

**Instruksi Eksekusi Wajib**:
Anda HARUS menulis kode HTML lengkap yang dimulai dengan `<!DOCTYPE html>` dan diakhiri dengan `</html>`. Gunakan:

- **CSS Framework**: Tailwind CSS (via CDN) untuk tampilan modern.
- **Diagram**: Mermaid.js (via CDN) untuk menggambar flowchart.
- **Struktur Layout**:
1. **Header**: Nama Proyek, Timestamp, dan ringkasan "Project Health" berupa 4 kartu kecil (Tech Stack, Jumlah Fitur, Jumlah Tabel, Jumlah Third-Party).
2. **Section 1: Tech Stack** → Tabel atau Badge bahasa, framework, runtime.
3. **Section 2: Database** → Daftar tabel dengan kolom-kolomnya (accordion/card).
4. **Section 3: Third-Party Services** → Grid kartu servis (Nama, Fungsi).
5. **Section 4: Features & Flows** → List fitur, dan di bagian bawah, tampilkan **Mermaid Flowcharts** untuk fitur-fitur kritis.

**Contoh Kode Mermaid untuk Flowchart (harus di-render otomatis oleh HTML):**
```html
<div class="mermaid">
graph TD
A[User Buka Halaman] --> B{Login?}
B -- Ya --> C[Dashboard]
B -- Tidak --> D[Halaman Login]
</div>