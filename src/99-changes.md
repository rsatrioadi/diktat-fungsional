# Daftar Perubahan

## Diktat Pemrograman Fungsional: Revisi 2014 vs. Versi 2026

Lampiran ini mencatat seluruh perubahan struktural, terminologis, metodologis, dan konseptual antara **Draft Diktat Kuliah Dasar Pemrograman
– Bagian: Pemrograman Fungsional (Revisi Februari 2014)** karya _Inggriani Liem_ dengan **Diktat Pemrograman Fungsional (Agustus 2026)** yang disusun ulang oleh _Satrio Adi Rukmono_.

### 1. Perubahan Peran dan Penataan Dokumen

- **Peralihan Bentuk Publikasi:** Diktat tidak lagi difungsikan sebagai "Diktat Kuliah" yang terikat pada kurikulum mata kuliah tertentu (misalnya IF2030 / IF1210 ITB), melainkan disusun ulang menjadi **modul yang tidak terikat pada bahasa tertentu** dan dapat dipetakan secara luwes ke CPMK mata kuliah mana pun.

- **Pemisahan Badan Diktat dan Suplemen Eksekusi:**

  - **Versi 2014:** Menggabungkan Notasi Fungsional (Buku I) dan aturan penerjemahan ke LISP (Buku II) secara langsung.

  - **Versi 2026:** Memisahkan badan diktat (konseptual, tidak terikat bahasa) secara tegas dari **Suplemen Eksekusi**.

- **Bahasa Acuan Penerjemahan:** Bahasa acuan eksekusi diperbarui dari **LISP/CGLISP** menjadi **Haskell**, agar sesuai dengan bahasa acuan yang secara nyata dipakai dalam beberapa tahun terakhir dan lazim digunakan di industri saat ini, dengan nilai tambah berupa fitur pemeriksaan type statis (_static type checking_) dan pencocokan pola (_pattern matching_) eksplisit yang bernilai pedagogis.

### 2. Penyesuaian Terminologi dan Konvensi Notasi

Untuk menyelaraskan dengan perkembangan paradigma pemrograman fungsional arus utama, dilakukan penyesuaian terminologi dasar:

| Topik / Elemen                 | Versi 2014 (Lama)                                          | Versi 2026 (Baru)                                                        | Alasan Perubahan                                                                                                              |
| ------------------------------ | ---------------------------------------------------------- | ------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| **List Tanpa Elemen Terakhir** | `Head(L)`                                                  | Tidak ada                                                                | Istilah `Head` pada versi lama berkebalikan dengan konvensi bahasa fungsional modern (yang memakai `head` untuk elemen pertama). |
| **Elemen Pertama List**        | `FirstElmt(L)`                                             | `head(L)`                                                                | Menyesuaikan konvensi standar (`head`/`tail`).                                                                                |
| **Penamaan Predikat**          | Menggunakan akhiran `?` (misalnya `IsOrigin?`, `IsPositiv?`) | Menggunakan awalan `is` dan _camelCase_ (misalnya `isOrigin`, `isPositiv`) | Menghilangkan ambiguitas nama serta menyelaraskan dengan konvensi modern.                                                     |
| **Simbol Deklarasi/Definisi**  | Menggunakan `=` atau `:` secara bergantian                 | Menggunakan `:` untuk definisi, `=` khusus untuk perbandingan relasional | Mencegah ambiguitas antara penugasan/definisi dengan perbandingan logika.                                                     |
| **Nama Antara**                | Digabung dalam sintaks lokal `let ... in`                  | Tetap `let ... in` dengan aturan pengikatan eksplisit dari atas ke bawah | Mempertegas urutan _scope_ lokal tanpa memperkenalkan efek samping.                                                           |

### 3. Perubahan Struktural dan Urutan Bab

- **Bab Rekursi vs. Fungsi Orde Tinggi (_Higher-Order Function_):**

  - **Versi 2014:** Membahas Ekspresi Lambda / Fungsi Orde Tinggi di bagian akhir bab (Bab 11) setelah Pohon.

  - **Versi 2026:** Memindahkan Fungsi Orde Tinggi ke **Bab 8**, tepat setelah Bab List (Bab 7) dan **sebelum** Pohon (Bab 10). Mahasiswa dilatih membuat fungsi rekursi manual terlebih dahulu, baru kemudian diajarkan mengabstraksikannya dengan `map`, `filter`, dan `fold`.

- **Penambahan Bab Khusus "Dari Notasi ke Program" (Bab 9):**

  - Bab baru yang secara eksplisit menjembatani antara rancangan statis fungsional dengan kenyataan eksekusi pada mesin: membahas _kemurnian (purity)_, _transparansi referensial_, _efek samping (side-effects)_, serta strategi evaluasi (_lazy vs. strict_).

- **Reorganisasi Topik Pohon dan List of List:**

  - **Versi 2014:** List of List adalah subbab (9.5) di dalam Bab List, bukan bab tersendiri; Pohon (10) dan Lambda (11) menutup diktat secara berurutan.
  
  - **Versi 2026:** List of List diangkat menjadi Bab 11 tersendiri (opsional), sejajar dengan Pohon (Bab 10, juga eksplisit opsional). Fungsi Orde Tinggi/Lambda dipindah lebih awal ke Bab 8, tidak lagi menjadi penutup.

### 4. Pembaruan Metodologis dan Pedagogis

1. **Syarat Analisis Kasus (`depend on`):**

   - **Versi 2014:** Mengizinkan evaluasi implisit atau bergantung pada urutan eksekusi _interpreter_.

   - **Versi 2026:** Menegaskan aturan **Lengkap (_Exhaustive_)** dan **Disjoint (_Mutually Exclusive_)** secara formal. Urutan penulisan kasus pada notasi `depend on` dinyatakan **tidak memengaruhi arti/hasil**.

2. **Penanganan Kasus Konstruktor List Tambahan:**

   - **Versi 2014:** Menyediakan primitif konstruktor tambah-kiri (`Konso`) dan tambah-kanan (`Kons⏺`) secara sejajar.

   - **Versi 2026:** Hanya mengakui **satu konstruktor primitif** (`makeCons` / `:`). Penambahan di akhir list (`addLast`) diturunkan sebagai fungsi biasa untuk memperlihatkan kompleksitas/harga eksekusinya.

3. **Formalisasi Kewajiban Rekurens:**

   - Penulisan fungsi rekursif wajib mencantumkan dua komponen secara terbuka di bagian spesifikasi: **Kondisi Basis** dan **Konvergensi Rekurens** (besaran yang mengecil beserta batas bawahnya).

4. **Pendekatan Type Bentukan (Product & Alternative):**

   - Mempertegas perbedaan konseptual antara **Type Produk** (tuple/rekord) dan **Type Alternatif** (varian/union).

   - Menegaskan prinsip _abstraksi data_: seluruh fungsi luar wajib mengakses komponen type bentukan **hanya melalui selektor** dan membentuk nilai **hanya melalui konstruktor**.
