# Ekspresi Rekursif

Bab 2 menyebutkan tiga bentuk komposisi ekspresi: ekspresi dasar (dibahas pada Bab 3), ekspresi kondisional (Bab 4), dan ekspresi rekursif. Bab ini membahas yang ketiga dan terakhir.

Sebuah definisi disebut **rekursif** bila definisi itu menyebut dirinya sendiri. Hal ini berlaku baik bagi fungsi maupun bagi type.

Perhatikan bahwa bab ini hampir tidak menambahkan notasi baru. `depend on`, `if-then-else`, `let`, tuple, dan alternatif `|` semuanya sudah tersedia sejak Bab 5. Yang ditambahkan hanyalah sebuah **izin**: nama sebuah fungsi boleh muncul di dalam realisasinya sendiri, dan nama sebuah type boleh muncul di dalam definisinya sendiri.

Penambahan yang kecil, tetapi akibatnya besar. Sampai Bab 5, setiap fungsi hanya dapat mengerjakan sejumlah pekerjaan yang sudah tertulis di dalam teksnya. Dengan rekursi, sebuah teks yang panjangnya tetap dapat mengerjakan pekerjaan sebanyak yang dituntut oleh masukannya.

## Analisis Rekurens

**Analisis rekurens** adalah penalaran yang menghasilkan sebuah definisi rekursif. Penalaran ini menuntut dua hal, dan **keduanya sama wajibnya**.

- **Kewajiban pertama: ada kasus yang berhenti.** Sekurang-kurangnya satu kasus harus mempunyai realisasi yang **tidak** mengandung aplikasi terhadap fungsi itu sendiri. Kasus semacam itu disebut **basis**. Sebuah fungsi boleh mempunyai lebih dari satu basis.

- **Kewajiban kedua: rekurens menuju basis.** Setiap aplikasi rekursif harus dilakukan terhadap argumen yang lebih "dekat" kepada sebuah basis daripada argumen semula, dan kedekatan itu harus dapat dinyatakan. Cara menyatakannya: sebutkan besaran apa yang mendekati basis pada setiap langkah, dan sebutkan batasnya.

Pelanggaran kewajiban-kewajiban ini membuat fungsi tidak berhenti (tidak pernah menghasilkan nilai).
Pelanggaran kewajiban pertama mudah terlihat dari spesifikasi dan realisasinya: tidak ada kondisi yang menyebabkan fungsi langsung berhenti.
Pelanggaran kewajiban kedua lebih sulit dilihat: sebuah fungsi dapat mempunyai basis yang sah, memenuhi syarat lengkap dan syarat disjoint, dan tetap tidak pernah menghasilkan nilai. Contohnya diberikan sebentar lagi.

Kedua kewajiban itu dituliskan sebagai komentar pada spesifikasi, dengan bentuk yang tetap:

```
{ Basis:    *kondisi* → *hasil*
  Rekurens: *kondisi* → *ekspresi rekursif* }
```

Menuliskannya bukan formalitas. Analisis rekurens adalah bagian dari rancangan, dan rancangan itu harus terbaca oleh orang lain tanpa membongkar realisasinya.

## Bentuk Umum Fungsi Rekursif

```
namaFungsi (x) :
    depend on x
        *kondisi basis*    : *ekspresi tanpa aplikasi namaFungsi*
        *kondisi rekurens* : *ekspresi dengan aplikasi namaFungsi*
```

Syarat lengkap dan syarat disjoint dari Bab 4 berlaku persis seperti biasa, dan di sini keduanya memberi keuntungan tambahan. Karena kondisi basis dan kondisi rekurens harus dituliskan **utuh**, pemilahan antara keduanya menjadi terlihat pada teksnya. Dalam banyak bahasa pemrograman, kondisi rekurens cukup dituliskan sebagai "selain itu", sehingga pembaca harus menyimpulkan sendiri kasus mana yang sesungguhnya ditangani. Perbedaan ini akan muncul lagi pada bagian terjemahan di akhir bab.

## Contoh 1: Faktorial

**Persoalan.** Tuliskan sebuah fungsi yang menghitung faktorial sebuah bilangan bulat tak negatif.

```
JUDUL   Faktorial
```

```
DEFINISI DAN SPESIFIKASI
    factorial : Integer ≥ 0 → Integer > 0
    { factorial (n) adalah n!, yaitu hasil kali seluruh bilangan bulat
      dari 1 sampai dengan n. factorial (0) bernilai 1 }
    { Basis:    n = 0 → 1
      Rekurens: n > 0 → n * factorial (n - 1) }
```

```
REALISASI
    factorial (n) :
        depend on n
            n = 0 : 1
            n > 0 : n * factorial (n - 1)
```

```
APLIKASI
    ⇒ factorial (0)
      1
    ⇒ factorial (1)
      1
    ⇒ factorial (5)
      120
```

Periksalah kedua kewajiban. 

- **Basis:** kasus `n = 0` menghasilkan `1` tanpa mengaplikasikan `factorial`. 
- **Konvergensi:** pada setiap rekurens, `n` berkurang satu, dan `n` terbatas di bawah oleh `0`, sehingga sesudah sejumlah berhingga langkah kondisi basis pasti tercapai.

Ketiga contoh aplikasi dipilih sebagaimana biasa: satu tepat pada basis, satu tepat di atasnya, satu jauh di dalam daerah rekurens.

### Versi kedua: basis yang lain

Faktorial dapat pula ditulis dengan basis `n = 1`:

```
DEFINISI DAN SPESIFIKASI
    factorial : Integer > 0 → Integer > 0
    { Basis:    n = 1 → 1
      Rekurens: n > 1 → n * factorial (n - 1) }
```

```
REALISASI
    factorial (n) :
        depend on n
            n = 1 : 1
            n > 1 : n * factorial (n - 1)
```

Realisasinya hampir tidak berubah. Yang berubah adalah **domainnya**: versi kedua tidak terdefinisi untuk `n = 0`.

