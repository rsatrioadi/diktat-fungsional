# Ekspresi Dasar dan Evaluasi

## Ekspresi

**Ekspresi** adalah teks yang tersusun dari nama, konstanta, operator, aplikasi fungsi, dan tanda kurung, yang bila dievaluasi menghasilkan sebuah nilai.

Pada bab sebelumnya, realisasi setiap fungsi berupa sebuah ekspresi. Bab ini membahas bentuk ekspresi yang paling dasar: yang hanya memakai operator dan fungsi yang sudah tersedia, beserta cara menelusuri evaluasinya.

Sebagai titik awal, dinyatakan bahwa notasi ini menyediakan tiga kelompok operator dan sejumlah kecil fungsi dasar. Segala sesuatu yang lain harus didefinisikan sendiri.

## Operator Dasar

### Operator aritmatika

| Operator | Domain | Range | Keterangan |
|---|---|---|---|
| `+` `-` `*` | `Integer` atau `Real` | sama dengan operan | penjumlahan, pengurangan, perkalian |
| `-` (uner) | `Integer` atau `Real` | sama dengan operan | negasi, ditulis `-x` |
| `/` | `Real` | `Real` | pembagian riil |
| `div` | `Integer` | `Integer` | hasil bagi bulat |
| `mod` | `Integer` | `Integer` | sisa bagi |

Kedua operan sebuah operator aritmatika harus bertype sama. Notasi ini tidak mengenal konversi otomatis antara `Integer` dan `Real`: bila keduanya perlu dicampur, konversi harus dinyatakan secara eksplisit.

### Operator relasional

| Operator | Domain | Range |
|---|---|---|
| `=` `≠` | dua nilai bertype sama | `Boolean` |
| `<` `>` `≤` `≥` | `Integer`, `Real`, atau `Character` | `Boolean` |

Perhatikan bahwa `=` di sini berarti **perbandingan**, bukan definisi. Notasi ini memakai `:` untuk definisi, sehingga `=` tidak pernah bermakna ganda. Beberapa bahasa pemrograman tidak seberuntung itu, dan harus memakai dua lambang yang berbeda.

### Operator Boolean

| Operator | Domain | Range |
|---|---|---|
| `and` `or` | dua `Boolean` | `Boolean` |
| `not` | satu `Boolean` | `Boolean` |

Operan `not` **selalu** dituliskan di dalam kurung: `not (x < y)`. Aturan ini dibuat agar tidak ada keraguan tentang sejauh mana negasi berlaku, dan agar teks tetap terbaca sama oleh pembaca yang terbiasa dengan bahasa pemrograman yang berbeda-beda.

## Fungsi Dasar

Selain operator di atas, diasumsikan tersedia sejumlah kecil fungsi dasar:

```
abs    : Integer → Integer    { abs (x) adalah nilai mutlak x }
sqrt   : Real → Real          { sqrt (x) adalah akar kuadrat x. Prasyarat: x ≥ 0.0 }
asReal : Integer → Real       { asReal (x) adalah nilai x sebagai bilangan riil }
pi     : → Real               { pi adalah konstanta matematika π }
```

Daftar ini sengaja dibuat pendek. Setiap fungsi lain yang dibutuhkan (bahkan yang sesederhana `max2`) harus didefinisikan sendiri, karena mendefinisikannya adalah latihan yang justru ingin diberikan.

Dua di antaranya ada karena memang tidak dapat didefinisikan sendiri. `asReal` adalah satu-satunya jalan menyeberang dari `Integer` ke `Real`; fungsi ini diperlukan karena notasi ini tidak mengenal konversi otomatis. Tidak ada kebalikannya: menyeberang dari `Real` ke `Integer` menuntut keputusan pembulatan, dan keputusan itu harus dituliskan sebagai fungsi tersendiri beserta spesifikasinya. `pi` adalah sebuah nilai, bukan sebuah perhitungan, dan menuliskannya sebagai `3.14159` di banyak tempat hanya memperbanyak kesempatan salah ketik.

Dalam Haskell, `asReal` bersesuaian dengan `fromIntegral` dan `pi` tersedia dengan nama yang sama.

## Presedensi

Bila sebuah ekspresi mengandung beberapa operator tanpa kurung, urutan evaluasinya ditentukan oleh presedensi:

