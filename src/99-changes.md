# Daftar Perubahan

## Diktat Pemrograman Fungsional: Revisi 2014 vs. Versi 2026

Lampiran ini mencatat seluruh perubahan struktural, terminologis, metodologis, dan konseptual antara **Draft Diktat Kuliah Pemrograman Fungsional (Revisi Februari 2014)** karya _Inggriani Liem_ dengan **Diktat Pemrograman Fungsional (Agustus 2026)** yang disusun ulang oleh _Satrio Adi Rukmono_.

### 1. Perubahan Peran dan Penataan Dokumen

- **Peralihan Bentuk Publikasi:** Diktat tidak lagi difungsikan sebagai "Diktat Kuliah" yang terikat pada kurikulum mata kuliah spesifik (misal: IF2030 / IF1210 ITB), melainkan direstrukturisasi menjadi **Modul Bebas-Bahasa** yang dapat dipetakan secara fleksibel ke CPMK mata kuliah manapun.

- **Pemisahan Tubuh Diktat dan Suplemen Eksekusi:**

  - **Versi 2014:** Menggabungkan Notasi Fungsional (Buku I) dan aturan translasi LISP (Buku II) secara langsung.

  - **Versi 2026:** Memisahkan tubuh diktat (konseptual/bebas-bahasa) secara tegas dari **Suplemen Eksekusi**.

- **Bahasa Acuan Penerjemahan:** Bahasa acuan eksekusi diperbarui dari **LISP/CGLISP** menjadi **Haskell**, guna menyesuaikan dengan bahasa acuan yang secara riil dipakai di beberapa tahun terakhir, bahasa yang lazim digunakan di industri pada era ini, dengan bonus fitur _static type checking_ dan _pattern matching_ eksplisit yang memiliki nilai pedagogis.

### 2. Penyesuaian Terminologi dan Konvensi Notasi

Untuk menyelaraskan dengan perkembangan paradigma pemrograman fungsional arus utama, dilakukan penyesuaian terminologi dasar:

| Topik / Elemen                 | Versi 2014 (Lama)                                          | Versi 2026 (Baru)                                                        | Alasan Perubahan                                                                                                              |
| ------------------------------ | ---------------------------------------------------------- | ------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| **List Tanpa Elemen Terakhir** | `Head(L)`                                                  | Tidak ada                                                                | Istilah `Head` pada versi lama berkebalikan dengan konvensi bahasa fungsional modern (di mana `head` berarti elemen pertama). |
| **Operasi Boolean**            | `and`, `or`                                                | `and`, `or`, `and then`, `or else`                                       | `and then` dan `or else` ditambahkan sebagai operasi *short circuit* untuk menyederhanakan berbagai ekspresi kondisional.     |
| **Elemen Pertama List**        | `FirstElmt(L)`                                             | `head(L)`                                                                | Menyesuaikan konvensi standar (`head`/`tail`).                                                                                |
| **Penamaan Predikat**          | Menggunakan akhiran `?` (misal: `IsOrigin?`, `IsPositiv?`) | Menggunakan awalan `is` dan _camelCase_ (misal: `isOrigin`, `isPositiv`) | Menghilangkan ambiguitas nama serta menyelaraskan dengan konvensi modern.                                                     |
| **Simbol Deklarasi/Definisi**  | Menggunakan `=` atau `:` secara bergantian                 | Menggunakan `:` untuk definisi, `=` khusus untuk perbandingan relasional | Mencegah ambiguitas antara penugasan/definisi dengan perbandingan logika.                                                     |
| **Nama Antara**                | Digabung dalam sintaks lokal `let ... in`                  | Tetap `let ... in` dengan aturan pengikatan eksplisit dari atas ke bawah | Mempertegas urutan _scope_ lokal tanpa memperkenalkan efek samping.                                                           |

### 3. Perubahan Struktural dan Urutan Bab

- **Bab Rekursi vs. Fungsi Orde Tinggi (High-Order Function):**

  - **Versi 2014:** Membahas Ekspresi Lambda / Fungsi Orde Tinggi di bagian akhir bab (Bab 11) setelah Pohon.

  - **Versi 2026:** Memindahkan Fungsi Orde Tinggi ke **Bab 8**, tepat setelah Bab List (Bab 7) dan **sebelum** Pohon (Bab 10). Mahasiswa dilatih membuat fungsi rekursi manual terlebih dahulu, baru diajarkan mengabstraknya dengan `map`, `filter`, dan `fold`.

- **Penambahan Bab Khusus "Dari Notasi ke Program" (Bab 9):**

  - Bab baru yang secara eksplisit menjembatani antara rancangan statis fungsional dengan realitas eksekusi mesin: membahas _kemurnian (purity)_, _transparansi referensial_, _efek samping (side-effects)_, serta strategi evaluasi (_lazy vs. strict_).

- **Reorganisasi Topik Pohon dan List of List:**

  - **Versi 2014:** Tabel, Rekursi, List, Pohon, Lambda.

  - **Versi 2026:** Pohon (Bab 10) dan List of List (Bab 11) ditempatkan sebagai **bab pengayaan/opsional** di akhir diktat untuk jembatan ke struktur data imperatif.

### 4. Pembaharuan Metodologis dan Pedagogis

1. **Syarat Analisis Kasus (`depend on`):**

   - **Versi 2014:** Mengizinkan evaluasi implisit atau bergantung pada urutan eksekusi _interpreter_.

   - **Versi 2026:** Menegaskan aturan **Lengkap (Exhaustive)** dan **Disjoint (Mutually Exclusive)** secara formal. Urutan penulisan kasus pada notasi `depend on` dinyatakan **tidak mempengaruhi arti/hasil**.

2. **Penanganan Kasus Konstruktor List Tambahan:**

   - **Versi 2014:** Menyediakan primitif konstruktor tambah-kiri (`Konso`) dan tambah-kanan (`Kons⏺`) secara sejajar.

   - **Versi 2026:** Hanya mengakui **satu konstruktor primitif** (`makeCons` / `:`). Penambahan di akhir list (`addLast`) diturunkan sebagai fungsi biasa untuk memperlihatkan kompleksitas/harga eksekusinya.

3. **Formalisasi Kewajiban Rekurens:**

   - Penulisan fungsi rekursif wajib mencantumkan dua komponen secara terbuka di bagian spesifikasi: **Kondisi Basis** dan **Konvergensi Rekurens** (besaran yang mengecil beserta batas bawahnya).

4. **Pendekatan Type Bentukan (Product & Alternative):**

   - Mempertegas perbedaan konseptual antara **Type Produk** (tuple/rekord) dengan **Type Alternatif** (varian/union).

   - Menegaskan prinsip _abstraksi data_: seluruh fungsi luar wajib mengakses komponen type bentukan **hanya melalui selektor** dan membentuk nilai **hanya melalui konstruktor**.