Ini adalah instansi keempat dari satu persoalan dengan beberapa realisasi, sesudah `cube` pada Bab 2, `distance` pada Bab 3, dan `toDHMS` pada Bab 5. Kali ini yang berbeda bukan hanya keterbacaannya, melainkan apa yang boleh diterima fungsinya. Memilih basis berarti memilih domain, dan keduanya tidak dapat diputuskan terpisah.

Diktat ini memakai versi pertama, dengan dua alasan. Pertama, `0! = 1` benar secara matematika dan menghapusnya berarti mempersempit fungsi tanpa keuntungan. Kedua, basis yang berupa "kasus kosong" adalah pola yang akan berulang terus mulai Bab 7, dan di sana basisnya adalah list kosong. Membiasakan diri sekarang membuat bab itu terasa lebih akrab.

## Menelusuri Ekspresi Rekursif

Penelusuran `factorial (4)`, ditulis lengkap:

```
⇒ factorial (4)
  → depend on 4                                  { ekspansi factorial }
        4 = 0 : 1
        4 > 0 : 4 * factorial (4 - 1)
  → 4 * factorial (3)                            { reduksi kondisi, pilih rekurens }
  → 4 * (3 * factorial (2))                      { ekspansi factorial }
  → 4 * (3 * (2 * factorial (1)))                { ekspansi factorial }
  → 4 * (3 * (2 * (1 * factorial (0))))          { ekspansi factorial }
  → 4 * (3 * (2 * (1 * 1)))                      { ekspansi factorial, basis }
  → 4 * (3 * (2 * 1))                            { reduksi * }
  → 4 * (3 * 2)                                  { reduksi * }
  → 4 * 6                                        { reduksi * }
  → 24                                           { reduksi * }
```

Mulai sekarang, langkah `depend on` boleh disingkat sebagaimana pada baris kedua di atas, karena bentuknya selalu sama. Yang tidak boleh disingkat adalah keterangan di sebelah kanan.

Perhatikan bentuk penelusuran ini. Ekspresi **membesar** lebih dahulu, satu lapis pada setiap ekspansi, sampai basis tercapai. Baru setelah itu **mengecil**, satu reduksi pada satu waktu, sampai tersisa sebuah nilai. Dua fase itu adalah ciri khas rekursi, dan mengenalinya membuat penelusuran fungsi rekursif yang lebih rumit jauh lebih mudah diikuti.

Perhatikan pula bahwa perkalian yang sesungguhnya baru terjadi **setelah** seluruh ekspansi selesai. Tidak ada satu pun perkalian yang dapat dihitung sebelum `factorial (0)` mencapai basisnya. Sifat ini akan menjadi persoalan kelak, dan dibicarakan pada Bab 9.

## Rumus yang Benar, Program yang Salah

Perhatikan realisasi berikut:

```
factorial (n) :
    depend on n
        n = 1 : 1
        n > 1 : factorial (n + 1) / (n + 1)
```

Rumusnya **benar**. Secara matematis, `(n+1)! = (n+1) × n!` sama dengan `n! = (n+1)! / (n+1)`, dan kesamaan itu berlaku untuk setiap `n > 0`.

Fungsinya mempunyai basis, yaitu `n = 1`. Kedua kondisinya disjoint. Atas domain `Integer > 0` keduanya lengkap. Seluruh syarat terpenuhi.

Meskipun demikian, `factorial (3)` tidak menghasilkan apa-apa. Ekspansinya adalah `factorial (4)`, lalu `factorial (5)`, lalu `factorial (6)`, dan seterusnya tanpa akhir. Argumennya **menjauhi** basis alih-alih mendekatinya, sehingga kewajiban kedua dilanggar.

Inilah sebabnya kedua kewajiban dinyatakan terpisah dan sama wajibnya. Kebenaran rumus tidak menjamin apa-apa tentang berhentinya evaluasi. Setiap kali sebuah rekurens dituliskan, sebutkan besaran yang mengecil beserta batas bawahnya. Bila besaran itu tidak dapat disebutkan, teksnya belum selesai.

## Contoh 2: Jumlah Digit

**Persoalan.** Tuliskan sebuah fungsi yang menghitung jumlah seluruh digit sebuah bilangan bulat tak negatif dalam penulisan desimal.

```
JUDUL   Jumlah Digit Sebuah Bilangan
```

```
DEFINISI DAN SPESIFIKASI
    sumDigits : Integer ≥ 0 → Integer ≥ 0
    { sumDigits (n) adalah jumlah seluruh digit n dalam penulisan desimal }
    { Basis:    n < 10 → n
      Rekurens: n ≥ 10 → (n mod 10) + sumDigits (n div 10) }
```

```
REALISASI
    sumDigits (n) :
        depend on n
            n < 10 : n
            n ≥ 10 : (n mod 10) + sumDigits (n div 10)
```

```
APLIKASI
    ⇒ sumDigits (0)
      0
    ⇒ sumDigits (7)
      7
    ⇒ sumDigits (10)
      1
    ⇒ sumDigits (2026)
      10
```

Dua hal patut diperhatikan, dan keduanya melawan dugaan yang wajar.

**Basisnya bukan sebuah nilai tunggal,** melainkan seluruh kelompok nilai `0` sampai `9`. Tidak ada aturan yang mengharuskan basis berupa satu nilai, dan memaksakannya di sini justru akan memperumit teks. Yang dituntut hanyalah bahwa kasus itu berhenti.

**Rekurensnya tidak mengurangi satu.** Argumennya dibagi sepuluh. Besaran yang mengecil adalah `n` itu sendiri, karena `n div 10 < n` untuk setiap `n ≥ 10`, dan batas bawahnya adalah `10`. Konvergensinya terjamin, dan jauh lebih cepat daripada pengurangan satu: `sumDigits (1000000)` mencapai basisnya dalam tujuh langkah, bukan sejuta.

Contoh ini menekankan bahwa rekursi tidak selalu berarti `n - 1`. Yang dituntut hanyalah bahwa argumen **mendekati** basis. Seberapa besar langkahnya, dan besaran apa yang dipakai untuk mengukur kedekatan itu, ditentukan oleh persoalannya.

