# Ekspresi Kondisional

## Analisis Kasus

Tidak semua persoalan dapat dinyatakan dengan satu ekspresi tunggal. Seringkali nilai yang dihasilkan bergantung pada *kasus* mana yang berlaku bagi nilai masukan: wujud air bergantung pada suhunya, kuadran sebuah titik bergantung pada tanda absis dan ordinatnya, tarif bergantung pada jarak tempuh.

**Analisis kasus** adalah kegiatan memilah domain sebuah fungsi menjadi beberapa bagian, dan menentukan ekspresi yang berlaku bagi masing-masing bagian. Hasilnya dituliskan sebagai **ekspresi kondisional**.

Analisis kasus adalah keterampilan, bukan sekadar notasi. Sebagian besar isi bab ini membahas bagaimana melakukannya dengan benar, karena di sinilah letak sebagian besar kesalahan pemula, dan tidak sedikit kesalahan pemrogram berpengalaman.

## Notasi Ekspresi Kondisional

Bentuk umumnya:

```
namaFungsi (x) :
    depend on *deskripsi domain*
        *kondisi-1* : *ekspresi-1*
        *kondisi-2* : *ekspresi-2*
        *kondisi-3* : *ekspresi-3*
```

Setiap baris terdiri dari sebuah **kondisi** (ekspresi Boolean atas parameter fungsi) dan sebuah **ekspresi** yang berlaku bila kondisi itu benar. 

Nilai ekspresi kondisional adalah nilai ekspresi yang kondisinya benar.

## Dua Syarat: Lengkap dan Disjoint

Rumusan di atas menyembunyikan sebuah janji: *ada tepat satu* kondisi yang benar. Janji itu tidak dijamin oleh notasi; penulisnyalah yang harus memenuhinya. Ada dua syarat, dan keduanya wajib.

**Lengkap (exhaustive).** Setiap nilai dalam domain harus memenuhi sekurang-kurangnya satu kondisi.

\\[ \text{kondisi-1} \ \textbf{or}\ \text{kondisi-2} \ \textbf{or}\ \text{kondisi-3} \\]

harus bernilai `true` untuk setiap nilai domain. Bila tidak, ada nilai masukan yang tidak menghasilkan apa-apa, dan sebuah fungsi yang tidak menghasilkan apa-apa untuk sebagian domainnya bukanlah fungsi atas domain itu.

**Disjoint (mutually exclusive).** Tidak boleh ada nilai yang memenuhi lebih dari satu kondisi. Untuk setiap pasangan kondisi:

\\[ \textbf{not}\ (\text{kondisi-}i \ \textbf{and}\ \text{kondisi-}j) \qquad \text{untuk setiap } i \neq j \\]

Bila dua kondisi dapat benar bersamaan, ekspresi menjadi ambigu: ada dua nilai yang sama-sama berhak menjadi hasil.

> **Penting.** Dalam notasi ini, urutan penulisan kasus **tidak berarti apa-apa**. Kasus boleh dituliskan dengan urutan mana pun tanpa mengubah arti. Inilah sebabnya syarat disjoint bersifat wajib dan bukan sekadar anjuran: tidak ada aturan "yang pertama cocok, itulah yang dipakai" yang dapat menyelamatkan kita.
>
> Banyak bahasa pemrograman justru memakai aturan tersebut. Kelak, ketika menerjemahkan ke bahasa nyata, kemudahan itu akan tersedia. Godaan untuk bersandar padanya harus ditahan, karena teks yang bergantung pada urutan hanya dapat dipahami dengan membacanya dari atas ke bawah secara utuh.

## Kondisi `else`

Menuliskan negasi dari seluruh kondisi sebelumnya seringkali panjang. Untuk itu tersedia kondisi khusus `else`, yang berarti tepat itu:

```
namaFungsi (x) :
    depend on x
        *kondisi-1* : *ekspresi-1*
        *kondisi-2* : *ekspresi-2*
        else        : *ekspresi-3*
```

setara dengan menuliskan `not (*kondisi-1*) and not (*kondisi-2*)` pada baris terakhir.

Dengan `else`, syarat lengkap otomatis terpenuhi, dan syarat disjoint tinggal berlaku bagi kondisi-kondisi sebelumnya.