| Presedensi | Operator |
|---|---|
| tertinggi | aplikasi fungsi |
| | `-` uner |
| | `*` `/` `div` `mod` |
| | `+` `-` |
| | `=` `≠` `<` `>` `≤` `≥` |
| terendah | `and` `or` |

Contoh: `3 + 4 * 5` dievaluasi sebagai `3 + (4 * 5)`, menghasilkan `23`.

Aturan presedensi ada supaya ekspresi tetap ringkas, bukan supaya kurung boleh dihilangkan. Bila sebuah ekspresi mulai sulit dibaca, tambahkan kurung; pembaca berikutnya tidak wajib menghafal tabel di atas. Untuk `and` dan `or` yang bercampur dalam satu ekspresi, kurung **wajib** dituliskan.

## Jenis Ekspresi

Ekspresi digolongkan menurut type nilai yang dihasilkannya:

- **Ekspresi numerik** menghasilkan `Integer` atau `Real`. Contoh: `(a + b) div 2`.
- **Ekspresi Boolean** menghasilkan `Boolean`. Contoh: `(x > 0) and (y > 0)`.
- **Ekspresi relasional** adalah ekspresi Boolean yang operator terluarnya relasional dan operannya numerik. Contoh: `a * a ≤ 100`. Ini adalah kasus khusus ekspresi Boolean, bukan jenis yang terpisah.

Fungsi yang menghasilkan `Boolean` disebut **predikat**, dan namanya diawali `is` sesuai konvensi pada Bab 2.

Operator biner pada notasi ini selalu ditulis **infix** (di antara kedua operannya) karena bentuk itulah yang paling dekat dengan kebiasaan menulis matematika. Bentuk penulisan lain dimungkinkan:

| Bentuk | Ekspresi numerik | Ekspresi relasional |
|---|---|---|
| Infix | `(3 + 4) * 5` | `3 < 5` |
| Prefix | `* (+ 3 4) 5` | `< 3 5` |
| Postfix | `3 4 + 5 *` | `3 5 <` |

Bentuk prefix dan postfix tidak memerlukan kurung maupun presedensi, dan karena itu lebih mudah diproses mesin. Keduanya akan muncul kembali sebagai representasi pohon ekspresi.

## Prasyarat dan Operator Parsial

Beberapa operator dan fungsi tidak terdefinisi untuk seluruh domainnya:

| Ekspresi | Tidak terdefinisi bila |
|---|---|
| `a div b`, `a mod b` | `b = 0` |
| `a / b` | `b = 0.0` |
| `sqrt (x)` | `x < 0.0` |

Bila sebuah fungsi yang kita tulis memakai salah satunya, keterbatasan itu **harus** dinyatakan sebagai prasyarat pada spesifikasi:

```
JUDUL   Rata-Rata Dua Bilangan Bulat
```

```
DEFINISI DAN SPESIFIKASI
    average2 : Integer, Integer → Integer
    { average2 (a, b) adalah rata-rata a dan b, dibulatkan ke bawah }
```

```
REALISASI
    average2 (a, b) : (a + b) div 2
```

Fungsi ini tidak memerlukan prasyarat: pembaginya konstanta `2`, sehingga tidak pernah nol. Bandingkan dengan:

```
JUDUL   Rata-Rata Sebagian
```

```
DEFINISI DAN SPESIFIKASI
    averagePart : Integer, Integer → Integer
    { averagePart (jumlah, n) adalah rata-rata dari n buah nilai yang
      jumlahnya adalah jumlah, dibulatkan ke bawah.
      Prasyarat: n > 0 }
```

```
REALISASI
    averagePart (jumlah, n) : jumlah div n
```

Prasyarat bukan sekadar catatan, melainkan bagian dari kontrak: pemakai fungsi berjanji memenuhinya, dan sebagai imbalannya penulis fungsi tidak perlu memeriksanya. Menuliskan prasyarat sejak awal juga memaksa kita memikirkan kembali apakah **domain** sudah dipilih dengan tepat. Kadang ternyata domainnya yang seharusnya dipersempit, bukan prasyaratnya yang ditambahkan.

## Contoh 1: Ekspresi Numerik dengan Fungsi Antara

**Persoalan.** Tuliskan sebuah fungsi yang menghitung jarak antara dua titik pada bidang, yang masing-masing dinyatakan oleh absis dan ordinatnya.

