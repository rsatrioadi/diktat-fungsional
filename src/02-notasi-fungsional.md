# Notasi Fungsional

## Program sebagai Model

Sebuah program adalah **model**: representasi dari solusi suatu persoalan, yang ditulis agar dapat diproses oleh mesin dan dibaca oleh manusia.

Dalam paradigma fungsional, solusi persoalan dinyatakan sebagai **identifikasi sekumpulan fungsi**, yang bila diaplikasikan terhadap nilai masukan akan menghasilkan nilai yang diharapkan. Program terdiri dari:

- **Type**, yaitu himpunan nilai beserta operasi yang terdefinisi terhadapnya;
- **Fungsi**, yaitu pemetaan dari satu type (*domain*) ke type lain (*range*).

Domain dan range dapat berupa type dasar (bilangan, karakter, boolean) atau type bentukan yang disusun dari type lain. Tidak ada konsep lain yang perlu ditambahkan: tidak ada variabel yang berubah nilai, tidak ada urutan instruksi, dan tidak ada perbedaan mendasar antara "data" dan "program".

Untuk menyusun fungsi dipakai **ekspresi**. Ada tiga bentuk komposisi ekspresi, dan ketiganya dibahas berturut-turut pada Bab 3, 4, dan 6:

| Bentuk | Dibahas pada |
|---|---|
| Ekspresi dasar (aritmatika, relasional, boolean) | Bab 3 |
| Ekspresi kondisional | Bab 4 |
| Ekspresi rekursif | Bab 6 |

## Perbandingan dengan Program Imperatif

Bagian ini ditujukan bagi pembaca yang sudah pernah memrogram secara prosedural. Pembaca yang belum, dapat melewatinya tanpa kehilangan alur.

Perhatikan sebuah program imperatif yang membaca dua bilangan dan menuliskan jumlahnya:

```
PROGRAM PLUSAB
{ Membaca dua nilai a dan b, menghitung dan menuliskan jumlahnya }
```

```
KAMUS
    a, b : Integer
```

```
ALGORITMA
    input(a, b)
    output(a + b)
```

Program ini terdiri dari **aksi** yang dilakukan berurutan: menunggu masukan, menghitung, mencetak. Ada *state*: nilai `a` dan `b` tersimpan di suatu tempat, dan ada *waktu*: sesuatu terjadi sebelum sesuatu yang lain.

Padanan fungsionalnya tidak mempunyai keduanya. Yang ada hanya ekspresi dan nilainya:

```
APLIKASI
⇒ 3 + 4
  7
```

Aksi membaca dan menulis diwakili oleh interaksi pemakai dengan lingkungan eksekusi, bukan oleh program. Program itu sendiri hanyalah `+`.

Perbedaan ini bukan sekadar soal penulisan. Dalam program imperatif, untuk mengetahui nilai sebuah nama, kita harus tahu *kapan* nama itu diperiksa; nilainya dapat berubah. Dalam program fungsional, sebuah nama yang telah terikat pada suatu nilai akan bernilai sama di mana pun dan kapan pun ia muncul. Sifat ini disebut **transparansi referensial**, dan darinyalah datang sebagian besar keuntungan paradigma ini: ekspresi dapat digantikan oleh nilainya tanpa mengubah arti program, sehingga penalaran terhadap program menjadi jauh lebih sederhana.

## Konstruksi Program Fungsional

Pemrograman fungsional bertumpu pada analisis *top-down*: dari persoalan, ke spesifikasi, lalu ke dekomposisi menjadi persoalan-persoalan antara. Setiap kali kita mendefinisikan sebuah fungsi antara, kita menambah satu tingkat abstraksi sekaligus memperkaya perbendaharaan fungsi yang tersedia untuk dipakai kembali.

Sebuah fungsi dikonstruksi dalam empat tahap yang **selalu** dituliskan dengan urutan yang sama:

| Tahap | Pertanyaan yang dijawab | Isi |
|---|---|---|
| **Definisi** | Fungsi ini memetakan *apa* ke *apa*? | Nama, domain, dan range |
| **Spesifikasi** | *Apa* arti fungsi ini? | Penjelasan makna, prasyarat, dan kasus batas |
| **Realisasi** | *Bagaimana* fungsi ini menghitung? | Ekspresi fungsional |
| **Aplikasi** | Bagaimana fungsi ini *dipakai*? | Contoh pemakaian beserta hasilnya |

Definisi dan spesifikasi ditulis berdampingan karena keterkaitannya sangat erat: yang satu menyatakan bentuk, yang lain menyatakan makna.