Kemudahan ini ada harganya: **`else` menyembunyikan kasus yang diwakilinya.** Pembaca tidak lagi melihat kasus apa yang sesungguhnya ditangani baris terakhir, dan penulisnya sendiri kadang tidak menyadari bahwa ada kasus yang tidak pernah ia pikirkan namun terlanjur tertampung di situ. Pakailah `else` bila sisa kasusnya memang satu kelompok yang wajar, dan hindari bila sisanya sebenarnya beberapa kasus berbeda yang kebetulan berbagi hasil.

## Bentuk `if-then-else`

Khusus untuk dua kasus yang saling melengkapi, tersedia bentuk yang lebih ringkas:

```
if *kondisi* then
    *ekspresi-1*
else
    *ekspresi-2*
```

yang setara dengan:

```
depend on
    *kondisi*       : *ekspresi-1*
    not (*kondisi*) : *ekspresi-2*
```

Bentuk ini sudah dipakai pada `max2` di Bab 2.

Perhatikan bahwa `else` **tidak boleh dihilangkan**. Bentuk `if c then e` tanpa `else` tidak mempunyai arti dalam notasi ini: bila `c` bernilai `false`, tidak ada nilai yang dihasilkan. Dalam paradigma imperatif, `if` tanpa `else` masuk akal karena yang dilewatkan adalah *aksi*, dan "tidak melakukan apa-apa" adalah pilihan yang sah. Dalam paradigma fungsional, yang dihasilkan adalah *nilai*, dan "tidak menghasilkan nilai" bukanlah pilihan.

Gunakan `if-then-else` hanya untuk dua kasus. Untuk tiga kasus atau lebih, `depend on` jauh lebih terbaca daripada `if-then-else` bersarang, dan membuat syarat lengkap serta disjoint dapat diperiksa dengan sekali pandang.

## Contoh 1: Pemilahan Berdasarkan Nilai Ambang

**Persoalan.** Pada tekanan 1 atm, tentukan wujud air berdasarkan suhunya dalam derajat Celcius. Air membeku pada 0 dan mendidih pada 100; di antara keduanya air berwujud cair.

Sebelum menulis apa pun, perhatikan bahwa persoalan ini mengandung dua batas, `0.0` dan `100.0`, dan bahwa persoalan tidak menyatakan dengan tegas ke mana batas itu sendiri termasuk. Keputusan harus diambil, dan setelah diambil harus dituliskan pada spesifikasi. Di sinilah kesalahan paling sering terjadi.

```
JUDUL   Wujud Air pada Tekanan 1 atm
```

```
DEFINISI DAN SPESIFIKASI
    stateOfWater : Real → Character
    { stateOfWater (t) adalah wujud air pada suhu t derajat Celcius dan
      tekanan 1 atm: 'S' bila padat (solid), 'L' bila cair (liquid), 'G' 
      bila uap (gas). Nilai di perbatasan dimiliki oleh wujud pada suhu 
      yang lebih rendah:
      t = 0.0 menghasilkan 'S', dan t = 100.0 menghasilkan 'L'. }
```

```
REALISASI
    stateOfWater (t) :
        depend on t
            t ≤ 0.0                   : 'S'
            (t > 0.0) and (t ≤ 100.0) : 'L'
            t > 100.0                 : 'G'
```

```
APLIKASI
    ⇒ stateOfWater (-10.0)
      'S'
    ⇒ stateOfWater (0.0)
      'S'
    ⇒ stateOfWater (25.0)
      'L'
    ⇒ stateOfWater (100.0)
      'L'
    ⇒ stateOfWater (150.0)
      'G'
```

Periksalah kedua syarat. **Lengkap:** setiap bilangan riil memenuhi salah satu dari `t ≤ 0.0`, `0.0 < t ≤ 100.0`, atau `t > 100.0`. **Disjoint:** ketiganya tidak dapat benar bersamaan, karena masing-masing batas hanya dimiliki oleh satu kasus.

Kelima contoh aplikasi dipilih dengan sengaja: satu di dalam setiap kasus, dan satu tepat pada masing-masing batas. Nilai batas adalah tempat kesalahan bersembunyi, dan karena itu wajib diuji.

> **Catatan.** Menyatakan kategori sebagai karakter `'S'`, `'L'`, `'G'` adalah cara sementara: notasi kita belum mempunyai sarana untuk menyatakan "salah satu dari tiga kemungkinan bernama". Bab 5 akan menyediakannya.

Dalam Haskell:

