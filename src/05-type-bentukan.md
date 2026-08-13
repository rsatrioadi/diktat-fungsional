# Type Bentukan

## Persoalan yang Melahirkannya

Sampai bab ini, setiap fungsi yang ditulis hanya menerima dan menghasilkan nilai bertype dasar: `Integer`, `Real`, `Boolean`, `Character`. Pembatasan itu sudah mulai terasa.

Pada Bab 4 ditulis `quadrant : Real, Real → Integer [1..4]`. Dua parameter `Real` itu sesungguhnya bukan dua bilangan yang berdiri sendiri; keduanya adalah satu titik. Tidak ada dalam teksnya yang menyatakan hal itu. Yang menyatakannya hanyalah nama parameternya, `x` dan `y`, dan itu pun hanya kepada pembaca manusia. Hal yang sama sudah terjadi pada Bab 3. Fungsi `distance` di sana berdefinisi `distance : Real, Real, Real, Real → Real`: empat bilangan berderet, dan tidak ada apa pun dalam teksnya yang mencegah seseorang mengaplikasikannya dengan urutan yang keliru. Bahwa keempatnya sesungguhnya dua titik hanya hidup di kepala penulisnya.

**Type bentukan** adalah type baru yang dibentuk dari type yang sudah ada, dengan memberinya nama. Type baru itu membuat gagasan "titik" menjadi sesuatu yang tertulis dalam program, bukan sekadar dimaksudkan oleh penulisnya.

Dalam berbagai paradigma dan bahasa, gagasan yang sama dikenal dengan nama *type komposisi*, *type terstruktur*, *rekord*, atau *struct*. Diktat ini memakai istilah **type bentukan**.

## Type sebagai Produk

Sebuah type adalah **himpunan nilai** beserta **sekumpulan operasi** yang terdefinisi atasnya.

Type bentukan yang paling umum dipakai adalah **produk**: nilainya terdiri dari beberapa komponen sekaligus. Sebuah titik (dengan type bentukan yang akan kita beri nama `Point`) adalah sebuah absis **dan** sebuah ordinat. Sebuah pecahan (`Fraction`) adalah sebuah pembilang (*numerator*) **dan** sebuah penyebut (*denominator*).

Himpunan nilainya adalah hasil kali kartesian himpunan nilai komponennya: dari sinilah nama "produk" berasal. Bila `Point` terdiri dari dua `Real`, maka setiap pasangan bilangan riil adalah sebuah `Point`, dan tidak ada nilai `Point` yang lain.

Nilai sebuah type bentukan dituliskan sebagai **tuple**, dengan komponen berurutan di antara kurung sudut:

```
⟨0.0, 0.0⟩          { titik pusat koordinat }
⟨1.0, 2.0⟩          { titik dengan absis 1.0 dan ordinat 2.0 }
⟨3, 4⟩              { pecahan tiga per empat }
```

Beberapa type bentukan yang sering muncul dalam informatika:

| Type | Komposisi | Makna Komponen |
|---|---|---|
| `Point` | `⟨x : Real, y : Real⟩` | absis, ordinat |
| `Complex` | `⟨re : Real, im : Real⟩` | bagian riil, bagian imajiner |
| `Fraction` | `⟨numerator : Integer, denominator : Integer > 0⟩` | pembilang, penyebut |
| `Time` | `⟨hour : Integer, minute : Integer, second : Integer⟩` | jam, menit, detik |
| `Date` | `⟨day : Integer, month : Integer, year : Integer⟩` | tanggal, bulan, tahun |

Komponen sebuah type bentukan tidak harus bertype dasar. Dari `Point` dapat dibentuk `Line` sebagai `⟨start : Point, end : Point⟩`, dan dari `Time` serta `Date` dapat dibentuk `DateTime` sebagai `⟨date : Date, time : Time⟩`. Kemampuan menyusun type di atas type membuat gagasan ini berguna melampaui contoh-contoh kecil di bab ini.

## Mendefinisikan Sebuah Type Bentukan

Mendefinisikan type bentukan dalam notasi fungsional berarti menuliskan lima hal. Empat di antaranya adalah fungsi, sesuai dengan konteks fungsional: yang dapat dituliskan hanyalah fungsi.

| Bagian | Isi | Direalisasi? |
|---|---|---|
| **Definisi type** | nama type dan komposisinya | tidak |
| **Selektor** | mengambil satu komponen dari sebuah nilai | tidak |
| **Konstruktor** | membentuk sebuah nilai dari komponen-komponennya | tidak |
| **Predikat** | menentukan karakteristik sebuah nilai | ya |
| **Operator lain** | fungsi yang berlaku atas type tersebut | ya |

**Konstruktor dan selektor tidak direalisasi.** Alasannya bukan karena keduanya sulit, melainkan karena keduanya adalah persoalan bahasa, bukan persoalan rancangan. Bagaimana sebuah pasangan nilai disimpan dan diambil kembali ditentukan sepenuhnya oleh bahasa yang dipakai; notasi hanya menyatakan bahwa keduanya *ada* dan menyatakan domain serta range-nya. Sebagaimana akan terlihat pada setiap contoh di bab ini, bahasa acuan menyediakan keduanya secara cuma-cuma begitu type-nya didefinisikan.

