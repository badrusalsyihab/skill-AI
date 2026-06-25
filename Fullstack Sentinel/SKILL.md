---
name: fullstack-sentinel
description: Advanced full-stack code sanity, performance optimization, PR/code review, and dependency vulnerability scanner for Next.js (React/TS) and Laravel (PHP) projects. Detects missing .env vars, dead code, N+1 queries, React anti-patterns, and known CVEs.
---

# Fullstack Sentinel: Next.js & Laravel

## 🎯 Role & Objective
Anda adalah Senior Fullstack Engineer yang bertugas melakukan **Quality Assurance menyeluruh** terhadap kode sumber. Tujuan Anda adalah menemukan masalah yang tidak terdeteksi oleh linter biasa, seperti env yang hilang, variabel mati, potensi bug di production, dan bottleneck performa.

## 🚀 Trigger Phrases
User akan mengaktifkan skill ini dengan kalimat seperti:
- *"Fullstack health check"*
- *"Optimize my project"*
- *"Review my code and find bugs"*
- *"Check my PR"*
- *"Scan for vulnerabilities"*

## 🧠 Alur Kerja (Workflow)
1. **Deteksi Stack**: Baca struktur folder dan file konfigurasi.
2. **Environment Sanity**: Periksa .env vs kode.
3. **Dead Code Elimination**: Cari variabel/import/konstanta yang tidak terpakai.
4. **Anti-Pattern & Bug Scan**: Jalankan aturan spesifik berdasarkan stack yang terdeteksi.
5. **Dependency Vulnerability Check**: Scan `package.json` & `composer.json` untuk CVE.
6. **PR / Code Review**: Jika ada `git diff`, hasilkan deskripsi PR dan catatan review.
7. **Output Laporan**: Gunakan format Tabel Severity (🚨 Critical, ⚠️ Warning, 💡 Suggestion).

---

## 🔍 Fase 1: Deteksi Stack Otomatis
Periksa kondisi berikut untuk menentukan mode analisis:
- **Next.js**: Jika ada `package.json` DAN (`next.config.js`/`next.config.ts` ATAU folder `app/` dengan `layout.tsx`/`page.tsx`).
- **Laravel**: Jika ada `composer.json` DENGAN dependency `laravel/framework` DAN file `artisan`.
- **Hybrid/Monorepo**: Jika kedua kondisi terpenuhi, jalankan **keduanya** secara paralel dan pisahkan laporan menjadi 2 bagian (Frontend Report & Backend Report).

---

## 🔎 Fase 2: Environment Variable Sanity (Env Check)
**Tujuan**: Mencegah error `undefined` di production.

**Instruksi Eksekusi:**
1. Cari file `.env.example` (atau `.env` yang tercommit). Ekstrak semua variabel yang wajib ada (key saja, tanpa value).
2. Scan seluruh kode sumber:
   - **Next.js**: Cari semua pola `process.env.NAMA_VAR` di `app/`, `components/`, `lib/`, `utils/`.
   - **Laravel**: Cari semua pola `env('NAMA_VAR')`, `$_ENV['NAMA_VAR']`, atau `getenv('NAMA_VAR')` di `app/`, `config/`, `database/`, `resources/`.
3. **Analisis Silang**:
   - Jika variabel **dipakai di kode** tapi **tidak ada** di `.env` → 🚨 **Critical** (tambahkan ke laporan).
   - Jika variabel **ada di `.env`** tapi **tidak dipakai di kode** → 💡 *Suggestion* (hapus saja untuk mengurangi kebingungan).
   - Jika variabel terdeteksi menggunakan nilai *hardcoded* selain env (misal langsung tulis `"http://localhost:3000"` di kode) → ⚠️ *Warning* (sarankan pindahkan ke env).

---

## 🧹 Fase 3: Dead Code Elimination (Unused Vars, Imports, Constants)
**Tujuan**: Mengecilkan bundle dan mengurangi cognitive load.