```haskell
stateOfWater :: Double -> Char
-- stateOfWater t adalah wujud air pada suhu t derajat Celcius, tekanan 1 atm
stateOfWater t
  | t <= 0.0   = 'S'
  | t <= 100.0 = 'L'
  | otherwise  = 'G'
```

Terjemahan ini **tidak** satu lawan satu, dan perbedaannya penting:

- Haskell menuliskan kondisi sebagai *guard* dengan tanda `|`, dan `otherwise` adalah padanan `else`.
- Guard pada Haskell **diperiksa berurutan**: yang pertama bernilai `True` itulah yang dipakai. Karena itu kondisi kedua cukup ditulis `t <= 100.0`, tanpa `t > 0.0`, sebab baris pertama sudah menyaring kasus itu.
- Akibatnya, Haskell **tidak memaksa** syarat disjoint. Teks yang kondisinya tumpang tindih tetap dapat dikompilasi dan dijalankan, dan hasilnya ditentukan oleh urutan penulisan.

Notasi lebih ketat daripada bahasanya di sini, dan kekakuan itu disengaja. Menuliskan kondisi kedua secara lengkap memaksa kita memikirkan batas bawahnya. Yang dituntut sekarang bukan teks terpendek, melainkan teks yang setiap kasusnya terlihat.

## Contoh 2: Pemilahan dengan Prasyarat

**Persoalan.** Tentukan kuadran tempat sebuah titik berada, dari absis dan ordinatnya.

Titik yang terletak pada salah satu sumbu tidak berada dalam kuadran mana pun, demikian pula titik pusat. Kasus-kasus itu tidak dapat dijawab, dan karena itu harus dikeluarkan dari domain melalui prasyarat.

```
JUDUL   Kuadran Sebuah Titik
```

```
DEFINISI DAN SPESIFIKASI
    quadrant : Real, Real → Integer [1..4]
    { quadrant (x, y) adalah nomor kuadran tempat titik (x, y) berada.
      Prasyarat: x ≠ 0.0 and y ≠ 0.0, yaitu titik tidak terletak pada 
      sumbu mana pun }
```

```
REALISASI
    quadrant (x, y) :
        depend on x, y
            (x > 0.0) and (y > 0.0) : 1
            (x < 0.0) and (y > 0.0) : 2
            (x < 0.0) and (y < 0.0) : 3
            (x > 0.0) and (y < 0.0) : 4
```

```
APLIKASI
    ⇒ quadrant (3.0, 2.0)
      1
    ⇒ quadrant (-3.0, 2.0)
      2
    ⇒ quadrant (-3.0, -2.0)
      3
    ⇒ quadrant (3.0, -2.0)
      4
```

Keempat kondisi ini **tidak lengkap** atas seluruh `Real, Real`: nilai `(0.0, 5.0)` tidak memenuhi satu pun. Yang membuatnya sah adalah prasyarat: kelengkapan hanya dituntut atas domain yang tersisa **setelah** prasyarat diberlakukan.

Hubungan antara prasyarat dan analisis kasus berlaku dua arah, dan patut disadari sejak sekarang: setiap kasus yang tidak dapat kita jawab harus dikeluarkan lewat prasyarat, dan setiap prasyarat yang kita tuliskan mempersempit apa yang harus dicakup oleh kasus-kasus kita. Menyusun keduanya bersamaan lebih mudah daripada menyusunnya berurutan.

Godaan yang muncul di sini adalah menuliskan kasus terakhir sebagai `else`: empat kasus menjadi tiga kasus dan satu `else`, dan teksnya lebih pendek. Tahanlah godaan itu. Dengan `else`, titik `(0.0, 5.0)` akan menghasilkan `4` alih-alih melanggar prasyarat secara terbuka. `else` mengubah pelanggaran prasyarat menjadi jawaban yang keliru namun tampak wajar, dan kekeliruan semacam itulah yang paling lama tidak tertemukan.

Dalam Haskell:

```haskell
quadrant :: Double -> Double -> Integer
-- quadrant x y adalah nomor kuadran titik (x,y)
-- Prasyarat: x /= 0.0 && y /= 0.0
quadrant x y
  | (x > 0.0) && (y > 0.0) = 1
  | (x < 0.0) && (y > 0.0) = 2
  | (x < 0.0) && (y < 0.0) = 3
  | (x > 0.0) && (y < 0.0) = 4
```