Jarak antara titik \\( (x_1, y_1) \\) dan \\( (x_2, y_2) \\) adalah \\( \sqrt{(x_1-x_2)^2 + (y_1-y_2)^2} \\). Bentuk \\( (a-b)^2 \\) muncul dua kali, sehingga pantas dijadikan fungsi antara.

```
JUDUL   Jarak Dua Titik pada Bidang
```

```
DEFINISI DAN SPESIFIKASI
    sqrDif : Real, Real → Real
    { sqrDif (a, b) adalah kuadrat dari selisih a dan b }

    distance : Real, Real, Real, Real → Real
    { distance (x1, y1, x2, y2) adalah jarak antara titik (x1, y1)
      dan titik (x2, y2) pada bidang }
    { Fungsi antara yang dipakai: sqrDif }
```

```
REALISASI
    sqrDif (a, b) : (a - b) * (a - b)

    distance (x1, y1, x2, y2) : sqrt (sqrDif (x1, x2) + sqrDif (y1, y2))
```

```
APLIKASI
    ⇒ distance (1.0, 3.0, 5.0, 6.0)
      5.0
    ⇒ distance (2.0, 2.0, 2.0, 2.0)
      0.0
```

Perhatikan bahwa `distance` tidak memerlukan prasyarat meskipun memakai `sqrt`: jumlah dua bilangan kuadrat tidak mungkin negatif. Alasan semacam ini pantas dituliskan sebagai komentar bila tidak segera terlihat.

Perhatikan pula bahwa `square` dari Bab 2 **tidak** dapat dipakai di sini. Domainnya `Integer`, sedangkan yang hendak dikuadratkan adalah `Real`, dan notasi ini tidak menyeberangkan keduanya diam-diam. Aturan yang tampak rewel di awal bab baru terasa gunanya di sini: ini memaksa perbedaan itu muncul pada saat menulis, bukan pada saat menjalankan.

Dalam Haskell:

```haskell
sqrDif :: Double -> Double -> Double
-- sqrDif a b adalah kuadrat dari selisih a dan b
sqrDif a b = (a - b) * (a - b)

distance :: Double -> Double -> Double -> Double -> Double
-- distance x1 y1 x2 y2 adalah jarak antara titik (x1,y1) dan (x2,y2)
distance x1 y1 x2 y2 = sqrt (sqrDif x1 x2 + sqrDif y1 y2)
```

## Nama Antara

Sebuah ekspresi antara yang dipakai lebih dari satu kali, atau yang cukup panjang sehingga menyulitkan pembacaan, dapat diberi **nama antara**. Nama ini hanya berlaku di dalam realisasi tempat ia dituliskan, dan tidak dikenal di luarnya.

Bentuk penulisannya:

```
REALISASI
    namaFungsi (x) :
        let
            n1 : *ekspresi*
            n2 : *ekspresi*
        in
            *ekspresi yang memakai n1 dan n2*
```

Type sebuah nama antara tidak perlu dituliskan: type dapat disimpulkan dari ekspresinya. Nama-nama diikat berurutan dari atas ke bawah, sehingga sebuah nama boleh memakai nama yang sudah diikat di atasnya. Perhatikan bahwa `:` di sini adalah tanda yang sama dengan yang dipakai pada realisasi fungsi, dan artinya pun sama: *ini didefinisikan sebagai itu*. Notasi ini tidak pernah memakai `=` untuk mendefinisikan.

Fungsi `distance` dapat direalisasikan ulang dengan nama antara, tanpa fungsi antara:

```
REALISASI
    distance (x1, y1, x2, y2) :
        let
            dx : x1 - x2
            dy : y1 - y2
        in
            sqrt (dx * dx + dy * dy)
```

Definisi dan spesifikasinya tidak berubah sedikit pun. Ini adalah contoh kedua, setelah `cube` pada Bab 2, dari satu spesifikasi yang direalisasikan dengan dua cara berbeda.

### Nama Antara atau Fungsi Antara?

Keduanya menghilangkan pengulangan, tetapi dengan akibat yang berbeda:

| | Nama antara | Fungsi antara |
|---|---|---|
| Jangkauan | hanya di dalam satu realisasi | dapat dipakai di mana saja |
| Menambah perbendaharaan? | tidak | ya |
| Perlu definisi dan spesifikasi? | tidak | ya |
| Cocok bila | nilainya khas untuk fungsi ini | konsepnya berdiri sendiri |