Kelimanya dituliskan dalam satu **teks type**, yang diawali judul `TYPE namaType`. Judul ini sejajar dengan `JUDUL` pada teks fungsi Bab 2: ia memudahkan pencarian dan bukan bagian dari program.

Konvensi penamaan pada bab ini:

- Nama type bentukan diawali huruf besar: `Point`, `Fraction`, `Date`.
- Nama konstruktor diawali `make`: `makePoint`, `makeFraction`.
- Nama selektor **sama dengan** nama komponen pada definisi type: `x`, `y`, `numerator`. Dua nama untuk satu hal yang sama tidak menambah apa-apa selain kesempatan keliru.
- Nama predikat diawali `is`, sebagaimana Bab 2: `isOrigin`, `isValidDate`.

## Prinsip yang Mendasari: Bekerja Lewat Selektor

Satu aturan berlaku di seluruh bab ini, dan sebaiknya diterima sekarang sebelum contohnya membuatnya tampak berlebihan:

> Setiap fungsi atas sebuah type bentukan mengakses komponennya **hanya melalui selektor**, dan membentuk nilainya **hanya melalui konstruktor**.

Artinya: `x (p)`, bukan "komponen pertama dari `p`". Harganya adalah teks yang sedikit lebih panjang. Imbalannya: seluruh pengetahuan tentang *bagaimana* sebuah `Point` tersusun terkumpul di satu tempat, yaitu definisi type-nya, sehingga bila kelak susunannya diubah (misalnya `Point` disimpan dalam koordinat polar), tidak ada satu pun fungsi lain yang perlu ikut berubah.

Gagasan ini akan kembali kelak, jauh lebih besar, dengan nama *abstraksi data*. Di sini, ini baru berupa disiplin menulis.

## Contoh 1: Type Point

**Persoalan.** Definisikan type `Point` yang mewakili sebuah titik pada koordinat kartesian, beserta operasi yang lazim atasnya.

```
TYPE POINT
```

```
DEFINISI TYPE
    type Point : ⟨x : Real, y : Real⟩
    { ⟨x, y⟩ adalah sebuah Point, dengan x sebagai absis dan y sebagai ordinat }

DEFINISI DAN SPESIFIKASI SELEKTOR
    x : Point → Real
    { x (p) memberikan absis dari Point p }

    y : Point → Real
    { y (p) memberikan ordinat dari Point p }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makePoint : Real, Real → Point
    { makePoint (a, b) membentuk sebuah Point dengan absis a dan ordinat b }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isOrigin : Point → Boolean
    { isOrigin (p) bernilai true bila p adalah titik pusat koordinat ⟨0.0, 0.0⟩ }

    isOnAxis : Point → Boolean
    { isOnAxis (p) bernilai true bila p terletak pada sumbu X atau sumbu Y }

DEFINISI DAN SPESIFIKASI OPERATOR LAIN
    distanceFromOrigin : Point → Real
    { distanceFromOrigin (p) adalah jarak p terhadap titik pusat koordinat }

    distance : Point, Point → Real
    { distance (p1, p2) adalah jarak antara Point p1 dan p2 }

    quadrant : Point → Integer [1..4]
    { quadrant (p) adalah nomor kuadran tempat p berada.
      Prasyarat: not (isOnAxis (p)) }
```

```
REALISASI
    isOrigin (p) : (x (p) = 0.0) and (y (p) = 0.0)

    isOnAxis (p) : (x (p) = 0.0) or (y (p) = 0.0)

    distanceFromOrigin (p) : sqrt (x (p) * x (p) + y (p) * y (p))

    distance (p1, p2) :
        sqrt (sqrDif (x (p1), x (p2)) +
              sqrDif (y (p1), y (p2)))

    quadrant (p) :
        depend on x (p), y (p)
            (x (p) > 0.0) and (y (p) > 0.0) : 1
            (x (p) < 0.0) and (y (p) > 0.0) : 2
            (x (p) < 0.0) and (y (p) < 0.0) : 3
            (x (p) > 0.0) and (y (p) < 0.0) : 4
```

```
APLIKASI
    ⇒ isOrigin (makePoint (0.0, 0.0))
      true
    ⇒ isOnAxis (makePoint (0.0, 5.0))
      true
    ⇒ distanceFromOrigin (makePoint (3.0, 4.0))
      5.0
    ⇒ distance (makePoint (1.0, 1.0), makePoint (4.0, 5.0))
      5.0
    ⇒ quadrant (makePoint (-3.0, 2.0))
      2
```

Tiga hal patut diperhatikan.