Notasi menuliskan range sebagai `Integer [1..4]`; Haskell tidak mempunyai padanannya dan hanya menuliskan `Integer`. Pembatasan itu hilang dalam terjemahan, dan tinggal hidup pada spesifikasi.

Sebaliknya, Haskell memberi sesuatu yang tidak dapat diberikan notasi. Guard di atas tidak mencakup seluruh kemungkinan, dan bila diminta, kompilator akan memperingatkan bahwa masih ada kasus yang tak tertangani:

```bash
$ ghc -Wincomplete-patterns Kuadran.hs
```

Peringatan itu tidak berarti programnya salah; di sini justru memang disengaja, karena prasyaratlah yang menutupi sisanya. Yang berharga justru peringatan mesinnya. Setiap kali peringatan semacam ini muncul, jawablah dengan sadar: apakah sisa kasusnya memang tidak mungkin terjadi, atau saya yang lupa memikirkannya?

### Pembatasan pada Domain dan Range

`Integer [1..4]` pada definisi `quadrant` adalah pemakaian pertama sebuah kelengkapan kecil yang akan sering dipakai mulai Bab 5: sebuah type dasar boleh diikuti pembatasan yang mempersempit himpunan nilainya.

| Bentuk | Contoh | Arti |
|---|---|---|
| Selang terbatas | `Integer [1..12]` | nilai bulat dari 1 sampai dengan 12 |
| Selang tak terbatas | `Integer > 0`, `Real ≤ 13.0` | nilai yang dibatasi di satu sisi (batas atas saja atau bawah saja) |

Pembatasan ini adalah bagian dari **spesifikasi**, bukan sesuatu yang dapat diperiksa mesin. Spesifikasi menyatakan janji, dan janji itu tetap harus ditegakkan oleh penulisnya. Menuliskannya tetap berharga: ini memindahkan sebagian prasyarat dari kalimat ke dalam definisi, tempat pembaca pasti melihatnya.

## Evaluasi Ekspresi Kondisional

Evaluasi sebuah ekspresi kondisional berlangsung dalam dua tahap: kondisi-kondisi dievaluasi hingga ditemukan yang bernilai `true`, lalu ekspresi yang bersesuaian dievaluasi. Ekspresi pada kasus lain **tidak** dievaluasi.

Penelusuran `stateOfWater (25.0)`:

```
⇒ stateOfWater (25.0)
  → depend on 25.0                                   { ekspansi stateOfWater }
        25.0 ≤ 0.0                       : 'S'
        (25.0 > 0.0) and (25.0 ≤ 100.0)  : 'L'
        25.0 > 100.0                     : 'G'
  → depend on 25.0                                   { reduksi ≤ }
        false                            : 'S'
        (25.0 > 0.0) and (25.0 ≤ 100.0)  : 'L'
        25.0 > 100.0                     : 'G'
  → depend on 25.0                                   { reduksi >, ≤, and }
        false                            : 'S'
        true                             : 'L'
        25.0 > 100.0                     : 'G'
  → 'L'
```

Karena kondisi-kondisi disjoint, penelusuran boleh berhenti pada kondisi pertama yang benar; tidak perlu memeriksa sisanya. Karena urutan pun tidak berarti, kita boleh memulai dari kondisi mana saja; hasilnya tetap sama.

## Operator `and then` dan `or else`

Sifat "tidak semua bagian dievaluasi" ternyata dibutuhkan juga di dalam sebuah ekspresi Boolean, bukan hanya di antara kasus.

Perhatikan persoalan berikut: tentukan apakah `b` membagi habis `a`. Ekspresi `a mod b = 0` menjawabnya, tetapi `mod` tidak terdefinisi bila `b` bernilai nol. Kita dapat menambahkan prasyarat `b ≠ 0`, tetapi bila kita justru ingin fungsinya menerima nol dan menjawab `false`, prasyarat bukan jawabannya.

Percobaan pertama:

```
isDivisor (a, b) : (b ≠ 0) and (a mod b = 0)
```

Teks ini **tidak benar**. Operator `and` menuntut kedua operannya bernilai, dan bila `b` bernilai nol, operan kedua tidak mempunyai nilai. Ekspresi tidak terdefinisi, meskipun operan pertama sudah bernilai `false`.

Untuk itu tersedia dua operator tambahan:

| Ekspresi | Setara dengan | Arti |
|---|---|---|
| `A and then B` | `if A then B else false` | `B` hanya dievaluasi bila `A` bernilai `true` |
| `A or else B` | `if A then true else B` | `B` hanya dievaluasi bila `A` bernilai `false` |