Perhatikan bahwa **definisi dan spesifikasi tidak mengandaikan realisasi tertentu**. Satu definisi dan spesifikasi yang sama dapat direalisasikan oleh beberapa ekspresi yang berbeda. Inilah yang membuat keduanya berguna: pemakai sebuah fungsi cukup membaca definisi dan spesifikasinya, tanpa perlu membaca realisasinya.

Nama-nama yang muncul pada sisi kiri realisasi disebut **parameter**. Nilai yang diberikan pada saat aplikasi disebut **argumen**.

## Bentuk Umum Teks Fungsi

Sebuah teks fungsi terdiri dari judul dan empat bagian:

```
JUDUL   Judul Fungsi
```

```
DEFINISI DAN SPESIFIKASI
    namaFungsi : Domain → Range
    { namaFungsi (p) adalah ... 
      Prasyarat: ...  }
```

```
REALISASI
    namaFungsi (p) : *ekspresi fungsional*
```

```
APLIKASI
    ⇒ namaFungsi (*argumen*)
      *hasil*
```

Judul adalah deskripsi sangat ringkas yang memudahkan pencarian, bukan bagian dari program.

Komentar dituliskan di antara kurung kurawal `{ }`.

Perhatikan bahwa pada realisasi tidak ada kata kunci semacam `function` atau `def`. Yang dituliskan hanyalah persamaan: nama beserta parameternya di sebelah kiri, ekspresi di sebelah kanan. Sebuah **konstanta** ditulis dengan bentuk yang sama, hanya tanpa parameter, dan memang, konstanta dapat dipandang sebagai fungsi tanpa argumen.

## Contoh 1: Ekspresi Numerik Sederhana

**Persoalan.** Tuliskan sebuah fungsi yang menerima sebuah bilangan bulat dan menghasilkan pangkat duanya.

```
JUDUL   Pangkat Dua
```

```
DEFINISI DAN SPESIFIKASI
    square : Integer → Integer
    { square (x) adalah nilai x dipangkatkan dua }
```

```
REALISASI
    square (x) : x * x
```

```
APLIKASI
    ⇒ square (5)
      25
    ⇒ square (0)
      0
    ⇒ square (-3)
      9
```

Dalam Haskell, definisi, spesifikasi, dan realisasi di atas diterjemahkan menjadi:

```haskell
square :: Integer -> Integer
-- square x adalah nilai x dipangkatkan dua
square x = x * x
```

Sementara aplikasi fungsi adalah sebagai berikut:

```haskell
square 5
```

## Contoh 2: Dekomposisi dengan Fungsi Antara

**Persoalan.** Tuliskan sebuah fungsi yang menghasilkan pangkat tiga dari sebuah bilangan bulat.

Realisasi paling langsung adalah `x * x * x`. Namun kita sudah mempunyai `square`, dan menggunakannya kembali menghasilkan teks yang lebih menyatakan maksud:

```
JUDUL   Pangkat Tiga
```

```
DEFINISI DAN SPESIFIKASI
    cube : Integer → Integer
    { cube (x) adalah nilai x dipangkatkan tiga }
    { Fungsi antara yang dipakai: square }
```

```
REALISASI
    cube (x) : x * square (x)
```

```
APLIKASI
    ⇒ cube (2)
      8
    ⇒ cube (-1)
      -1
```

Dalam Haskell:

```haskell
cube :: Integer -> Integer
-- cube x adalah nilai x dipangkatkan tiga
cube x = x * square x
```

Dua realisasi yang berbeda (`x * x * x` dan `x * square (x)`) memenuhi definisi dan spesifikasi yang sama. Keduanya benar. Pemilihan di antara keduanya adalah keputusan rancangan, bukan keputusan kebenaran.

## Contoh 3: Fungsi dengan Dua Parameter

**Persoalan.** Tuliskan sebuah fungsi yang menghasilkan nilai terbesar dari dua bilangan bulat.

```
JUDUL   Maksimum dari 2 Bilangan
```

```
DEFINISI DAN SPESIFIKASI
    max2 : Integer, Integer → Integer
    { max2 (a, b) adalah nilai terbesar di antara a dan b.
      Jika a = b, menghasilkan nilai tersebut. }
```

```
REALISASI
    max2 (a, b) : if a ≥ b then 
                    a 
                  else 
                    b
```

```
APLIKASI
    ⇒ max2 (3, 7)
      7
    ⇒ max2 (4, 4)
      4
```

Dalam Haskell, definisi, spesifikasi, dan realisasi di atas diterjemahkan menjadi:

```haskell
max2 :: Integer -> Integer -> Integer
-- max2 a b adalah nilai terbesar di antara a dan b
-- Jika a = b, menghasilkan nilai tersebut
max2 a b = if a >= b then a else b
```

Sementara aplikasi fungsi adalah sebagai berikut:

```haskell
max2 3 7
```

- Perhatikan domain yang ditulis sebagai `Integer -> Integer -> Integer`, bukan `(Integer, Integer) -> Integer`. Bacalah untuk sementara sebagai "menerima dua Integer dan menghasilkan sebuah Integer". Alasan mengapa panah ditulis dua kali, dan apa yang jadi mungkin dilakukan karenanya, dibahas pada Bab 8.
- Aplikasi ditulis dengan menderetkan argumen setelah nama fungsi, tanpa kurung dan tanpa koma. Kurung hanya dipakai untuk mengelompokkan ekspresi, misalnya `max2 (3 + 1) 7` atau `cube (-1)`.

## Acuan Notasi (sejauh Bab 2)

Notasi ini belum lengkap pada bab ini. Bab-bab berikutnya menambahkan `let` (Bab 3), `depend on`, `else`, `and then`, `or else` (Bab 4), serta `type`, tuple `⟨…⟩`, dan pembatasan domain seperti `Integer [1..12]` (Bab 4 dan 5). Setiap tambahan diperkenalkan di tempatnya dan dirangkum kembali pada akhir bab yang bersangkutan.

### Type dasar

| Notasi | Haskell | Keterangan |
|---|---|---|
| `Integer` | `Integer` | bilangan bulat, tanpa batas |
| `Real` | `Double` | bilangan riil |
| `Boolean` | `Bool` | nilai `true` / `false` → `True` / `False` |
| `Character` | `Char` | karakter tunggal |

### Operator

| Notasi | Haskell | Perhatian |
|---|---|---|
| `+ - *` | `+ - *` | |
| `/` | `/` | hanya untuk `Real` |
| `div`, `mod` | `` `div` ``, `` `mod` `` | pembagian dan sisa untuk `Integer` |
| `<` `>` | `<` `>` | |
| `≤` `≥` | `<=` `>=` | |
| `=` *(perbandingan)* | `==` | |
| `≠` | `/=` | |
| `and` `or` `not` | `&&` `\|\|` `not` | |

### Struktur

| Notasi | Haskell |
|---|---|
| `namaFungsi : A → B` | `namaFungsi :: A -> B` |
| `namaFungsi : A, B → C` | `namaFungsi :: A -> B -> C` |
| `namaKonstanta : → A` | `namaKonstanta :: A` |
| `namaFungsi (x) : e` | `namaFungsi x = e` |
| `namaFungsi (x, y) : e` | `namaFungsi x y = e` |
| `namaFungsi (x, y)` { aplikasi fungsi } | `namaFungsi x y` |
| `if c then e1 else e2` | `if c then e1 else e2` |
| `{ komentar }` | `-- komentar` atau `{- komentar -}` |
| `⇒ ekspresi` | ketik pada GHCi |

**Catatan.** Koma pada `A, B → C` menandakan bahwa fungsi mempunyai dua parameter. Terjemahannya ke Haskell memecah koma menjadi panah, sehingga jumlah panah bertambah. Lihat Contoh 3 dan Bab 8.

Untuk konstanta, panah pada `→ A` hilang dalam terjemahan. Notasi fungsional menekankan bahwa fungsi adalah pemetaan suatu kumpulan nilai ke kumpulan nilai lainnya; tanda panah menunjukkan makna "pemetaan". Haskell tidak membedakan antara "nilai bertype `A`" dan "fungsi tanpa parameter yang menghasilkan `A`".

## Konvensi Penamaan

Konvensi berikut berlaku di seluruh diktat, **tanpa kecuali**. Konsistensi penamaan bukan soal selera: nama yang dapat ditebak mengurangi beban ingatan pembaca, dan penyimpangan yang tidak disengaja adalah sumber kesalahan yang nyata.

| Jenis | Aturan | Contoh |
|---|---|---|
| Fungsi | huruf kecil di awal, `punukUnta` bila lebih dari satu kata | `square`, `divMod` |
| Type | huruf besar di awal | `Integer`, `Point`, `Fraction`, `List` |
| Predikat | awalan `is`, menghasilkan `Boolean` | `isEmpty`, `isMember`, `isOrigin` |
| Konstruktor | awalan `make` | `makePoint`, `makeFraction` |
| Selektor | nama komponen yang diambil | `x`, `y`, `numerator` |
| Konversi | awalan `to` | `toReal`, `toSeconds` |
| Nama antara | pendek, hanya berlaku lokal | `dx`, `dy`, `remD` |

Tambahan yang perlu diperhatikan:

- **Tidak ada spasi dan tidak ada tanda hubung dalam nama.** Diktat terdahulu menuliskan *placeholder* `Nama-Fungsi` dan nama seperti `Pangkat Tiga`; nama semacam itu ambigu dan tidak dipakai lagi.
- **Tanda tanya tidak dipakai pada nama predikat.** Diktat terdahulu menuliskan `IsOrigin?` dan `IsPos?` secara tidak konsisten dengan `IsEmpty` dan `IsMember`. Awalan `is` sudah cukup menandakan bahwa hasilnya boolean.
- **Nama ditulis dalam bahasa Inggris, penjelasan dalam bahasa Indonesia.** Alasannya praktis: pembaca akan membaca dan menulis kode nyata, dan pustaka standar bahasa apa pun memakai bahasa Inggris. Nama yang sudah mapan dalam persoalan tertentu boleh dipertahankan.

## Yang Dapat dan Tidak Dapat Dinyatakan oleh Notasi Ini

Notasi ini dirancang untuk menuliskan **rancangan** program agar mudah dibaca manusia. Notasi ini tidak mempunyai pemroses bahasa, dan karena itu tidak dapat dieksekusi. Justru itulah gunanya: notasi memaksa penulisnya menyatakan definisi dan spesifikasi lebih dahulu, sebelum realisasi. Ini mudah dilewati bila kita langsung mengetik di depan mesin.

Namun perlu dinyatakan bahwa notasi ini **tidak** mencakup seluruh konsep pada pemrograman fungsional. Beberapa hal berikut berada di luar jangkauannya, dan bila diperlukan akan dibicarakan langsung dalam bahasa acuan:

- Strategi evaluasi: notasi tidak menyatakan urutan atau kemalasan evaluasi.
- Efek, masukan, dan keluaran: dibicarakan pada Bab 9.
- Modularitas: pustaka, ruang nama, dan kompilasi terpisah.

Notasi ini adalah alat berpikir, bukan bahasa pemrograman. Kekurangannya justru menjadi kelebihannya.

## Alur Kerja yang Disarankan

Untuk setiap persoalan:

1. Tuliskan **definisi**: nama, domain, range. Tanyakan pada diri sendiri apakah domain sudah tepat? Apakah semua nilai dalam domain itu benar-benar boleh diterima?
2. Tuliskan **spesifikasi** dalam bahasa Indonesia yang jelas, termasuk prasyarat dan kasus batas.
3. Baru kemudian tuliskan **realisasi**.
4. Tuliskan **aplikasi** beserta hasil yang Anda harapkan sebelum menjalankannya di mesin.
5. Jalankan dalam bahasa acuan, dan bandingkan hasil sesungguhnya dengan yang Anda harapkan pada langkah 4.

Langkah 4 dan 5 adalah bentuk pengujian yang paling sederhana. Menuliskan hasil yang diharapkan lebih dahulu mengubah eksekusi dari "mari lihat apa yang keluar" menjadi "mari periksa apakah dugaan saya benar", dan hanya yang kedua yang mengajarkan sesuatu ketika hasilnya berbeda.

## Latihan

**Membaca.** Untuk setiap realisasi berikut, tentukan definisi (nama, domain, range) dan tuliskan spesifikasinya dalam bahasa Indonesia. Jangan mengubah realisasinya.

```
f (a, b) : (a + b) div 2
g (x)    : x - (x div 10) * 10
h (a, b) : a mod b = 0
```

**Menulis.** Untuk setiap persoalan berikut, tuliskan keempat bagian secara lengkap, lalu terjemahkan dan jalankan.

1. Menghitung luas sebuah lingkaran dari jari-jarinya.

    Untuk latihan ini, perhatikan contoh bagaimana konstanta dapat dituliskan dalam notasi fungsional, yang mengilustrasikan pernyataan sebelumnya bahwa konstanta serupa dengan fungsi tanpa parameter. (Mulai Bab 3, `pi` tersedia sebagai konstanta dasar dan tidak perlu dituliskan lagi.)

    ```
    DEFINISI DAN SPESIFIKASI
        pi : → Real
        { pi adalah konstanta matematika π. }
    ```
    
    ```
    REALISASI
        pi : 3.14159
    ```

2. Menentukan apakah sebuah bilangan bulat genap. Namai predikat Anda sesuai konvensi.
3. Menghitung nilai terbesar dari **tiga** bilangan bulat, dengan menggunakan kembali `max2`.
4. Mengubah suhu dari derajat Celcius ke derajat Fahrenheit.

**Menelusuri.** Untuk `cube` pada Contoh 2, tuliskan langkah demi langkah evaluasi `cube (4)` hingga menghasilkan sebuah nilai. Sebutkan pada setiap langkah apakah yang terjadi adalah *ekspansi* (mengganti nama fungsi dengan realisasinya) atau *reduksi* (menghitung hasil sebuah operator).