**`quadrant` dan `distance` sudah pernah ditulis, dan sekarang berubah.** Pada Bab 4 definisinya adalah `quadrant : Real, Real → Integer [1..4]`, dan pada Bab 3 `distance : Real, Real, Real, Real → Real`. Isi realisasinya praktis sama; yang berubah adalah *apa yang diterimanya*. Versi terdahulu menerima bilangan-bilangan yang kebetulan diperlakukan sebagai titik; versi ini menerima titik. Perbedaannya tidak terlihat pada hasil, tetapi terlihat pada apa yang mungkin salah: `quadrant (3.0, 2.0)` dan `quadrant (2.0, 3.0)` sama-sama sah pada versi lama meskipun salah satunya mungkin berasal dari kekeliruan urutan, sedangkan pada versi ini tidak ada urutan yang dapat tertukar. Mulai bab ini, kedua nama itu selalu berarti versi yang bekerja atas `Point`.

Perhatikan bahwa `distance` memakai kembali `sqrDif` dari Bab 3, dan bahwa `distanceFromOrigin` justru **tidak** dapat memakai `square` dari Bab 2: domain `square` adalah `Integer`, sedangkan yang dikuadratkan di sini adalah `Real`.

**Prasyaratnya kini dapat dinyatakan dengan nama.** Bab 4 menuliskan prasyarat `x ≠ 0.0 and y ≠ 0.0`. Di sini prasyaratnya adalah `not (isOnAxis (p))`: kalimat yang menyebutkan gagasannya, bukan perhitungannya. Setiap kali sebuah kondisi cukup penting untuk muncul lebih dari sekali, ia layak diberi nama sebagai predikat.

**`distanceFromOrigin` bukan kasus khusus yang berdiri sendiri.** Fungsi ini dapat ditulis ulang sebagai `distance (p, makePoint (0.0, 0.0))`, dan dengan begitu hilanglah satu realisasi yang harus dipelihara. Yang tersisa hanyalah pertimbangan keterbacaan. Latihan Merancang di akhir bab menanyakan mana yang Anda pilih.

### Dalam Haskell

```haskell
data Point = Point { x :: Double, y :: Double }

-- konstruktor: Point 3.0 4.0
-- selektor:    x p, y p

isOrigin :: Point -> Bool
-- isOrigin p bernilai True bila p adalah titik pusat koordinat
isOrigin p = x p == 0.0 && y p == 0.0

distance :: Point -> Point -> Double
-- distance p1 p2 adalah jarak antara p1 dan p2
distance p1 p2 = sqrt (sqrDif (x p1) (x p2) +
                       sqrDif (y p1) (y p2))
```

Satu baris `data` menghasilkan tiga hal sekaligus: type-nya, konstruktornya (`Point`), dan kedua selektornya (`x`, `y`). Inilah yang dimaksud ketika dikatakan bahwa konstruktor dan selektor tidak perlu direalisasi dalam notasi. Implementasi bahasa yang mengerjakannya.

Dua catatan tentang terjemahan ini:

- Konstruktor dalam Haskell bernama `Point`, sama dengan nama type-nya, dan bukan `makePoint`. Keduanya boleh sama karena Haskell membedakan nama type dari nama nilai. Notasi tidak membuat pembedaan itu, sehingga `makePoint` tetap diperlukan di sana.
- `x p == 0.0` adalah perbandingan kesamaan atas bilangan riil, dan perbandingan semacam itu jarang merupakan gagasan yang baik: hasil perhitungan riil hampir tidak pernah persis sama dengan nilai yang diharapkan. Untuk titik yang dibentuk langsung dari literal, seperti pada contoh di atas, hal ini tidak menjadi masalah. Untuk titik yang merupakan hasil perhitungan, `isOrigin` sebaiknya diganti dengan pemeriksaan `distanceFromOrigin (p) < ε` untuk suatu `ε` kecil yang dinyatakan pada spesifikasi.

## Contoh 2: Type Fraction

**Persoalan.** Definisikan type `Fraction` beserta operator aritmatika dan relasional atasnya.

Type ini memperkenalkan dua persoalan yang tidak muncul pada `Point`.

```
TYPE FRACTION
```

```
DEFINISI TYPE
    type Fraction : ⟨numerator : Integer, denominator : Integer > 0⟩
    { ⟨numerator, denominator⟩ adalah sebuah pecahan yang bernilai
      numerator dibagi denominator. Penyebut selalu positif; tanda
      pecahan dibawa oleh pembilangnya }

DEFINISI DAN SPESIFIKASI SELEKTOR
    numerator : Fraction → Integer
    { numerator (f) memberikan pembilang dari f }

    denominator : Fraction → Integer > 0
    { denominator (f) memberikan penyebut dari f }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makeFraction : Integer, Integer → Fraction
    { makeFraction (n, d) membentuk pecahan bernilai n/d.
      Prasyarat: d > 0 }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isEqFraction : Fraction, Fraction → Boolean
    { isEqFraction (f1, f2) bernilai true bila f1 dan f2 bernilai sama }

    isLtFraction : Fraction, Fraction → Boolean
    { isLtFraction (f1, f2) bernilai true bila nilai f1 lebih kecil
      daripada nilai f2 }

DEFINISI DAN SPESIFIKASI OPERATOR LAIN
    addFraction : Fraction, Fraction → Fraction
    { addFraction (f1, f2) adalah jumlah f1 dan f2:
      n1/d1 + n2/d2 = (n1*d2 + n2*d1) / (d1*d2) }

    mulFraction : Fraction, Fraction → Fraction
    { mulFraction (f1, f2) adalah hasil kali f1 dan f2:
      n1/d1 * n2/d2 = (n1*n2) / (d1*d2) }

    toReal : Fraction → Real
    { toReal (f) adalah nilai f sebagai bilangan riil }
```