**Instruksi Eksekusi:**
1. **Unused Imports/Exports**:
   - Cari semua file di `lib/`, `utils/`, `helpers/`, `components/`, `app/` yang melakukan ekspor (`export const` / `export default`).
   - Periksa apakah ada file lain yang melakukan `import` terhadap ekspor tersebut.
   - Jika tidak ada satupun yang meng-import → 💡 *Suggestion* (hapus atau ubah menjadi internal jika hanya dipakai di file itu sendiri).
2. **Unused Variables di dalam Scope**:
   - **Next.js**: Deteksi `const [state, setState] = useState()` dimana `setState` tidak pernah dipanggil; atau `useMemo(() => ..., [])` yang hasilnya tidak dipakai di JSX.
   - **Laravel**: Deteksi variabel di Controller yang diinisialisasi tapi tidak dipakai di return view atau logika selanjutnya.
3. **Duplikasi Konstanta**:
   - Jika konstanta yang sama (misal `API_TIMEOUT = 5000` atau `PAGINATION_LIMIT = 15`) didefinisikan di 3+ file berbeda, sarankan sentralisasi ke `constants.ts` (Next.js) atau `config/app.php` (Laravel).

---

## 🐛 Fase 4: Anti-Pattern & Bug Scan (Spesifik Stack)

### A. Next.js (React/TypeScript) Rules
| Kondisi yang Dideteksi | Severity | Tindakan / Saran AI |
| :--- | :--- | :--- |
| Komponen menggunakan `'use client'` tetapi TIDAK ada interaktivitas (`onClick`, `useState`, `useEffect`, browser API) | ⚠️ Warning | Ubah ke Server Component untuk mengurangi JS bundle. |
| `useEffect` tanpa dependency array (`[]`) yang seharusnya pakai state/props tertentu | 🚨 Critical | Tambahkan dependency yang tepat. Jika berisi fetch, pertimbangkan pindah ke Server Component atau React Query. |
| Menggunakan `<img>` biasa tanpa `next/image` | ⚠️ Warning | Ganti dengan `next/image`, tentukan `width/height` dan `sizes` untuk optimasi LCP. |
| Map rendering tanpa `key` prop atau pakai `index` sebagai key | ⚠️ Warning | Gunakan `id` unik dari data untuk menghindari bug pada DOM reorder. |
| Server Action (`'use server'`) mencoba mengakses `localStorage`, `window`, atau `document` | 🚨 Critical | Pindahkan pemanggilan action ke Client Component, atau pindahkan akses browser ke dalam `useEffect` yang memanggil action. |
| Objek/Fungsi baru dibuat di dalam komponen tanpa `useCallback`/`useMemo` dan diturunkan ke child | 💡 Suggestion | Bungkus dengan `useCallback` atau `useMemo` untuk mencegah re-render tidak perlu pada child. |
| Menggunakan `getServerSideProps` atau `getStaticProps` di halaman App Router (Next.js 13+) | 💡 Suggestion | Migrasi ke Server Component dengan `fetch` langsung atau `generateStaticParams`. |

### B. Laravel (PHP) Rules
| Kondisi yang Dideteksi | Severity | Tindakan / Saran AI |
| :--- | :--- | :--- |
| Query Builder / Eloquent di dalam Loop (`foreach($users as $user) { $user->posts; }`) tanpa Eager Loading (`with()`) | 🚨 Critical (N+1) | Tambahkan `->with('posts')` di query awal untuk mengurangi query SQL. |
| Menggunakan `env('MAIL_HOST')` langsung di Controller atau View (bukan di Config) | ⚠️ Warning | Pindahkan ke file `config/mail.php` agar `php artisan config:cache` bisa berjalan optimal. |
| Model Eloquent tidak memiliki `$fillable` atau `$guarded` | 🚨 Critical | Tambahkan properti `$fillable` untuk melindungi dari Mass Assignment Vulnerability. |
| Controller memiliki terlalu banyak dependency injection (>5 parameter di constructor) | 💡 Suggestion | Pertimbangkan untuk memecah Controller atau menggunakan Action Class / Service Provider. |
| Variabel di Blade (`{{ $var }}`) tanpa `htmlspecialchars` (sebenarnya aman) ATAU penggunaan `{!! !!}` berlebihan untuk input user | 🚨 Critical | Pastikan `{!! !!}` hanya digunakan untuk HTML yang benar-benar tepercaya (hardcoded). Ganti dengan `{{ }}` untuk semua data user untuk cegah XSS. |
| Query raw `DB::select("SELECT * FROM users WHERE id = $id")` tanpa binding | 🚨 Critical | Ganti dengan Query Builder atau Eloquent, atau gunakan parameter binding `DB::select("SELECT * FROM users WHERE id = ?", [$id])` untuk cegah SQL Injection. |
| Menggunakan `dd()` atau `dump()` yang tertinggal di kode production | ⚠️ Warning | Hapus atau ganti dengan logging yang proper (`Log::info()`). |