Realisasi lain dengan basis `n = 0` dan rekurens `n > 0` juga benar, dan menghasilkan nilai yang sama untuk seluruh domain. Bandingkan keduanya, dan perhatikan berapa langkah masing-masing memerlukan untuk `sumDigits (7)`.

## Contoh 3: Pembagi Persekutuan Terbesar

Type `Fraction` pada Bab 5 mempunyai persoalan "beberapa representasi, satu nilai", dan salah satu jalan keluarnya menuntut penyederhanaan pecahan, yang menuntut faktor persekutuan terbesar, yang definisinya rekursif. Mari kita tinjau fungsi untuk menghitungnya, yang diberi nama `gcd` (*greatest common divisor*):

```
JUDUL   Faktor Persekutuan Terbesar
```

```
DEFINISI DAN SPESIFIKASI
    gcd : Integer ≥ 0, Integer ≥ 0 → Integer > 0
    { gcd (a, b) adalah bilangan bulat positif terbesar yang membagi
      habis a maupun b.
      Prasyarat: a dan b tidak keduanya nol }
    { Basis:    b = 0 → a
      Rekurens: b > 0 → gcd (b, a mod b) }
```

```
REALISASI
    gcd (a, b) :
        depend on b
            b = 0 : a
            b > 0 : gcd (b, a mod b)
```

```
APLIKASI
    ⇒ gcd (12, 18)
      6
    ⇒ gcd (17, 5)
      1
    ⇒ gcd (9, 0)
      9
```

Fungsi ini padat, dan setiap bagiannya pantas diperiksa.

**Basisnya bukan nilai yang "kecil".** Kondisi basis adalah `b = 0`, dan `a` boleh berapa saja. Basis tidak selalu berarti masukan terkecil, melainkan kasus yang jawabannya diketahui tanpa perlu bertanya lagi.

**Pemilahan kasus hanya menyebut satu parameter.** Fungsinya menerima dua argumen, tetapi `depend on b` sudah memadai, karena hanya `b` yang menentukan apakah kita sudah selesai.

**Argumennya bertukar tempat.** Pada rekurens, `b` berpindah ke posisi pertama. Penelusuran `gcd (12, 18)` memperlihatkan akibatnya:

```
⇒ gcd (12, 18)
  → gcd (18, 12 mod 18)     → gcd (18, 12)     { ekspansi, lalu reduksi mod }
  → gcd (12, 18 mod 12)     → gcd (12, 6)      { ekspansi, lalu reduksi mod }
  → gcd (6, 12 mod 6)       → gcd (6, 0)       { ekspansi, lalu reduksi mod }
  → 6                                          { basis }
```

Langkah pertama sekadar menukar kedua argumen, karena `12 mod 18` adalah `12`. Sesudah itu barulah nilainya mengecil.

**Konvergensinya perlu dinyatakan dengan hati-hati.** Besaran yang mengecil adalah parameter **kedua**. Untuk setiap `b > 0` berlaku `a mod b < b`, sehingga argumen kedua berkurang secara tegas pada setiap langkah, dan ia terbatas di bawah oleh `0`. Perhatikan bahwa `a` sendiri tidak selalu mengecil, dan pada langkah pertama di atas ia justru membesar. Yang wajib mengecil bukanlah setiap argumen, melainkan besaran yang kita pilih sebagai ukuran kedekatan terhadap basis.

Bahwa `gcd (a, b) = gcd (b, a mod b)` adalah kebenaran matematika yang pembuktiannya di luar cakupan diktat ini. Yang menjadi urusan kita hanyalah ini: sesudah kebenaran itu diterima, teks di atas berhenti.

### Penyederhanaan pecahan

Dengan `gcd`, kita dapat membuat fungsi untuk menyederhanakan pecahan:

```
JUDUL   Penyederhanaan Pecahan
```

```
DEFINISI DAN SPESIFIKASI
    simplify : Fraction → Fraction
    { simplify (f) adalah pecahan yang bernilai sama dengan f dan berada
      dalam bentuk paling sederhana, yaitu pembilang dan penyebutnya tidak
      mempunyai pembagi persekutuan selain 1 }
    { Fungsi antara yang dipakai: gcd }
```

```
REALISASI
    simplify (f) :
        let
            g : gcd (abs (numerator (f)), denominator (f))
        in
            makeFraction (numerator (f) div g, denominator (f) div g)
```

```
APLIKASI
    ⇒ simplify (makeFraction (6, 12))
      ⟨1, 2⟩
    ⇒ simplify (makeFraction (5, 6))
      ⟨5, 6⟩
    ⇒ simplify (mulFraction (makeFraction (2, 3), makeFraction (3, 4)))
      ⟨1, 2⟩
```

Tiga hal yang layak diperhatikan.

**`simplify` sendiri tidak rekursif.** Yang rekursif adalah `gcd`. Sebuah fungsi boleh memakai rekursi tanpa menjadi rekursif, dan justru itulah bentuk yang paling sering ditemui dalam program nyata: rekursinya terkurung di dalam satu fungsi antara yang kecil dan dapat diperiksa sendiri.

**`abs` diperlukan.** Invarian `Fraction` menyatakan bahwa penyebutnya positif dan tanda pecahan dibawa oleh pembilangnya, sehingga pembilang boleh negatif, sedangkan domain `gcd` adalah `Integer ≥ 0`.

**Prasyarat `gcd` terpenuhi dengan sendirinya.** Prasyarat ini menuntut bahwa kedua argumennya tidak keduanya nol, dan invarian `Fraction` menjamin `denominator (f) > 0`. Inilah imbalan dari menuliskan invarian pada Bab 5. Sebuah prasyarat yang harus diperiksa di satu tempat ternyata sudah dijamin oleh rancangan type di tempat lain.

Setelah `simplify` tersedia, pertanyaan Merancang nomor 2 pada Bab 5 kini dapat dijawab sungguh-sungguh. Pertanyaan ini diajukan kembali pada akhir bab ini.