```
REALISASI
    isEqFraction (f1, f2) :
        numerator (f1) * denominator (f2) = numerator (f2) * denominator (f1)

    isLtFraction (f1, f2) :
        numerator (f1) * denominator (f2) < numerator (f2) * denominator (f1)

    addFraction (f1, f2) :
        makeFraction (numerator (f1) * denominator (f2) +
                      numerator (f2) * denominator (f1),
                      denominator (f1) * denominator (f2))

    mulFraction (f1, f2) :
        makeFraction (numerator (f1) * numerator (f2),
                      denominator (f1) * denominator (f2))

    toReal (f) : asReal (numerator (f)) / asReal (denominator (f))
```

```
APLIKASI
    ⇒ isEqFraction (makeFraction (1, 2), makeFraction (2, 4))
      true
    ⇒ isLtFraction (makeFraction (1, 3), makeFraction (1, 2))
      true
    ⇒ addFraction (makeFraction (1, 2), makeFraction (1, 3))
      ⟨5, 6⟩
    ⇒ mulFraction (makeFraction (2, 3), makeFraction (3, 4))
      ⟨6, 12⟩
    ⇒ toReal (makeFraction (1, 4))
      0.25
```

### Persoalan pertama: invarian type

Definisi type menyatakan `denominator : Integer > 0`. Pernyataan itu **tidak dijamin oleh apa pun**. Notasi menuliskannya, tetapi tidak ada mekanisme yang mencegah terbentuknya pecahan dengan penyebut nol (atau negatif).

Syarat semacam ini disebut **invarian** sebuah type: sifat yang harus berlaku bagi setiap nilai type tersebut, sepanjang hidupnya. Dalam notasi fungsional, invarian ditegakkan dengan disiplin, di dua tempat:

- Konstruktor mencantumkan prasyaratnya (`d > 0`), sehingga tanggung jawab memeriksanya jatuh kepada pemanggil. Perhatikan bahwa prasyaratnya adalah `d > 0` dan bukan `d ≠ 0`: prasyarat sebuah konstruktor harus menjamin **seluruh** invarian type-nya.
- Setiap operator yang menghasilkan nilai baru harus memastikan hasilnya tetap memenuhi invarian. Perhatikan bahwa `addFraction` dan `mulFraction` menghasilkan penyebut berupa hasil kali kedua penyebut masukannya, yang positif bila keduanya positif, jadi keduanya aman.

Bila satu saja operator lalai, seluruh sisa program berjalan di atas nilai yang tidak sah, dan gejalanya akan muncul di tempat yang jauh dari sebabnya. Memeriksa invarian pada setiap operator yang menghasilkan nilai bertype tersebut adalah kebiasaan yang murah dan berumur panjang.

### Persoalan kedua: nilai versus representasi

`makeFraction (1, 2)` dan `makeFraction (2, 4)` adalah dua nilai `Fraction` yang **berbeda representasinya** tetapi **sama nilainya**. Inilah sebabnya `isEqFraction` harus direalisasi, dan tidak boleh diserahkan kepada perbandingan komponen demi komponen, yang akan menjawab bahwa keduanya berbeda.

Perbedaan ini tidak muncul pada `Point`: dua `Point` sama bila dan hanya bila absis dan ordinatnya sama. `Fraction` adalah contoh pertama dalam diktat ini tempat **beberapa representasi mewakili satu nilai yang sama**, dan setiap kali hal itu terjadi, kesamaan menjadi sesuatu yang harus dirancang, bukan diwarisi.

Ada jalan lain: mengharuskan setiap `Fraction` selalu berada dalam bentuk paling sederhana, sehingga satu nilai hanya punya satu representasi dan perbandingan komponen kembali sah. Jalan itu memerlukan fungsi faktor persekutuan terbesar (FPB) yang definisinya rekursif, sehingga pembahasannya menunggu Bab 6. Latihan Merancang di akhir bab menanyakan mana di antara keduanya yang Anda pilih dan apa harganya.

### Dalam Haskell

```haskell
data Fraction = Fraction { numerator :: Integer, denominator :: Integer }

isEqFraction :: Fraction -> Fraction -> Bool
-- isEqFraction f1 f2 bernilai True bila f1 dan f2 bernilai sama
isEqFraction f1 f2 = numerator f1 * denominator f2 == numerator f2 * denominator f1

addFraction :: Fraction -> Fraction -> Fraction
-- addFraction f1 f2 adalah jumlah f1 dan f2
addFraction f1 f2 = Fraction (numerator f1 * denominator f2 + numerator f2 * denominator f1)
                             (denominator f1 * denominator f2)
```

Haskell menyediakan cara pintas untuk membangkitkan operasi kesamaan secara otomatis, dengan menambahkan `deriving Eq` pada definisi `data`. **Untuk `Fraction`, cara pintas itu memberikan jawaban yang salah**, karena yang dibangkitkannya adalah perbandingan komponen demi komponen: `Fraction 1 2` dan `Fraction 2 4` akan dinyatakan berbeda.