Dengan `and then`:

```
JUDUL   Pembagi Habis
```

```
DEFINISI DAN SPESIFIKASI
    isDivisor : Integer, Integer → Boolean
    { isDivisor (a, b) bernilai true bila b membagi habis a.
      Bernilai false bila b = 0 }
```

```
REALISASI
    isDivisor (a, b) : (b ≠ 0) and then (a mod b = 0)
```

```
APLIKASI
    ⇒ isDivisor (12, 3)
      true
    ⇒ isDivisor (12, 5)
      false
    ⇒ isDivisor (12, 0)
      false
```

Perhatikan bahwa `and then` **tidak komutatif**: `(a mod b = 0) and then (b ≠ 0)` tetap tidak terdefinisi bila `b` nol. Urutan operan menjadi berarti. Ini adalah satu-satunya tempat dalam notasi ini yang urutan operannya berarti. Karena itu, pakailah `and then` hanya bila memang diperlukan, yaitu bila operan kedua tidak selalu terdefinisi, dan pakailah `and` biasa di tempat lain. Pembaca yang melihat `and then` akan bertanya "mengapa di sini urutan penting?", dan pertanyaan itu pantas selalu ada jawabannya.

Dalam Haskell:

```haskell
isDivisor :: Integer -> Integer -> Bool
-- isDivisor a b bernilai True bila b membagi habis a; False bila b = 0
isDivisor a b = (b /= 0) && (a `mod` b == 0)
```

Haskell hanya mempunyai `&&` dan `||`, dan keduanya **sudah** berperilaku seperti `and then` dan `or else`. Perbedaan yang dibuat notasi kita menghilang dalam terjemahan.

Apakah pembedaan itu berarti sia-sia? Tidak. Pembedaan tersebut ada bukan demi mesin, melainkan demi pembaca: `and then` menyatakan bahwa penulisnya sadar operan kedua tidak selalu terdefinisi, dan sengaja bersandar pada urutan. Dalam Haskell, maksud itu hanya dapat disampaikan lewat komentar. Ini adalah kebalikan dari keadaan pada Contoh 2, tempat bahasa justru lebih kuat daripada notasi. Masing-masing punya bagian yang lebih baik dinyatakan olehnya, dan itulah alasan keduanya dipakai berdampingan.

## Kesalahan yang Sering Terjadi

| Kesalahan | Gejala | Cara memeriksa |
|---|---|---|
| Kasus tidak lengkap | ada masukan yang tidak menghasilkan apa-apa | ambil nilai ekstrem dan nilai batas; apakah tercakup? |
| Kasus tumpang tindih | dua kasus benar bersamaan | untuk setiap pasangan kasus, adakah nilai yang memenuhi keduanya? |
| Batas salah dimiliki | hanya keliru tepat pada nilai batas | uji **setiap** nilai batas, bukan hanya nilai di antaranya |
| `else` menutupi lubang | kasus yang terlupa menghasilkan jawaban yang tampak wajar | tuliskan sementara kondisi terakhir secara lengkap; masih lengkapkah? |
| `if` tanpa `else` | tidak ada nilai bagi sebagian masukan | tidak berlaku dalam notasi ini |
| `and` untuk operan yang tidak selalu terdefinisi | tidak terdefinisi padahal seharusnya bernilai | apakah operan kedua terdefinisi untuk seluruh domain? |

Kesalahan batas layak mendapat perhatian khusus. Kesalahan ini tidak pernah tampak pada contoh aplikasi yang dipilih sembarangan, karena nilai sembarang hampir tidak pernah jatuh tepat di batas. Kebiasaan yang menyelamatkan: **untuk setiap batas, uji nilai tepat pada batas, tepat di bawahnya, dan tepat di atasnya.**

## Latihan

**Membaca.** Untuk setiap realisasi berikut, tentukan definisi dan tuliskan spesifikasinya. Kemudian periksa syarat lengkap dan disjoint. Bila salah satu dilanggar, sebutkan nilai masukan yang membuktikannya.

```
p (n) :
    depend on n
        n < 0  : -1
        n = 0  : 0
        n > 0  : 1

q (x) :
    depend on x
        x ≤ 10  : 'A'
        x ≥ 5   : 'B'
        else    : 'C'

r (a, b) :
    depend on a, b
        a > b : a - b
        a < b : b - a
```