Pertanyaan yang membantu memutuskan: *apakah hal ini pantas diberi nama dan dijelaskan kepada orang lain?* `sqrDif` (kuadrat selisih) adalah konsep yang berdiri sendiri dan mungkin berguna di tempat lain, sehingga pantas menjadi fungsi. `dx` hanyalah selisih absis dalam perhitungan ini, dan tidak berarti apa-apa di luar konteksnya, sehingga cukup menjadi nama antara.

Kecenderungan yang sehat bagi pemula adalah membuat terlalu banyak fungsi antara, bukan terlalu sedikit.

## Contoh 2: Ekspresi Boolean

**Persoalan.** Tuliskan sebuah predikat yang menentukan apakah suatu tahun adalah tahun kabisat. Sebuah tahun adalah kabisat bila habis dibagi 4, kecuali bila habis dibagi 100 tetapi tidak habis dibagi 400.

```
JUDUL   Tahun Kabisat
```

```
DEFINISI DAN SPESIFIKASI
    isLeapYear : Integer → Boolean
    { isLeapYear (y) bernilai true bila y adalah tahun kabisat.
      Prasyarat: y > 0 }
```

```
REALISASI
    isLeapYear (y) :
        (y mod 4 = 0) and ((y mod 100 ≠ 0) or (y mod 400 = 0))
```

```
APLIKASI
    ⇒ isLeapYear (2024)
      true
    ⇒ isLeapYear (1900)
      false
    ⇒ isLeapYear (2000)
      true
    ⇒ isLeapYear (2023)
      false
```

Keempat aplikasi di atas dipilih dengan sengaja, dan bukan sekadar diambil sembarangan: `2024` habis dibagi 4 saja, `1900` habis dibagi 100 tetapi tidak 400, `2000` habis dibagi 400, dan `2023` tidak habis dibagi 4. Bersama-sama keempatnya menyentuh setiap cabang ekspresi. Memilih contoh aplikasi dengan cara ini, satu untuk setiap kemungkinan, adalah bentuk pengujian yang paling sederhana, dan sudah dapat dilakukan sekarang, jauh sebelum ada perkakas pengujian.

Dalam Haskell:

```haskell
isLeapYear :: Integer -> Bool
-- isLeapYear y bernilai True bila y adalah tahun kabisat
-- Prasyarat: y > 0
isLeapYear y = (y `mod` 4 == 0) && ((y `mod` 100 /= 0) || (y `mod` 400 == 0))
```

## Evaluasi Ekspresi

Mengevaluasi sebuah ekspresi berarti menggantinya berulang kali dengan ekspresi yang lebih sederhana, sampai tersisa sebuah nilai. Hanya ada dua macam langkah:

| Langkah | Yang terjadi |
|---|---|
| **Ekspansi** | Aplikasi sebuah fungsi diganti oleh realisasinya, dengan setiap parameter diganti oleh argumennya |
| **Reduksi** | Sebuah operator atau fungsi dasar dihitung, menghasilkan nilai |

Berikut penelusuran lengkap `distance (1.0, 3.0, 5.0, 6.0)`, memakai realisasi dengan fungsi antara:

```
⇒ distance (1.0, 3.0, 5.0, 6.0)
  → sqrt (sqrDif (1.0, 5.0) + sqrDif (3.0, 6.0))            { ekspansi distance }
  → sqrt ((1.0 - 5.0) * (1.0 - 5.0) + sqrDif (3.0, 6.0))    { ekspansi sqrDif }
  → sqrt ((-4.0) * (-4.0) + sqrDif (3.0, 6.0))              { reduksi - }
  → sqrt (16.0 + sqrDif (3.0, 6.0))                         { reduksi * }
  → sqrt (16.0 + (3.0 - 6.0) * (3.0 - 6.0))                 { ekspansi sqrDif }
  → sqrt (16.0 + (-3.0) * (-3.0))                           { reduksi - }
  → sqrt (16.0 + 9.0)                                       { reduksi * }
  → sqrt (25.0)                                             { reduksi + }
  → 5.0                                                     { reduksi sqrt }
```

Sembilan langkah, masing-masing hanya menyentuh satu bagian ekspresi. Menelusuri secara tertulis seperti ini adalah cara paling andal untuk memahami program yang ditulis orang lain *dan* menemukan kesalahan pada program yang kita tulis sendiri.

## Urutan Evaluasi