Ini adalah pelajaran yang lebih besar daripada satu kata kunci. Fasilitas otomatis sebuah bahasa bekerja pada tingkat representasi, sedangkan yang sering kita maksudkan adalah kesamaan pada tingkat nilai. Keduanya bertepatan cukup sering sehingga kita lupa bahwa keduanya berbeda (dan tidak bertepatan cukup sering sehingga kelupaan itu mahal).

## Contoh 3: Type Date

**Persoalan.** Definisikan type `Date` beserta predikat untuk memeriksa keabsahan dan membandingkan dua tanggal.

```
TYPE DATE
```

```
DEFINISI TYPE
    type Date : ⟨day : Integer [1..31], month : Integer [1..12], year : Integer > 0⟩
    { ⟨day, month, year⟩ adalah sebuah tanggal }

DEFINISI DAN SPESIFIKASI SELEKTOR
    day : Date → Integer [1..31]
    month : Date → Integer [1..12]
    year : Date → Integer > 0
    { masing-masing memberikan komponen yang bersesuaian dari sebuah Date }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makeDate : Integer, Integer, Integer → Date
    { makeDate (d, m, y) membentuk sebuah Date.
      Prasyarat: isValidDate (d, m, y) }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isValidDate : Integer, Integer, Integer → Boolean
    { isValidDate (d, m, y) bernilai true bila ⟨d, m, y⟩ adalah tanggal yang sah }

    isBefore : Date, Date → Boolean
    { isBefore (d1, d2) bernilai true bila d1 jatuh sebelum d2 }

DEFINISI DAN SPESIFIKASI OPERATOR LAIN
    nDaysInMonth : Integer [1..12], Integer > 0 → Integer [28..31]
    { nDaysInMonth (m, y) adalah banyaknya hari dalam bulan m pada tahun y }
```

```
REALISASI
    nDaysInMonth (m, y) :
      depend on m
        m = 2                                     : if isLeapYear (y) then 29 else 28
        (m = 4) or (m = 6) or (m = 9) or (m = 11) : 30
        else                                      : 31

    isValidDate (d, m, y) :
      ((y > 0) and (m ≥ 1) and (m ≤ 12) and (d ≥ 1))
        and then (d ≤ nDaysInMonth (m, y))

    isBefore (d1, d2) :
      let
        sameYear  : year (d1) = year (d2)
        sameMonth : month (d1) = month (d2)
      in
        depend on d1, d2
          not (sameYear)               : year (d1) < year (d2)
          sameYear and not (sameMonth) : month (d1) < month (d2)
          sameYear and sameMonth       : day (d1) < day (d2)
```

```
APLIKASI
    ⇒ nDaysInMonth (2, 1900)
      28
    ⇒ nDaysInMonth (2, 2000)
      29
    ⇒ isValidDate (29, 2, 2023)
      false
    ⇒ isValidDate (29, 2, 2024)
      true
    ⇒ isBefore (makeDate (31, 12, 2023), makeDate (1, 1, 2024))
      true
    ⇒ isBefore (makeDate (1, 3, 2024), makeDate (1, 3, 2024))
      false
```

Contoh ini mengumpulkan hampir seluruh isi empat bab sebelumnya dalam satu tempat, dan itulah sebabnya contoh ini diletakkan terakhir.

**`isLeapYear` dipakai kembali tanpa ditulis ulang.** Fungsi ini sudah didefinisikan pada Bab 3 dan sudah diperiksa di sana. Yang dikerjakan di sini hanyalah mengaplikasikannya. Kebiasaan menyusun fungsi dari fungsi yang sudah ada, alih-alih menuliskan kembali perhitungannya, adalah satu-satunya cara sebuah program tetap dapat diperiksa kebenarannya ketika ia bertumbuh.

**`and then` dipakai sekali, dan memang diperlukan sekali.** Pada `isValidDate`, ekspresi `d ≤ nDaysInMonth (m, y)` hanya bermakna bila `m` sudah dipastikan berada dalam `[1..12]`; tanpa hubung-singkat, `nDaysInMonth (13, 2024)` akan dievaluasi dan tidak menghasilkan apa-apa. Empat pemeriksaan sebelumnya selalu terdefinisi, sehingga keempatnya dirangkai dengan `and` biasa, sesuai anjuran Bab 4: `and then` hanya dipakai di tempat yang memang menuntutnya, agar pembaca tahu di mana urutan benar-benar berarti.

**`isBefore` adalah analisis kasus berjenjang, dan justru karena itu kondisinya harus ditulis utuh.** Godaan yang wajar adalah menuliskan kondisi kedua sebagai `month (d1) ≠ month (d2)` saja, seperti pada `stateOfWater`, dan membiarkan kondisi pertama "menyaring" kasusnya lebih dahulu. Kondisi seperti itu **tidak disjoint**: `⟨15, 5, 2023⟩` dan `⟨20, 3, 2024⟩` memenuhi kondisi pertama maupun kondisi kedua, dan keduanya menjawab berbeda: yang pertama `true`, yang kedua `false`. Karena urutan penulisan tidak berarti apa-apa dalam notasi ini (Bab 4), fungsi semacam itu tidak mempunyai nilai bagi masukan tersebut.