## Contoh 4: Pemangkatan, dan Harga Sebuah Realisasi

**Persoalan.** Tuliskan sebuah fungsi yang memangkatkan sebuah bilangan bulat dengan sebuah bilangan bulat tak negatif.

```
JUDUL   Pemangkatan
```

```
DEFINISI DAN SPESIFIKASI
    power : Integer, Integer ≥ 0 → Integer
    { power (x, n) adalah x pangkat n. power (x, 0) bernilai 1 }
    { Basis:    n = 0 → 1
      Rekurens: n > 0 → x * power (x, n - 1) }
```

```
REALISASI
    power (x, n) :
        depend on n
            n = 0 : 1
            n > 0 : x * power (x, n - 1)
```

```
APLIKASI
    ⇒ power (2, 0)
      1
    ⇒ power (2, 10)
      1024
    ⇒ power (-3, 3)
      -27
```

Realisasi ini langsung mengikuti definisi pemangkatan dan tidak perlu dipertanyakan kebenarannya. Namun `power (2, 10)` menuntut sebelas kali aplikasi `power`, dan `power (2, 100)` menuntut seratus satu.

Ada realisasi lain bagi spesifikasi yang **persis sama**:

```
REALISASI (versi kedua)
    fastPower (x, n) :
        depend on n
            n = 0                     : 1
            (n > 0) and (n mod 2 = 0) : square (fastPower (x, n div 2))
            (n > 0) and (n mod 2 = 1) : x * fastPower (x, n - 1)
```

```
APLIKASI
    ⇒ fastPower (2, 10)
      1024
    ⇒ fastPower (-3, 3)
      -27
```

Dasarnya adalah dua kesamaan: `x^n = (x^(n div 2))²` bila `n` genap, dan `x^n = x × x^(n-1)` bila `n` ganjil. `square` diambil kembali dari Bab 2 tanpa ditulis ulang.

Ketiga kondisi lengkap dan disjoint atas `Integer ≥ 0`. Konvergensinya: pada kasus genap `n div 2 < n`, pada kasus ganjil `n - 1 < n`, dan keduanya terbatas di bawah oleh `0`. Perhatikan pula bahwa kasus ganjil selalu menghasilkan `n` genap, sehingga kedua kasus rekurens tidak dapat saling memanggil tanpa henti.

Sekarang hitunglah aplikasinya. Untuk `n = 10`, `fastPower` diaplikasikan pada `n` bernilai 10, 5, 4, 2, 1, dan 0, yaitu enam kali, sedangkan `power` sebelas kali. Untuk `n = 100`, `fastPower` sepuluh kali, `power` seratus satu kali. Selisihnya melebar dengan cepat.

Inilah pertama kalinya dalam diktat ini dua realisasi dari satu spesifikasi berbeda bukan pada keterbacaannya, melainkan pada **banyaknya pekerjaan** yang dilakukan. Sampai Bab 5, pemilihan antara dua realisasi selalu berupa pertimbangan selera dan kejelasan. Mulai sekarang ada sumbu ketiga.

Sumbu itu tidak gratis. `power` dapat diyakini kebenarannya dengan sekali baca, sedangkan `fastPower` menuntut pembacanya memeriksa dua kesamaan aljabar, tiga kondisi, dan dua argumen konvergensi. Yang lebih cepat adalah yang lebih sulit dipercaya. Kapan pertukaran itu pantas dilakukan bukan pertanyaan teknis, dan Latihan Merancang menanyakannya.

Perhatikan pula apa yang **tidak** dilakukan di sini. Kita menghitung banyaknya aplikasi, dan berhenti sampai di situ. Cara menyatakan pertumbuhan itu secara umum, beserta notasinya, adalah bahan tersendiri yang berada di luar cakupan diktat ini.

## Contoh 5: Fibonacci

**Persoalan.** Tuliskan sebuah fungsi yang menghasilkan suku ke-`n` deret Fibonacci, dengan dua suku pertamanya `0` dan `1`.

```
JUDUL   Bilangan Fibonacci
```

```
DEFINISI DAN SPESIFIKASI
    fibonacci : Integer ≥ 0 → Integer ≥ 0
    { fibonacci (n) adalah suku ke-n deret Fibonacci, dengan
      fibonacci (0) = 0, fibonacci (1) = 1, dan setiap suku berikutnya
      adalah jumlah dua suku sebelumnya }
    { Basis:    n = 0 → 0
      Basis:    n = 1 → 1
      Rekurens: n > 1 → fibonacci (n - 1) + fibonacci (n - 2) }
```

```
REALISASI
    fibonacci (n) :
        depend on n
            n = 0 : 0
            n = 1 : 1
            n > 1 : fibonacci (n - 1) + fibonacci (n - 2)
```

```
APLIKASI
    ⇒ fibonacci (0)
      0
    ⇒ fibonacci (1)
      1
    ⇒ fibonacci (7)
      13
```

**Mengapa dua basis?** Karena rekurensnya menjangkau dua langkah ke belakang. Dengan basis `n = 0` saja, evaluasi `fibonacci (1)` akan menuntut `fibonacci (0) + fibonacci (-1)`, dan `-1` berada di luar domain. Aturan umumnya: banyaknya basis ditentukan oleh sejauh mana rekurens melangkah mundur, bukan oleh selera. Rekurens yang melangkah dua, memerlukan dua.

**Dua aplikasi rekursif dalam satu ekspresi.** Sampai sekarang setiap rekurens hanya mengandung satu aplikasi, sehingga penelusurannya berupa satu garis. Di sini penelusurannya bercabang menjadi **pohon**:

```
fibonacci (5)
├── fibonacci (4)
│   ├── fibonacci (3)
│   │   ├── fibonacci (2)
│   │   │   ├── fibonacci (1)
│   │   │   └── fibonacci (0)
│   │   └── fibonacci (1)
│   └── fibonacci (2)
│       ├── fibonacci (1)
│       └── fibonacci (0)
└── fibonacci (3)
    ├── fibonacci (2)
    │   ├── fibonacci (1)
    │   └── fibonacci (0)
    └── fibonacci (1)
```