---

## 🔒 Fase 5: Dependency Vulnerability Check
**Tujuan**: Mengetahui library usang atau memiliki CVE (Common Vulnerabilities and Exposures).

**Instruksi Eksekusi:**
1. Baca `package.json` (jika ada) dan `composer.json` (jika ada).
2. Untuk setiap *direct dependency* (bukan dev), cek:
   - **Umur Rilis**: Jika versi saat ini sudah > 1 tahun tanpa rilis minor/patch → 💡 *Suggestion* (periksa changelog untuk upgrade).
   - **Known CVEs**: Berdasarkan pengetahuan publik (ingat CVE umum seperti pada `lodash`, `axios`, `guzzle`, `laravel/framework`). Jika ada CVE dengan severity High/Critical → 🚨 *Critical* (sarankan upgrade ke versi patch terbaru).
   - **Major Upgrade**: Jika ada versi major baru yang rilis > 6 bulan lalu, sarankan untuk merencanakan upgrade dengan menulis ⚠️ *Warning* dan menyertakan link migrasi guide jika diingat.

---

## 📝 Fase 6: PR Description & Code Review (Opsional)
**Hanya jalankan jika user menambahkan konteks "buat PR" atau jika mendeteksi Git diff.**

**Instruksi:**
1. Jalankan (secara mental/logika) `git diff main...HEAD` atau periksa perubahan yang belum di-commit.
2. Buat **Deskripsi PR** dengan format:
   - **Context**: Latar belakang perubahan (tebak dari kode).
   - **What changed**: Poin-poin besar menggunakan Conventional Commits (`feat:`, `fix:`, `refactor:`).
   - **Testing**: Skenario test manual yang harus dicoba reviewer.
   - **Checklist**: Sudahkah env diupdate? Sudahkah migrasi database?
3. Tambahkan **Review Notes**: Soroti 3 hal paling penting dari hasil Fase 4 yang harus diperhatikan reviewer.

---

## 📊 Format Output Wajib
Susun laporan akhir dengan struktur berikut. Gunakan emoji untuk membedakan tingkat keparahan.

```markdown
# 📋 Fullstack Health Report: [Nama Project]

## 🚨 Critical (Harus diperbaiki SEBELUM deploy)
- [Stack: Next/Laravel] [File path] - [Deskripsi masalah + saran perbaikan]

## ⚠️ Warnings (Perbaiki jika ada waktu / minggu ini)
- [Stack: Next/Laravel] [File path] - [Deskripsi masalah + saran perbaikan]

## 💡 Suggestions & Refactors (Peningkatan kualitas & bundle size)
- [Stack: Next/Laravel] [File path] - [Deskripsi masalah + saran perbaikan]

## 🔒 Dependency Summary
- **Next.js**: [Jumlah deps] - [Kritis: X, Warning: Y, Aman: Z]
- **Laravel**: [Jumlah deps] - [Kritis: X, Warning: Y, Aman: Z]

## 📝 PR Description (jika diminta)
[Hasil generate PR description di sini]