Yang menyelamatkannya adalah menyebutkan `sameYear` pada kondisi kedua dan ketiga. Apa yang dalam banyak bahasa diserahkan diam-diam kepada urutan penulisan, di sini harus dinyatakan. Nama antara membuat harga itu murah sekaligus membuat maksudnya terbaca. Perbandingan berjenjang seperti ini (banding komponen paling berpengaruh dahulu, dan hanya bila seri lanjutkan ke komponen berikutnya) adalah pola yang akan muncul lagi setiap kali dua nilai bentukan harus diurutkan.

**Perhatikan apa yang tidak ditulis di sini.** Menghitung selisih dua tanggal dalam hari, atau menentukan tanggal ke-*n* setelah sebuah tanggal, memerlukan penjumlahan atas sejumlah bulan atau tahun yang tidak diketahui sebelumnya. Persoalan semacam itu memerlukan rekursi, dan menunggu Bab 6.

## Type Bentukan sebagai Hasil

Sejauh ini type bentukan selalu muncul sebagai **domain**. Type bentukan dapat pula muncul sebagai **range**: sebuah fungsi boleh menghasilkan lebih dari satu nilai, asalkan keduanya dibungkus menjadi satu.

Kemampuan ini menyelesaikan persoalan yang sudah terasa sejak Bab 3. Pembagian bulat menghasilkan dua hal (hasil bagi dan sisa) dan keduanya dihitung oleh perhitungan yang sama. Menuliskan `div` dan `mod` sebagai dua fungsi terpisah berarti mengerjakan pekerjaan yang sama dua kali.

```
JUDUL   Pembagian Bulat dengan Sisa

DEFINISI DAN SPESIFIKASI
    divMod : Integer, Integer → ⟨Integer, Integer⟩
    { divMod (n, d) menghasilkan ⟨q, r⟩ dengan q hasil bagi bulat dan r sisa
      pembagian n oleh d, sehingga n = q*d + r dan 0 ≤ r < d.
      Prasyarat: d > 0 }

REALISASI
    divMod (n, d) : ⟨n div d, n mod d⟩

APLIKASI
    ⇒ divMod (17, 5)
      ⟨3, 2⟩
    ⇒ divMod (86400, 3600)
      ⟨24, 0⟩
```

Range `divMod` adalah sebuah type bentukan **tanpa nama**: sebuah tuple yang menyatakan komposisi nilainya tanpa `type` yang mendahuluinya. Notasi mengizinkan hal ini demi keringkasan, dan berguna ketika nilai bentukan itu hanya lewat.

Nilai bentukan tanpa nama diambil komponennya dengan `let` dari Bab 3, dengan sebuah tuple nama di sisi kiri pengikatan:

```
JUDUL   Konversi Detik menjadi Hari, Jam, Menit, Detik

DEFINISI DAN SPESIFIKASI
    toDHMS : Integer ≥ 0 → ⟨Integer ≥ 0, Integer [0..23], Integer [0..59], Integer [0..59]⟩
    { toDHMS (x) menghasilkan ⟨d, h, m, s⟩ sehingga
      x = 86400*d + 3600*h + 60*m + s }

REALISASI
    toDHMS (x) :
        let
            ⟨d, remD⟩ : divMod (x, 86400)
            ⟨h, remH⟩ : divMod (remD, 3600)
            ⟨m, s⟩    : divMod (remH, 60)
        in
            ⟨d, h, m, s⟩

APLIKASI
    ⇒ toDHMS (90061)
      ⟨1, 1, 1, 1⟩
    ⇒ toDHMS (59)
      ⟨0, 0, 0, 59⟩
```

Bandingkan dengan realisasi yang sama tanpa `let`:

```
toDHMS (x) :
    ⟨ x div 86400,
      (x mod 86400) div 3600,
      ((x mod 86400) mod 3600) div 60,
      ((x mod 86400) mod 3600) mod 60 ⟩
```

Keduanya benar dan menghasilkan hal yang sama. Yang kedua menghitung `x mod 86400` tiga kali dan `(x mod 86400) mod 3600` dua kali, dan memaksa pembacanya melakukan hal yang sama untuk meyakini bahwa keempat baris itu memang membicarakan satu pemecahan yang sama. Nama antara, sebagaimana pada Bab 3, dipakai bukan untuk menghemat perhitungan melainkan untuk menghemat usaha membaca.

**Kapan sebuah type bentukan perlu diberi nama?** Aturan praktisnya: bila nilai itu mempunyai arti tersendiri dan ada operasi yang berlaku atasnya, berilah ia nama. `⟨q, r⟩` hasil `divMod` hanya lewat dan langsung dibongkar (kedua komponennya dipakai secara terpisah), sehingga tuple tanpa nama memadai. Sebaliknya, bila `⟨d, h, m, s⟩` akan dibandingkan, dijumlahkan, atau dicetak di banyak tempat, ia sudah pantas menjadi `type Duration`.