Hitunglah simpulnya: lima belas aplikasi untuk `fibonacci (5)`. Hitunglah pula berapa kali masing-masing muncul. `fibonacci (3)` dihitung dua kali, `fibonacci (2)` tiga kali, `fibonacci (1)` lima kali.

Transparansi referensial dari Bab 3 menjamin bahwa setiap perhitungan ulang itu menghasilkan nilai yang sama persis. Justru karena itulah seluruhnya adalah pekerjaan yang sia-sia. Sifat yang membuat program fungsional mudah dinalar sekaligus membuat pemborosan semacam ini terlihat jelas sebagai pemborosan.

### Membawa hasil ke depan: akumulasi

Pemborosan itu berasal dari bentuk rekursinya. Pada `fibonacci`, hasil disusun **pada saat kembali** dari basis, sehingga setiap cabang harus turun sendiri sampai ke dasar. Ada bentuk lain, yang membawa informasi yang sudah diperoleh **maju** sebagai parameter tambahan.

```
JUDUL   Bilangan Fibonacci dengan Akumulasi
```

```
DEFINISI DAN SPESIFIKASI
    fibFrom : Integer ≥ 0, Integer, Integer → Integer
    { fibFrom (k, a, b) adalah suku ke-k dari sebuah deret Fibonacci
      yang dua suku pertamanya a dan b }
    { Basis:    k = 0 → a
      Rekurens: k > 0 → fibFrom (k - 1, b, a + b) }

    fibonacci2 : Integer ≥ 0 → Integer ≥ 0
    { fibonacci2 (n) menghasilkan nilai yang sama dengan fibonacci (n) }
    { Fungsi antara yang dipakai: fibFrom }
```

```
REALISASI
    fibFrom (k, a, b) :
        depend on k
            k = 0 : a
            k > 0 : fibFrom (k - 1, b, a + b)

    fibonacci2 (n) : fibFrom (n, 0, 1)
```

```
APLIKASI
    ⇒ fibonacci2 (0)
      0
    ⇒ fibonacci2 (7)
      13
```

Penelusuran `fibonacci2 (7)` berupa satu garis lurus, dan `fibFrom` diaplikasikan delapan kali. Bandingkan dengan `fibonacci (7)`, yang menuntut empat puluh satu aplikasi.

Parameter `a` dan `b` disebut **akumulator**: keduanya membawa hasil sementara maju ke langkah berikutnya. Pola ini akan muncul berulang kali mulai Bab 7.

Kesulitannya tidak terletak pada realisasi, yang justru lebih pendek. Kesulitan terletak pada **spesifikasinya**. Spesifikasi `fibonacci` dapat dituliskan dalam satu kalimat yang dipahami siapa pun. Spesifikasi `fibFrom` menuntut kita menjelaskan apa arti `a` dan `b` pada setiap langkah, dan menjelaskannya dengan tepat adalah bagian tersulit dari seluruh konstruksi ini. Sebuah fungsi berakumulator yang spesifikasinya asal-asalan hampir selalu keliru, dan kekeliruannya hampir selalu terletak pada nilai awal akumulatornya.

> ### Ke mana perginya pengulangan?
>
> Pembaca yang terbiasa dengan bahasa prosedural mungkin sudah menyadari sesuatu. Letakkan `fibFrom` berdampingan dengan sebuah loop:
>
> ```
> fibFrom (k, a, b) :
>     depend on k
>         k = 0 : a
>         k > 0 : fibFrom (k - 1, b, a + b)
> ```
>
> ```
> while (k > 0) do
>     t ← a + b
>     a ← b
>     b ← t
>     k ← k - 1
> → a
> ```
>
> Keduanya adalah hal yang sama. Ketiga parameter `k`, `a`, dan `b` memainkan peran yang persis sama dengan ketiga variabel pada loop, setiap aplikasi rekursif adalah satu putaran, dan kondisi basis adalah kondisi berhentinya. Bahkan urutan penulisan `b, a + b` bersesuaian dengan ketiga baris penugasan di dalam loop, termasuk keperluan akan variabel sementara `t`.
>
> Pengamatan ini penting, dan mudah disalahartikan ke dua arah.
>
> **Ini tidak berarti bahwa program fungsional sesungguhnya prosedural.** Pada versi prosedural, ketiga variabel itu **berubah nilai**, dan untuk mengetahui nilai `a` kita harus tahu sudah berapa putaran berlalu. Pada versi fungsional tidak ada yang berubah sama sekali. Setiap aplikasi `fibFrom` adalah aplikasi baru terhadap argumen yang berbeda, dan setiap nama tetap terikat pada satu nilai selama hidupnya. Transparansi referensial dari Bab 2 utuh. Yang sama pada keduanya adalah **bentuknya**, bukan artinya.
>
> **Ini juga tidak berarti bahwa bentuk berakumulator selalu lebih baik.** Bentuk rekursif berakumulator lebih baik di sini, karena persoalannya memang berbentuk pengulangan sederhana. Bab 7 akan memperlihatkan bahwa sebagian besar fungsi atas list justru paling jelas dalam bentuk yang bukan akumulator, dan memaksakan akumulator ke dalamnya menghasilkan teks yang lebih panjang sekaligus lebih lambat.
>
> Yang wajar ditanyakan sesudah ini adalah pertanyaan tentang mesin. Bila keduanya berbentuk sama, apakah keduanya dijalankan dengan cara yang sama, dan apakah salah satunya lebih hemat? Pertanyaan itu adalah pertanyaan eksekusi, bukan pertanyaan rancangan, dan jawabannya berbeda-beda menurut bahasanya. Bab 9 membahasnya.

## Rekursi Tidak Langsung

Sebuah fungsi tidak harus mengaplikasikan dirinya sendiri untuk menjadi rekursif. Sebuah fungsi \\(f\\) dapat mengaplikasikan fungsi lain \\(g\\) yang pada gilirannya mengaplikasikan \\(f\\).