Pada penelusuran di atas, `sqrDif (1.0, 5.0)` dievaluasi lebih dahulu. Pilihan itu tidak wajib. Kita dapat mengevaluasi `sqrDif (3.0, 6.0)` lebih dahulu:

```
⇒ distance (1.0, 3.0, 5.0, 6.0)
  → sqrt (sqrDif (1.0, 5.0) + sqrDif (3.0, 6.0))            { ekspansi distance }
  → sqrt (sqrDif (1.0, 5.0) + (3.0 - 6.0) * (3.0 - 6.0))    { ekspansi sqrDif }
  → sqrt (sqrDif (1.0, 5.0) + (-3.0) * (-3.0))              { reduksi - }
  → sqrt (sqrDif (1.0, 5.0) + 9.0)                          { reduksi * }
  → ...
  → 5.0
```

Hasilnya sama. Ini bukan kebetulan, melainkan akibat langsung dari transparansi referensial yang dibicarakan pada Bab 2: sebuah ekspresi hanya bergantung pada nilai bagian-bagiannya, tidak pada kapan bagian-bagian itu dihitung. Karena itu:

- Urutan evaluasi tidak mengubah hasil.
- Bagian-bagian yang saling bebas secara teoritis dapat dievaluasi bersamaan.
- Sebuah ekspresi dapat dipahami dengan membaca ekspresi itu saja, tanpa perlu mengetahui apa yang terjadi sebelumnya di tempat lain dalam program.

Ketiganya tidak berlaku pada program imperatif yang mengubah state, dan ketiganya adalah alasan utama mengapa paradigma ini layak dipelajari.

Notasi ini karenanya **tidak menetapkan** urutan evaluasi, karena tidak perlu. Bahasa pemrograman nyata harus menetapkannya, dan pilihan yang diambil masing-masing bahasa mempunyai akibat yang perlu diketahui pada saat program benar-benar dijalankan. Bab 9 membahas pilihan-pilihan yang tersedia, dan Suplemen S10 membahas pilihan yang diambil bahasa acuan.

## Latihan

**Membaca.** Tentukan definisi (nama, domain, range) setiap realisasi berikut, tuliskan spesifikasinya dalam bahasa Indonesia, dan sebutkan prasyaratnya bila ada. Kemudian usulkan nama yang sesuai konvensi penamaan.

```
p (a, b, c) : (a + b + c) div 3

q (x)       : (x mod 2 = 0) and (x mod 3 = 0)

r (a, b)    :
    let
        s : a + b
    in
        s * s - 4.0 * a * b

t (n)       : abs (n) = n
```

Untuk `t`, jawablah juga: apakah spesifikasi Anda lebih pendek bila dituliskan sebagai "bernilai true bila …" ketimbang menyalin ulang ekspresinya? Spesifikasi yang baik menyatakan **arti**, bukan mengulang realisasi dengan kata-kata.

**Menulis.** Untuk setiap persoalan berikut, tuliskan keempat bagian secara lengkap. Nyatakan prasyarat bila ada. Pilih contoh aplikasi yang menyentuh setiap kemungkinan, tuliskan hasil yang Anda harapkan, lalu terjemahkan dan jalankan.

1. Menentukan apakah tiga bilangan bulat positif dapat menjadi panjang sisi sebuah segitiga.
2. Menghitung jumlah menit dari sebuah durasi yang dinyatakan dalam jam dan menit, misalnya 2 jam 30 menit menjadi 150.
3. Kebalikan dari nomor 2: menghitung banyaknya jam penuh dari sejumlah menit.
4. Menentukan apakah sebuah bilangan bulat merupakan kuadrat sempurna. Gunakan `sqrt`, dan pikirkan baik-baik prasyaratnya.
5. Menghitung keliling sebuah segitiga dari koordinat ketiga titik sudutnya. Gunakan kembali `distance`.

**Menelusuri.** Tuliskan penelusuran lengkap `isLeapYear (1900)`, dengan menyebutkan pada setiap langkah apakah yang terjadi ekspansi atau reduksi. Kemudian jawab: pada langkah keberapa hasil akhirnya sudah dapat dipastikan, meskipun ekspresinya belum tersisa satu nilai?

**Merancang.** Fungsi `distance` ditulis dengan dua realisasi berbeda pada bab ini. Untuk persoalan *Menulis* nomor 5 di atas, mana yang Anda pilih sebagai dasar, dan mengapa? Tidak ada jawaban yang salah, yang dinilai adalah alasannya.