## Sekilas: Type Alternatif

Seluruh bab ini membahas produk, yaitu nilai yang terdiri dari beberapa komponen **sekaligus**. Ada bentuk kedua yang diperkenalkan di sini secukupnya dan baru dibahas tuntas pada Bab 6 dan 7: **alternatif**, yaitu nilai yang merupakan **salah satu** dari beberapa kemungkinan.

Sebuah `Shape` adalah sebuah lingkaran **atau** sebuah persegi panjang (atau bangun-bangun lainnya, namun di contoh ini kita batasi dua bangun untuk menyederhanakan persoalan). Tidak pernah keduanya secara bersamaan, dan komponennya berbeda menurut alternatif mana yang berlaku.

```
TYPE SHAPE
```

```
DEFINISI TYPE
    type Shape : Circle ⟨r : Real > 0⟩
               | Rectangle ⟨w : Real > 0, h : Real > 0⟩
    { Sebuah Shape adalah sebuah lingkaran berjari-jari r,
      atau sebuah persegi panjang berukuran w × h }

DEFINISI DAN SPESIFIKASI SELEKTOR
    r : Circle → Real > 0
    { r (s) memberikan jari-jari s }

    w : Rectangle → Real > 0
    { w (s) memberikan lebar s }

    h : Rectangle → Real > 0
    { h (s) memberikan tinggi s }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makeCircle : Real → Circle
    { makeCircle (a) membentuk sebuah lingkaran berjari-jari a.
      Prasyarat: a > 0.0 }

    makeRectangle : Real, Real → Rectangle
    { makeRectangle (a, b) membentuk sebuah persegi panjang berukuran a × b.
      Prasyarat: a > 0.0 and b > 0.0 }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isCircle : Shape → Boolean
    { isCircle (s) bernilai true bila s adalah sebuah lingkaran }

    isRectangle : Shape → Boolean
    { isRectangle (s) bernilai true bila s adalah sebuah persegi panjang }

DEFINISI DAN SPESIFIKASI OPERATOR LAIN
    area : Shape → Real > 0
    { area (s) adalah luas bentuk s }
```

```
REALISASI
    area (s) :
        depend on s
            isCircle (s)    : pi * r (s) * r (s)
            isRectangle (s) : w (s) * h (s)
```

```
APLIKASI
    ⇒ area (makeCircle (1.0))
      3.14159
    ⇒ area (makeRectangle (3.0, 4.0))
      12.0
```

Tiga hal yang membuat alternatif berbeda dari produk, dan ketiganya menguntungkan kita:

- Analisis kasus atas type alternatif **selalu lengkap dan disjoint dengan sendirinya**, karena setiap nilai persis merupakan satu alternatif. Syarat yang pada Bab 4 harus dijaga sendiri oleh penulis, di sini dijamin oleh type-nya. Karena itu pula `else` tidak dipakai pada `area`: menuliskan `isRectangle (s)` secara terbuka tidak lebih panjang, dan membuat jaminan tersebut terlihat alih-alih tersembunyi.
- Selektor sebuah alternatif hanya berlaku bagi alternatif itu: `r (s)` tidak bermakna bila `s` adalah sebuah persegi panjang.
- Setiap alternatif mempunyai konstruktornya sendiri, dan karena itu prasyaratnya sendiri. Pada produk hanya ada satu pintu masuk ke sebuah type; pada alternatif ada sebanyak alternatifnya.

Dalam Haskell:

```haskell
data Shape = Circle Double
           | Rectangle Double Double

-- konstruktor: Circle 1.0, Rectangle 3.0 4.0

area :: Shape -> Double
-- area s adalah luas bentuk s
area (Circle r)      = pi * r * r
area (Rectangle w h) = w * h
```

Haskell menggabungkan pemilihan kasus dengan pengambilan komponen dalam satu langkah, yang disebut *pattern matching*: baris pertama sekaligus menyatakan "bila b adalah sebuah lingkaran" dan memberi nama `r` kepada jari-jarinya. Predikat dan selektor terpisah tidak lagi diperlukan. Kompilator juga memeriksa bahwa seluruh alternatif tertangani. Syarat lengkap yang pada notasi hanya berupa janji, di sini diperiksa oleh mesin.

Alternatif akan muncul lagi pada Bab 7, karena **list adalah type alternatif**: sebuah list adalah list kosong, atau sebuah elemen diikuti sebuah list.

## Ringkasan Padanan

| Notasi fungsional | Haskell |
|---|---|
| `type Point : ⟨x : Real, y : Real⟩` | `data Point = Point { x :: Double, y :: Double }` |
| `makePoint (a, b)` | `Point a b` |
| `x (p)` | `x p` |
| `⟨a, b⟩` sebagai range | `(a, b)` |
| `let ⟨q, r⟩ : divMod (n, d) in …` | `let (q, r) = divMod n d in ...` |
| `type T : A ⟨...⟩ \| B ⟨...⟩` | `data T = A ... \| B ...` |
| `Integer [1..12]` | *tidak ada padanan; hidup pada spesifikasi* |
| `d : Integer > 0` (invarian) | *tidak ada padanan; hidup pada spesifikasi* |