```
DEFINISI DAN SPESIFIKASI
    isEven : Integer ≥ 0 → Boolean
    { isEven (n) bernilai true bila n genap }
    { Basis:    n = 0 → true
      Rekurens: n > 0 → isOdd (n - 1) }

    isOdd : Integer ≥ 0 → Boolean
    { isOdd (n) bernilai true bila n ganjil }
    { Basis:    n = 0 → false
      Rekurens: n > 0 → isEven (n - 1) }
```

```
REALISASI
    isEven (n) :
        depend on n
            n = 0 : true
            n > 0 : isOdd (n - 1)

    isOdd (n) :
        depend on n
            n = 0 : false
            n > 0 : isEven (n - 1)
```

Bentuk ini disebut **rekursi tidak langsung** atau **saling rekursif**. Kedua kewajiban tetap berlaku dan tetap dapat diperiksa: masing-masing mempunyai basis pada `n = 0`, dan `n` berkurang satu pada setiap langkah tanpa memandang fungsi mana yang mengambil alih.

Sebagai cara menguji keganjilan sebuah bilangan, cara di atas tentu saja kalah oleh `n mod 2 = 0` yang sudah Anda tuliskan pada Latihan Bab 2. Cara ini dicantumkan di sini bukan karena berguna, melainkan karena **bentuknya** akan diperlukan kelak. Pada Bab 10, sebuah pohon dan daftar anaknya didefinisikan saling merujuk, sehingga fungsi atas keduanya juga harus saling merujuk.

## Type Rekursif

Sebuah type disebut **rekursif** bila definisinya menyebut dirinya sendiri. Notasi untuk itu sudah tersedia sejak Bab 5, yaitu type alternatif, dan tidak ada yang perlu ditambahkan selain izin untuk menyebut nama type yang sedang didefinisikan.

```
TYPE NAT
```

```
DEFINISI TYPE
    type Nat : Zero
             | Succ ⟨pred : Nat⟩
    { Sebuah Nat adalah bilangan nol, atau penerus dari sebuah Nat }

DEFINISI DAN SPESIFIKASI SELEKTOR
    pred : Succ → Nat
    { pred (n) memberikan Nat yang diteruskan oleh n }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makeZero : → Nat
    { makeZero membentuk bilangan nol }

    makeSucc : Nat → Nat
    { makeSucc (n) membentuk penerus dari n }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isZero : Nat → Boolean
    { isZero (n) bernilai true bila n adalah bilangan nol }

DEFINISI DAN SPESIFIKASI OPERATOR LAIN
    toInteger : Nat → Integer ≥ 0
    { toInteger (n) adalah nilai n sebagai bilangan bulat }
    { Basis:    isZero (n)        → 0
      Rekurens: not (isZero (n))  → 1 + toInteger (pred (n)) }
```

```
REALISASI
    toInteger (n) :
        depend on n
            isZero (n)       : 0
            not (isZero (n)) : 1 + toInteger (pred (n))
```

```
APLIKASI
    ⇒ toInteger (makeZero)
      0
    ⇒ toInteger (makeSucc (makeSucc (makeZero)))
      2
```

Nilai-nilai `Nat` adalah `Zero`, lalu `Succ ⟨Zero⟩`, lalu `Succ ⟨Succ ⟨Zero⟩⟩`, dan seterusnya. Setiap nilai dibangun dalam sejumlah **berhingga** langkah, mulai dari `Zero`.

Justru di sinilah letak nilai gagasan ini, dan sebaiknya diperhatikan baik-baik sebelum Bab 7.

**Untuk type rekursif, kedua kewajiban diberikan oleh type-nya.** Bab 5 menyatakan bahwa analisis kasus atas type alternatif selalu lengkap dan disjoint dengan sendirinya. Sekarang tambahkan hal berikut: alternatif yang **tidak** menyebut dirinya sendiri adalah basisnya, alternatif yang menyebut dirinya sendiri adalah rekurensnya, dan konvergensi terjamin karena setiap nilai hanya tersusun dari berhingga lapis. Ketiga hal yang pada Contoh 1 sampai 5 harus dijaga sendiri oleh penulisnya, di sini datang cuma-cuma bersama definisi type.

**`Integer` bukan type rekursif dalam diktat ini.** Ia adalah type dasar, sebagaimana ditetapkan sejak Bab 2. Yang kita lakukan pada seluruh contoh sebelumnya bukanlah membongkar sebuah struktur rekursif, melainkan **menalar secara rekursif** terhadap sebuah type yang tidak rekursif. Itulah sebabnya setiap contoh menuntut argumen konvergensi yang ditulis sendiri, dan mengapa `factorial (n + 1) / (n + 1)` sempat lolos dari seluruh pemeriksaan Bab 4.

Perbedaan ini punya akibat pada peristilahan. Untuk type rekursif, banyaknya kemungkinan basis ditentukan oleh banyaknya konstruktornya, sehingga wajar bila masing-masing diberi nama. Untuk `Integer`, tidak ada yang membatasi di mana basis boleh diletakkan, dan kelima contoh di bab ini membuktikannya dengan basis `n = 0`, `n = 1`, `n < 10`, `b = 0`, serta pasangan `n = 0` dan `n = 1`. Peristilahan **basis-0** dan **basis-1** yang dipakai secara luas di lingkungan kuliah karenanya baru diperkenalkan pada Bab 7, dan di sana masing-masing mengacu pada list kosong dan list satu elemen.

Type rekursif yang paling penting dalam informatika adalah **list**: sebuah list adalah list kosong, atau sebuah elemen diikuti oleh sebuah list. Bab 7 mengambil alih dari sini.

## Kesalahan yang Sering Terjadi