**Memperbaiki.** Fungsi berikut menghitung ongkos kirim menurut tarif yang berlaku. Ketiga contoh aplikasinya menghasilkan jawaban yang benar, dan kasus-kasusnya memenuhi syarat lengkap maupun syarat disjoint. Meskipun demikian, fungsi ini keliru.

```
JUDUL   Ongkos Kirim Paket
```

```
DEFINISI DAN SPESIFIKASI
    deliveryFee : Real → Integer
    { deliveryFee (b) adalah ongkos kirim dalam rupiah bagi paket
      seberat b kilogram, menurut tarif berikut:
          paket ringan : sampai dengan 1 kg        → 10000
          paket sedang : di atas 1 kg s.d. 5 kg    → 20000
          paket berat  : di atas 5 kg              → 35000
      Prasyarat: b > 0.0 }
```

```
REALISASI
    deliveryFee (b) :
        depend on b
            b < 1.0                   : 10000
            (b > 1.0) and (b ≤ 5.0)   : 20000
            else                      : 35000
```

```
APLIKASI
    ⇒ deliveryFee (0.5)
      10000
    ⇒ deliveryFee (3.0)
      20000
    ⇒ deliveryFee (7.0)
      35000
```

1. Temukan satu nilai masukan yang menghasilkan jawaban keliru. Berapa yang dihasilkan fungsi, dan berapa yang seharusnya menurut spesifikasi?
2. Periksa syarat lengkap dan syarat disjoint. Keduanya terpenuhi. Jelaskan mengapa fungsinya tetap salah. Apa sesungguhnya yang dijamin oleh syarat lengkap?
3. Spesifikasi dan realisasi bertentangan mengenai satu nilai. Mana yang Anda perbaiki, dan apa alasannya? Pertimbangkan siapa yang membaca spesifikasi dan siapa yang membaca realisasi.
4. Tuliskan ulang kondisi `else` secara lengkap, sebagai negasi dari kedua kondisi sebelumnya. Setelah ditulis lengkap, apakah kekeliruannya lebih mudah terlihat?
5. Tambahkan contoh aplikasi sehingga setiap kasus **dan setiap nilai batas** terwakili. Berapa contoh yang diperlukan?

**Menulis.** Tuliskan keempat bagian secara lengkap. Nyatakan prasyarat bila ada, periksa kedua syarat, dan pilih contoh aplikasi yang mencakup setiap kasus **beserta setiap nilai batas**.

1. Menghitung nilai mutlak sebuah bilangan bulat, tanpa memakai `abs`.
2. Mengubah nilai angka `0..100` menjadi indeks huruf `'A'`, `'B'`, `'C'`, `'D'`, atau `'E'`. Tentukan sendiri batasnya dan nyatakan pada spesifikasi.
3. Menentukan apakah tiga bilangan bulat positif dapat menjadi panjang sisi sebuah segitiga. Bandingkan dengan jawaban Anda pada Bab 3: apakah ekspresi kondisional membuatnya lebih terbaca, atau justru sebaliknya?
4. Menghitung tarif parkir: dua jam pertama Rp5.000, setiap jam berikutnya Rp3.000, dengan tarif maksimum sehari Rp50.000. Masukan adalah lama parkir dalam jam.
5. Menentukan nilai terbesar dari tiga bilangan bulat, dengan `depend on` dan tanpa memakai `max2`. Bandingkan hasilnya dengan penyelesaian memakai `max2` pada Bab 2, dan sebutkan mana yang lebih mudah diperiksa kebenarannya.

**Menelusuri.** Tuliskan penelusuran lengkap `isDivisor (12, 0)`. Kemudian tuliskan penelusuran `(12 mod 0 = 0) and then (0 ≠ 0)` dan jelaskan apa yang terjadi.

**Merancang.** Pada Bab 3, penelusuran `isLeapYear (1900)` menunjukkan bahwa hasil akhirnya sudah dapat dipastikan sebelum ekspresinya tersisa satu nilai, tetapi hanya setelah kedua operan `and` yang terluar dihitung, dan urutan pemeriksaannya sama sekali tidak terbaca dari teksnya. Tuliskan ulang `isLeapYear` sebagai ekspresi kondisional dengan `depend on`. Apakah versi itu lebih mudah dibaca? Apakah kondisi-kondisinya disjoint? Mana yang akan Anda pilih untuk diktat ini, dan mengapa?