Dua baris terakhir patut diperhatikan. Notasi dapat menyatakan pembatasan yang tidak dapat dinyatakan oleh bahasanya, dan pembatasan itu hilang dalam terjemahan. Sebaliknya, bahasa memberi pemeriksaan yang tidak dapat diberikan notasi. Keduanya bukan versi berbeda dari hal yang sama; keduanya mengerjakan pekerjaan yang berbeda.

## Latihan

**Membaca.** Diberikan definisi berikut:

```
type Segment : ⟨a : Point, b : Point⟩

g : Segment → Point
g (s) : makePoint ((x (a (s)) + x (b (s))) / 2.0,
                    (y (a (s)) + y (b (s))) / 2.0)

h : Segment → Boolean
h (s) : (x (a (s)) = x (b (s))) or (y (a (s)) = y (b (s)))
```

Tuliskan spesifikasi kedua fungsi tersebut, lalu usulkan nama yang sesuai dengan konvensi bab ini. Untuk `h`, adakah kasus yang membuat namanya menyesatkan?

**Memperbaiki.** Berikut sebuah realisasi `subFraction` (pengurangan pecahan) yang mengandung satu kesalahan:

```
subFraction (f1, f2) :
    makeFraction (numerator (f1) * denominator (f2) -
                  numerator (f2) * denominator (f1),
                  denominator (f1) - denominator (f2))
```

Temukan kesalahannya, dan sebutkan aplikasi yang membuktikannya. Kemudian jawab: invarian type `Fraction` mana yang dilanggar, dan pada nilai masukan seperti apa pelanggaran itu terjadi?

**Menulis.** Tuliskan kelima bagian secara lengkap: definisi type, selektor, konstruktor, predikat, dan operator lain. Nyatakan prasyarat bila ada, dan pilih contoh aplikasi yang mencakup setiap kasus beserta nilai batasnya.

1. Type `Line` yang dibentuk dari dua `Point`, beserta predikat untuk menentukan apakah dua garis sejajar, dan operator untuk menghitung panjang sebuah garis. Gunakan kembali `distance`.
2. Type `Square` yang dibentuk dari titik kiri-atas dan titik kanan-bawah, dengan sisi sejajar sumbu. Sertakan predikat `isSquare`, yang menentukan apakah kedua titik itu benar-benar membentuk bujur sangkar dan bukan persegi panjang sembarang, serta `isInside`, yang menentukan apakah sebuah `Point` terletak di dalamnya. Nyatakan dengan jelas apakah titik pada tepinya termasuk "di dalam" dan sadari bahwa keputusan itu milik Anda, bukan milik persoalannya.
3. Type `Complex` beserta penjumlahan, perkalian, dan modulus. Bandingkan strukturnya dengan `Fraction`: mana di antara keduanya yang mengalami persoalan "banyak representasi, satu nilai", dan mengapa?
4. Type `Time` sebagai `⟨hour, minute, second⟩`, beserta `isValidTime`, `toSeconds` yang mengubahnya menjadi jumlah detik sejak tengah malam, dan `isBefore`. Bandingkan realisasi `isBefore` Anda dengan versi `Date` pada bab ini.
5. Type `DateTime` yang dibentuk dari `Date` dan `Time`, beserta `isBefore` atasnya. Berapa banyak kode baru yang sesungguhnya harus Anda tulis, bila `isBefore` untuk `Date` dan untuk `Time` sudah ada?

**Menelusuri.** Tuliskan penelusuran lengkap `toDHMS (3661)`, dengan menyebutkan pada setiap langkah apakah yang terjadi ekspansi atau reduksi, dan pada langkah mana masing-masing `let` diikat. Kemudian tuliskan penelusuran `isValidDate (31, 2, 2024)` dan sebutkan pada kondisi keberapa hasilnya sudah dapat dipastikan.

**Merancang.** Tiga pertanyaan, ketiganya tanpa jawaban tunggal yang benar:

1. `distanceFromOrigin (p)` dapat dihapus dan diganti dengan `distance (p, makePoint (0.0, 0.0))`. Mana yang Anda pilih untuk diktat ini? Apa yang hilang bila `distanceFromOrigin` dihapus, dan apa yang hilang bila ia dipertahankan?
2. `Fraction` dapat dirancang dengan mengharuskan setiap nilainya berada dalam bentuk paling sederhana. Apa yang menjadi lebih mudah, apa yang menjadi lebih sulit, dan bagian mana dari definisi type yang harus berubah? (Fungsi penyederhananya sendiri baru dapat ditulis pada Bab 6.)
3. `type Point : ⟨x : Real, y : Real⟩` menyimpan titik dalam koordinat kartesian. Bila ia diganti menjadi koordinat polar `⟨r : Real, θ : Real⟩`, fungsi mana saja pada Contoh 1 yang harus ditulis ulang, dan fungsi mana yang tidak? Bandingkan jawaban Anda dengan aturan "bekerja lewat selektor" di awal bab. Apakah aturan itu memberikan sebanyak yang dijanjikannya?