| Kesalahan | Gejala | Cara memeriksa |
|---|---|---|
| Tidak ada basis | tidak pernah menghasilkan nilai | adakah sekurang-kurangnya satu kasus yang realisasinya tidak mengandung nama fungsi itu sendiri? |
| Rekurens tidak menuju basis | tidak pernah menghasilkan nilai, meskipun rumusnya benar | sebutkan besaran yang mengecil pada setiap rekurens beserta batas bawahnya; bila tidak dapat disebutkan, teksnya belum selesai |
| Basis kurang | benar untuk masukan besar, gagal untuk masukan kecil | bila rekurens melangkah mundur *k* langkah, adakah *k* buah basis? |
| Rekurens melompati basis | gagal untuk sebagian masukan saja, misalnya yang ganjil | telusuri masukan terkecil pada daerah rekurens; apakah ia mendarat tepat pada basis? |
| Rekurens keluar dari domain | tidak terdefinisi untuk masukan tertentu | apakah argumen rekursifnya masih memenuhi domain dan prasyarat fungsi itu sendiri? |
| Kasus tidak disjoint | ambigu | berlaku persis seperti Bab 4; basis dan rekurens adalah kasus biasa |
| Pekerjaan berulang | hasilnya benar, tetapi sangat lambat untuk masukan sedang | gambarkan pohon aplikasinya; adakah aplikasi yang sama muncul lebih dari sekali? |

Baris kedua patut mendapat perhatian khusus, karena ini adalah satu-satunya kesalahan pada tabel yang tidak dapat ditemukan dengan mencoba nilai batas. Sebuah fungsi yang rekurensnya menjauhi basis tidak menghasilkan jawaban keliru yang dapat diperiksa; ia tidak menghasilkan apa-apa sama sekali.

## Ringkasan Padanan

Faktorial dalam Haskell:

```haskell
factorial :: Integer -> Integer
-- factorial n adalah n!
factorial 0 = 1
factorial n = n * factorial (n - 1)
```

Pembagi persekutuan terbesar dan Fibonacci:

```haskell
gcd' :: Integer -> Integer -> Integer
-- gcd' a b adalah pembagi persekutuan terbesar a dan b
gcd' a 0 = a
gcd' a b = gcd' b (a `mod` b)

fibonacci :: Integer -> Integer
-- fibonacci n adalah suku ke-n deret Fibonacci
fibonacci 0 = 0
fibonacci 1 = 1
fibonacci n = fibonacci (n - 1) + fibonacci (n - 2)
```

> **Catatan:** Fungsi diberi nama `gcd'` karena Haskell sudah mempunyai fungsi bawaan `gcd`. 

Empat hal yang berbeda, dan keempatnya sudah menjadi pola yang akrab.

**Haskell mencocokkan langsung pada nilai.** `factorial 0 = 1` menyatakan kasus basis tanpa menuliskan kondisi apa pun. Fasilitas ini disebut pencocokan pola (*pattern matching*), dan sudah muncul pada Bab 5 untuk type alternatif. Di sini, pencocokan pola diterapkan pada bilangan.

**Persamaan diperiksa berurutan, dan lubangnya tidak terlihat.** Baris kedua `factorial n = ...` berlaku bagi **setiap** `n` yang tidak nol, termasuk `-1`. Aplikasi `factorial (-1)` karenanya tidak pernah berhenti. Notasi menuliskan `n > 0` secara terbuka, dan pembatasan domain `Integer ≥ 0` pada barisan definisi memaksa penulisnya memikirkan hal itu. Ini adalah keketatan yang sama dengan yang dibicarakan pada Bab 4, muncul kembali di tempat baru: apa yang di sana menyangkut batas kasus, di sini menyangkut batas domain.

**`Integer` pada Haskell tidak terbatas.** `factorial 100` menghasilkan bilangan seratus lima puluh delapan digit, tepat dan utuh. Program yang sama dalam C dengan `long long` menghasilkan bilangan yang salah tanpa peringatan apa pun, karena hasilnya melampaui daya tampung typenya. Perhatikan bahwa yang berbeda di sini bukan paradigmanya, melainkan pilihan rancangan type dasarnya, dan bahwa persoalan itu sama sekali tidak muncul dalam notasi kita karena `Integer` di sana memang berarti bilangan bulat tanpa batas.

**Haskell sudah mempunyai `gcd` dan `^`.** Keduanya tersedia sebagai fungsi pustaka, dan itulah sebabnya contoh di atas dinamai `gcd'`. Sikap terhadap hal ini sama dengan sikap terhadap daftar fungsi dasar yang sengaja dibuat pendek pada Bab 3: keduanya tetap dituliskan sendiri, karena menuliskannya adalah latihannya.

| Notasi fungsional | Haskell |
|---|---|
| `namaFungsi (x) : depend on x` dengan basis dan rekurens | beberapa persamaan, atau *guard* |
| `n = 0 : 1` | `factorial 0 = 1` atau `\| n == 0 = 1` |
| `n > 0 : n * factorial (n - 1)` | `factorial n = n * factorial (n - 1)` |
| `Integer ≥ 0` sebagai domain | `Integer`; pembatasannya hidup pada spesifikasi |
| `{ Basis: … Rekurens: … }` | komentar |
| `type Nat : Zero \| Succ ⟨pred : Nat⟩` | `data Nat = Zero \| Succ Nat` |

Perhatikan baris terakhir. Definisi type rekursif diterjemahkan hampir tanpa perubahan, dan kompilator memeriksa bahwa seluruh alternatifnya tertangani. Sebaliknya, baris keempat memperlihatkan bahwa pembatasan domain yang justru paling menentukan berhentinya evaluasi adalah hal yang tidak dapat dinyatakan oleh bahasanya. Sebagaimana pada Bab 5, keduanya bukan versi berbeda dari hal yang sama.

## Latihan

**Membaca.** Untuk setiap realisasi berikut, tentukan definisinya, tuliskan spesifikasinya, sebutkan basis dan rekurensnya, lalu **buktikan bahwa evaluasinya berhenti** dengan menyebutkan besaran yang mengecil beserta batas bawahnya.

```
p (n) :
    depend on n
        n = 0 : 0
        n > 0 : n + p (n - 1)

q (a, b) :
    depend on b
        b = 0 : 0
        b > 0 : a + q (a, b - 1)

r (n) :
    depend on n
        n = 1                     : 1
        (n > 1) and (n mod 2 = 0) : r (n div 2)
        (n > 1) and (n mod 2 = 1) : r (3 * n + 1)
```

Untuk `q`, jawablah juga: apa yang terjadi bila `b` boleh negatif, dan bagaimana Anda mencegahnya?

Untuk `r`, telusurilah `r (6)` dan `r (7)`, lalu cobalah menyebutkan besaran yang mengecil. Bila Anda tidak berhasil, itu bukan kegagalan Anda. Apakah teks ini berhenti untuk setiap masukan adalah persoalan matematika yang sampai hari ini belum terjawab, meskipun sudah diperiksa untuk bilangan yang sangat besar. Jelaskan apa yang diajarkan oleh keadaan ini tentang kewajiban kedua.

**Memperbaiki.** Fungsi berikut menghitung banyaknya digit sebuah bilangan. Ketiga contoh aplikasinya menghasilkan jawaban yang benar, kedua kondisinya lengkap dan disjoint, dan evaluasinya selalu berhenti. Meskipun demikian, fungsi ini keliru.

```
JUDUL   Banyaknya Digit
```

```
DEFINISI DAN SPESIFIKASI
    nDigits : Integer ≥ 0 → Integer > 0
    { nDigits (n) adalah banyaknya digit n dalam penulisan desimal.
      nDigits (0) bernilai 1 }
    { Basis:    n = 0 → 0
      Rekurens: n > 0 → 1 + nDigits (n div 10) }
```

```
REALISASI
    nDigits (n) :
        depend on n
            n = 0 : 0
            n > 0 : 1 + nDigits (n div 10)
```

```
APLIKASI
    ⇒ nDigits (7)
      1
    ⇒ nDigits (42)
      2
    ⇒ nDigits (2026)
      4
```

1. Temukan satu nilai masukan yang menghasilkan jawaban keliru. Berapa yang dihasilkan fungsi, dan berapa yang seharusnya menurut spesifikasi?
2. Hasil pada nomor 1 melanggar sesuatu yang dituliskan pada baris **definisi**, bukan pada spesifikasi. Apa itu?
3. Spesifikasi dan realisasi bertentangan mengenai satu nilai. Mana yang Anda perbaiki, dan mengapa?
4. Perbaiki realisasinya. Bandingkan basis Anda dengan basis `sumDigits` pada Contoh 2, dan jelaskan mengapa keduanya berbeda meskipun kedua fungsi memakai rekurens yang sama.
5. Tambahkan contoh aplikasi sehingga setiap kasus dan setiap nilai batas terwakili.

**Menulis.** Tuliskan keempat bagian secara lengkap. Nyatakan prasyarat bila ada, tuliskan basis dan rekurens pada spesifikasi, sebutkan argumen konvergensinya, lalu terjemahkan dan jalankan.

1. `dayOfYear (d)`, yang menghasilkan nomor urut sebuah `Date` di dalam tahunnya, sehingga 1 Januari menghasilkan 1. Gunakan kembali `nDaysInMonth` dari Bab 5, dan rekursikan terhadap nomor bulan.
2. `daysBetween (d1, d2)`, yang menghasilkan selisih dua `Date` dalam hari. Mulailah dengan kasus keduanya berada pada tahun yang sama. Bila Anda melanjutkan ke tahun yang berbeda, nyatakan dengan jelas apa yang Anda rekursikan.
3. `reverseDigits (n)`, yang membalik urutan digit sebuah bilangan bulat tak negatif, sehingga `1230` menghasilkan `321`. Perhatikan baik-baik apa yang terjadi pada digit nol di ujung.
4. `isPalindromeNumber (n)`, dengan memakai kembali `reverseDigits`.
5. `isPrime (n)`, yang menentukan apakah sebuah bilangan bulat lebih besar dari 1 adalah bilangan prima. Fungsi ini menuntut sebuah fungsi antara dengan parameter tambahan yang menyatakan pembagi mana yang sedang diperiksa. Tuliskan spesifikasi fungsi antara itu lebih dahulu, dan perhatikan bahwa itulah bagian tersulitnya.

**Menelusuri.**

1. Gambarkan pohon aplikasi `fibonacci (6)`. Berapa banyak aplikasi seluruhnya, dan berapa kali `fibonacci (2)` muncul?
2. Telusuri `fibonacci2 (6)`. Berapa banyak aplikasi `fibFrom`? Bandingkan dengan nomor 1.
3. Telusuri `gcd (48, 18)`, lalu `gcd (18, 48)`. Bandingkan panjang keduanya dan jelaskan selisihnya.
4. Hitunglah banyaknya aplikasi `power (3, 8)` dan `fastPower (3, 8)`. Kemudian lakukan hal yang sama untuk pangkat `7`, dan jelaskan mengapa selisihnya berbeda.

**Merancang.** Tiga pertanyaan, ketiganya tanpa jawaban tunggal yang benar.

1. Faktorial ditulis dengan dua basis pada bab ini, `n = 0` dan `n = 1`. Diktat ini memilih yang pertama beserta alasannya. Setujukah Anda? Apa yang akan berubah pada Bab 7 seandainya pilihannya kebalikannya?
2. Pertanyaan Merancang nomor 2 pada Bab 5 kini dapat dijawab sungguh-sungguh. Bila setiap `Fraction` diharuskan selalu berada dalam bentuk paling sederhana, bagian mana dari teks type-nya yang berubah? Apakah `isEqFraction` masih perlu direalisasi? Apakah `addFraction` masih menjamin invariannya? Dan siapa yang bertanggung jawab memanggil `simplify`, konstruktornya atau pemakainya?
3. `fastPower` lebih hemat dan lebih sulit dipercaya. Sebutkan satu keadaan yang membuat Anda tetap memilih `power`, dan satu keadaan yang membuat Anda memilih `fastPower`. Kemudian jawab pertanyaan yang lebih tajam: seandainya diktat ini hanya boleh memuat satu di antara keduanya, mana yang Anda buang?
