# List of List

Bab ini berdiri di luar alur utama Diktat, sebagaimana Bab 10, tetapi dengan cara yang berbeda: Bab 10 memperkenalkan sebuah struktur baru, sedangkan bab ini tidak memperkenalkan satu pun type, operasi, atau notasi yang belum ada. Seluruh isinya diturunkan dari Bab 5, 6, 7, dan 10, dan pekerjaan bab ini adalah memperlihatkan penurunan itu.

Bab ini juga dirancang untuk dikerjakan sendiri, bukan untuk diajarkan: sedikit yang dituliskan lengkap, banyak yang ditanyakan. Bahan terdahulu di lingkungan ITB, mengikuti literatur Lisp, memperlakukan topik ini sebagai pokok bahasan tersendiri dengan delapan operasi dasarnya sendiri. Pembaca bab ini mengerjakan seluruh topik tersebut tanpa satu pun operasi baru.

Bab ini mengasumsikan Bab 10 sudah dibaca.

## Dua Hal yang Bernama Sama

Istilah *list of list* dipakai untuk dua struktur yang berbeda, dan keduanya menuntut penanganan yang berlainan.

**Sarang teratur.** Setiap elemen sebuah list adalah sebuah list, dan seluruhnya bertype sama:

```
[[1, 2, 3], [4, 5], [], [6]]
```

Sebuah matriks, sebuah teks sebagai list kata dengan kata sebagai list karakter, sebuah tabel nilai per mahasiswa. Type-nya adalah `List of (List of Integer)`, dan bentuknya sepenuhnya diketahui sebelum nilainya diperiksa: selalu dua lapis.

**Sarang tak teratur.** Sebuah elemen mungkin sebuah bilangan, mungkin pula sebuah list:

```
[1, [2, [3, 4]], 5, []]
```

Kedalamannya tidak seragam dan tidak terbatas. Bentuknya tidak diketahui dari type-nya, melainkan harus diperiksa pada setiap elemen.

Keduanya ditulis dengan lambang yang sama, yaitu kurung siku, sehingga keduanya mudah tertukar. Sarang teratur bukan persoalan baru dan diselesaikan pada bagian berikut; seluruh sisa bab ini menyangkut sarang tak teratur.

## Sarang Teratur: yang Bukan Persoalan Baru

Bab 7 menyatakan bahwa `Element` adalah tempat kosong yang diisi sekali untuk setiap list, dan bahwa isinya boleh berupa type apa pun. Bab 7 sudah mengisinya dengan `Integer`, `Character`, dan `Date`. Mengisinya dengan `List of Integer` tidak menuntut kesepakatan baru apa pun.

```
JUDUL   Jumlah Seluruh Bilangan pada Sebuah Matriks
```

```
DEFINISI DAN SPESIFIKASI
    sumRows : List of (List of Integer) → Integer
    { sumRows (M) adalah jumlah seluruh bilangan pada seluruh baris M,
      nol bila M kosong }
    { Basis-0:  isEmpty (M)       → 0
      Rekurens: not (isEmpty (M)) → sumList (head (M)) + sumRows (tail (M)) }
```

```
REALISASI
    sumRows (M) :
        depend on M
            isEmpty (M)       : 0
            not (isEmpty (M)) : sumList (head (M)) + sumRows (tail (M))
```

```
APLIKASI
    ⇒ sumRows ([])
      0
    ⇒ sumRows ([[], []])
      0
    ⇒ sumRows ([[1, 2, 3], [4, 5], [], [6]])
      21
```

Letakkan realisasi ini berdampingan dengan `length` pada Bab 7. Bentuknya sama persis. Rekurensnya berjalan **satu arah**, yaitu sepanjang `tail`, dan `head (M)` diserahkan kepada `sumList` yang sudah ada. Sarangnya terlihat pada type, tidak pada rekurensnya.

Hal yang sama berlaku bagi seluruh sarang teratur. Bila kedalamannya tetap dan diketahui, setiap lapis dikerjakan oleh fungsinya sendiri, dan setiap fungsi tersebut adalah fungsi list biasa sebagaimana Bab 7. Tiga lapis menuntut tiga fungsi semacam itu.

## Sarang Tak Teratur: Merancang Type-nya

Pada sarang tak teratur, sebuah elemen boleh berupa bilangan **atau** berupa list. Bab 5 sudah memberikan alat untuk menyatakan "salah satu dari", yaitu type alternatif, dan Bab 6 sudah memberikan izin bagi sebuah type untuk menyebut dirinya sendiri.

```
TYPE ITEM
```

```
DEFINISI TYPE
    type Item : Atom ⟨value : Integer⟩
              | Nested ⟨items : List of Item⟩
    { Sebuah Item adalah sebuah bilangan, atau sebuah list yang
      elemennya juga Item }

DEFINISI DAN SPESIFIKASI SELEKTOR
    value : Atom → Integer
    { value (I) memberikan bilangan yang tersimpan pada I }

    items : Nested → List of Item
    { items (I) memberikan list Item yang tersimpan pada I }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makeAtom : Integer → Atom
    { makeAtom (n) membentuk sebuah Item berisi bilangan n }

    makeNested : List of Item → Nested
    { makeNested (L) membentuk sebuah Item berisi list L }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isAtom : Item → Boolean
    { isAtom (I) bernilai true bila I adalah sebuah bilangan }

    isNested : Item → Boolean
    { isNested (I) bernilai true bila I adalah sebuah list }
```

Kotak di atas tidak memuat satu pun konstruktor, selektor, atau predikat list yang baru. `makeNil`, `makeCons`, `head`, `tail`, `isEmpty`, dan `isOneElement` dari Bab 7 berlaku seluruhnya dan tanpa perubahan, karena `List of Item` adalah sebuah list biasa yang `Element`-nya kebetulan bernama `Item`. Yang bertambah hanya satu type, beserta konstruktor, selektor, dan predikatnya sendiri, persis sebagaimana `Shape` pada Bab 5.

> **Peringatan bagi pembaca bahan lama.** Diktat terdahulu di lingkungan ITB memperlakukan *list of list* sebagai type tersendiri dengan seperangkat operasi dasarnya sendiri: `KonsLo`, `KonsL⏺`, `FirstList`, `TailList`, `LastList`, `HeadList`, di samping `IsAtom` dan `IsList`. Delapan nama baru untuk pekerjaan yang, pada diktat ini, dikerjakan oleh dua konstruktor dan dua selektor yang sudah ada sejak Bab 7. Perangkat rangkap tersebut bukan kesalahan penulisnya, melainkan akibat dari tidak tersedianya cara untuk menyatakan bahwa sebuah elemen boleh berupa salah satu dari dua hal. Setelah type alternatif tersedia, perangkat rangkap itu menjadi tidak diperlukan. Bila Anda membaca materi arsip, sepadankan `KonsLo` dengan `makeCons`, `FirstList` dengan `head`, dan `TailList` dengan `tail`.

### Penulisan singkat, dan bahayanya

Sebagaimana Bab 7, menuliskan setiap nilai dengan konstruktornya itu melelahkan. Maka digunakan penulisan singkat:

```
3                    { setara dengan makeAtom (3) }
[]                   { setara dengan makeNested (makeNil) }
[1, 2]               { setara dengan makeNested (makeCons (makeAtom (1),
                                        makeCons (makeAtom (2), makeNil))) }
[1, [2, 3]]          { sebuah Nested berisi sebuah Atom dan sebuah Nested }
```

Penulisan singkat ini memakai kurung siku yang sama dengan penulisan singkat list pada Bab 7, sehingga `[1, 2]` kini mungkin berarti dua hal yang berlainan: sebuah `List of Integer`, atau sebuah `Item` bertype `Nested`. Keduanya tidak dapat dibedakan dari bentuk tertulisnya.

Kerancuan itu melekat pada penulisan singkatnya, bukan pada type-nya. Sepanjang bab ini, setiap kali sebuah struktur sedang dibongkar, tulislah konstruktornya.

### Konvensi penamaan bab ini

Type-nya menyebut dirinya sendiri **melalui** sebuah list: `Item` memuat `List of Item`, dan `List of Item` memuat `Item`. Dua type saling menyebut, dan Bab 6 menyebut keadaan semacam ini rekursi tidak langsung.

Akibatnya, fungsi atas `Item` datang **berpasangan**: satu bekerja atas sebuah `Item`, satu lagi atas sebuah `List of Item`. Sepanjang bab ini pasangan tersebut diberi nama dengan pola yang dipakai pada latihan Bab 10: nama dasar untuk `Item`, nama dasar berakhiran `List` untuk `List of Item`.

## Contoh: Menjumlahkan Seluruh Bilangan

```
JUDUL   Jumlah Seluruh Bilangan di Dalam Sebuah Item
```

```
DEFINISI DAN SPESIFIKASI
    sumItem : Item → Integer
    { sumItem (I) adalah jumlah seluruh bilangan di dalam I,
      betapa pun dalam sarangnya }
    { Basis:    isAtom (I)   → value (I)
      Rekurens: isNested (I) → sumItemList (items (I)) }

    sumItemList : List of Item → Integer
    { sumItemList (L) adalah jumlah seluruh bilangan di dalam seluruh
      elemen L, nol bila L kosong }
    { Basis-0:  isEmpty (L)       → 0
      Rekurens: not (isEmpty (L)) → sumItem (head (L)) + sumItemList (tail (L)) }
```

```
REALISASI
    sumItem (I) :
        depend on I
            isAtom (I)   : value (I)
            isNested (I) : sumItemList (items (I))

    sumItemList (L) :
        depend on L
            isEmpty (L)       : 0
            not (isEmpty (L)) : sumItem (head (L)) + sumItemList (tail (L))
```

```
APLIKASI
    ⇒ sumItem (makeAtom (7))
      7
    ⇒ sumItem ([])
      0
    ⇒ sumItem ([1, [2, [3, 4]], 5])
      15
    ⇒ sumItem ([[], [[]]])
      0
```

**Setiap fungsi mengikuti satu type, dan hanya satu.** `sumItem` menganalisis kasus atas kedua alternatif `Item`; `sumItemList` menganalisis kasus atas kedua alternatif `List`. Tidak ada satu pun kondisi yang mencampur keduanya. Kelengkapan dan disjointness kedua analisis kasus itu dijamin oleh type-nya masing-masing, sebagaimana Bab 5 menyatakannya.

**Konvergensinya terjamin dari dua arah.** `items (I)` menghasilkan sebuah list, dan `sumItemList` selalu memanggil dirinya atas `tail (L)` yang lebih pendek, atau memanggil `sumItem` atas `head (L)` yang berada satu lapis lebih dalam. Setiap langkah mengurangi salah satu dari dua ukuran: panjang list yang tersisa, atau kedalaman sarang yang tersisa. Keduanya berhingga.

Penelusuran `sumItem ([1, [2]])`, dengan konstruktor:

```
⇒ sumItem (makeNested (makeCons (makeAtom (1),
                       makeCons (makeNested (makeCons (makeAtom (2), makeNil)),
                                 makeNil))))
  → sumItemList (makeCons (makeAtom (1), makeCons (makeNested (...), makeNil)))
                                                       { ekspansi, isNested }
  → sumItem (makeAtom (1)) + sumItemList (makeCons (makeNested (...), makeNil))
                                                       { ekspansi, rekurens }
  → 1 + sumItemList (makeCons (makeNested (...), makeNil))
                                                       { ekspansi, isAtom }
  → 1 + (sumItem (makeNested (...)) + sumItemList (makeNil))
                                                       { ekspansi, rekurens }
  → 1 + (sumItemList (makeCons (makeAtom (2), makeNil)) + sumItemList (makeNil))
                                                       { ekspansi, isNested }
  → 1 + ((2 + 0) + 0)                                  { beberapa langkah }
  → 3
```

Bandingkan bentuk penelusuran ini dengan penelusuran `length` pada Bab 7. Pada `length`, ekspresi tumbuh sebagai sebuah rantai lurus. Di sini ia tumbuh **bercabang**, dan bentuk bercabang itu adalah bentuk struktur yang sedang ditelusuri.

## Rekurens dengan Dua Arah

Pasangan `sumItem` dan `sumItemList` bukan satu-satunya bentuk yang mungkin. Bentuk kedua menghapus salah satunya dan mengerjakan seluruhnya dalam satu fungsi atas `List of Item`:

```
REALISASI (BENTUK KEDUA)
    sumAll (L) :
        depend on L
            isEmpty (L)                                    : 0
            not (isEmpty (L)) and then isAtom (head (L))   : value (head (L))
                                                             + sumAll (tail (L))
            not (isEmpty (L)) and then isNested (head (L)) : sumAll (items (head (L)))
                                                             + sumAll (tail (L))
```

Bentuk inilah yang dipakai bahan terdahulu dan literatur Lisp. Ia lebih pendek: satu fungsi, bukan dua.

Kasus ketiga memuat **dua** aplikasi rekursif dalam satu ekspresi, dan keduanya mengerjakan hal yang berlainan. `sumAll (items (head (L)))` bergerak **turun**, masuk ke dalam sarang. `sumAll (tail (L))` bergerak **maju**, sepanjang list yang sama. Tidak ada fungsi mana pun pada Bab 7 yang memiliki sifat ini; di sana rekursi selalu bergerak maju saja.

Perbandingan kedua bentuk:

| | Pasangan `sumItem`/`sumItemList` | Tunggal `sumAll` |
|---|---|---|
| Banyaknya fungsi | dua | satu |
| Analisis kasus per fungsi | atas satu type saja | atas dua type sekaligus |
| Kelengkapan dan disjointness | dijamin type-nya | harus dijaga sendiri |
| `and then` | tidak diperlukan | diperlukan, dan wajib |
| Arah rekursi | terpisah pada dua fungsi | bercampur dalam satu ekspresi |
| Padanan langsung ke type-nya | ada | tidak ada |

Baris keempat menunjukkan: pada bentuk tunggal, `isAtom (head (L))` tidak terdefinisi bila `L` kosong, sehingga `and` tidak cukup dan `and then` menjadi wajib, persis sebagaimana Bab 4 menyatakannya. Pada bentuk pasangan, keadaan tersebut tidak pernah muncul, karena `head (L)` hanya disebut sesudah kekosongan `L` disingkirkan oleh fungsi yang lain.

Diktat ini memilih bentuk pasangan. Alasannya bukan bahwa bentuk tunggal keliru—keduanya benar dan menghasilkan nilai yang sama—melainkan bahwa bentuk pasangan **mengikuti bentuk type-nya**. Dua type yang saling menyebut dilayani oleh dua fungsi yang saling memanggil. Aturan yang sama dipakai sejak Bab 5.

## Pertemuan dengan Bab 10

Letakkan kedua definisi type berdampingan:

```
type Tree : Node ⟨info : Element, children : List of Tree⟩

type Item : Atom   ⟨value : Integer⟩
          | Nested ⟨items : List of Item⟩
```

Keduanya menyebut dirinya sendiri melalui sebuah list. Keduanya menempatkan basis rekurensnya di dalam list tersebut, bukan pada sebuah alternatif tersendiri. Keduanya melahirkan fungsi berpasangan.

Perbedaannya hanya satu: **letak nilainya**. Sebuah `Tree` menyimpan `info` pada setiap simpul, termasuk simpul dalam. Sebuah `Item` menyimpan nilai hanya pada daunnya; simpul dalamnya, yaitu `Nested`, tidak membawa apa pun selain anak-anaknya.

Sebuah *list of list* dengan sarang tak teratur, karenanya, adalah sebuah pohon n-aire yang nilainya seluruhnya berada di daun. Kedua bab ini membahas satu struktur yang sama, dan bab ini menemukannya dari arah yang berlawanan: Bab 10 berangkat dari gagasan hierarki, bab ini berangkat dari gagasan list yang elemennya boleh berupa list.

Dua pertanyaan yang pantas dijawab sendiri, dan tidak dijawab di sini:

1. `Nested (makeNil)`, yaitu `[]`, adalah sebuah `Item` yang sah dan tidak memuat satu pun bilangan. Padanan apakah yang dimilikinya pada `Tree`? Bab 10 menyatakan bahwa tidak ada pohon kosong. Apakah pernyataan tersebut masih berlaku di sini, dan bila ya, apa akibatnya bagi range `sumItem`?
2. Bila sebuah persoalan dapat dinyatakan dengan `Tree` maupun dengan `Item`, mana yang lebih jujur? Pertimbangkan dua persoalan: struktur bab sebuah buku, dan sebuah ekspresi aritmatika. Bab 10 memodelkan yang kedua sebagai `Tree` dengan operator pada simpul dalam. Dapatkah ia dimodelkan sebagai `Item`, dan apa yang hilang bila demikian?

## Soal-soal

Seluruh fungsi berikut dituliskan lengkap sebagai contoh pada bahan terdahulu dan pada literatur Lisp. Di sini semuanya diserahkan kepada pembaca.

Untuk setiap nomor, tuliskan keempat bagian secara lengkap. Pakailah bentuk pasangan. Sebutkan basis kedua fungsinya, dan sertakan contoh aplikasi yang mencakup sekurang-kurangnya: sebuah atom tunggal, `[]`, sebuah sarang datar, dan sebuah sarang berlapis tiga.

1. **`countAtoms`**: mencacah banyaknya bilangan di dalam sebuah `Item`, betapa pun dalam sarangnya. Sesudah selesai, bandingkan realisasi Anda dengan `sumItem` pada bab ini. Berapa lambang yang berbeda di antara keduanya? Pertanyaan yang sama dibangkitkan oleh `sumList` dan `productList` pada Bab 7, dan jawabannya menunggu pada Bab 8.

2. **`depth`**: menghasilkan kedalaman sarang sebuah `Item`. Sebelum menulis apa pun, tetapkanlah lebih dahulu: berapa kedalaman `makeAtom (3)`, dan berapa kedalaman `[]`? Kedua jawaban itu adalah basis Anda, dan seluruh sisa fungsi bergantung padanya. Bandingkan kesulitan ini dengan `height` pada Bab 10.

3. **`flatten`**: menghasilkan sebuah `List of Integer` yang berisi seluruh bilangan di dalam sebuah `Item` dengan urutan kiri ke kanan, tanpa sarang. Hasilnya bertype **lain** dari masukannya. Anda memerlukan `append` dari latihan Bab 7. Sesudah selesai, jawablah: mengapa `makeCons` saja tidak cukup di sini, padahal pada seluruh fungsi Bab 7 yang menghasilkan list ia selalu cukup?

4. **`maxItem`**: menghasilkan bilangan terbesar di dalam sebuah `Item`. Fungsi ini **tidak terdefinisi** bagi sebagian masukan. Sebutkan bagi masukan yang mana, nyatakan prasyaratnya dengan cermat, dan bandingkan dengan cara `maxList` pada Bab 7 mengeluarkan list kosong dari domainnya. Prasyarat di sini lebih sukar dinyatakan daripada di sana; jelaskan mengapa.

5. **`isEqualItem`**: memeriksa apakah dua buah `Item` sama, yaitu berbentuk sarang sama dan berisi bilangan sama pada tempat yang sama. Analisis kasusnya menyangkut **dua** parameter sekaligus. Berapa kasus yang harus dijawab, dan berapa di antaranya menghasilkan `false` tanpa rekursi?

6. **`removeValue`**: menghasilkan sebuah `Item` yang sama dengan masukannya, kecuali seluruh kemunculan sebuah bilangan tertentu dihapus, pada segala kedalaman. Struktur sarangnya dipertahankan, sehingga `removeValue (1, [[1], 2])` menghasilkan `[[], 2]` dan bukan `[2]`. Fungsi ini dikenal luas dengan nama `rember*` [9]. Sesudah selesai, jawablah: apa yang harus diubah agar sarang yang menjadi kosong ikut terhapus, dan mengapa perubahan tersebut lebih sukar daripada tampaknya?

## Catatan Sejarah

Struktur pada bab ini adalah *S-expression*, dan berasal dari Lisp (1958), bahasa fungsional tertua yang masih dipakai. Di sana ia bukan sebuah topik lanjut, melainkan satu-satunya struktur data yang ada, dan sekaligus bentuk penulisan program itu sendiri: sebuah program Lisp adalah sebuah S-expression, sehingga program dapat diperlakukan sebagai data tanpa perkakas tambahan apa pun. Gagasan tersebut dibahas panjang lebar dalam [6].

Lisp tidak mempunyai sistem type, sehingga `IsAtom` dan `IsList` di sana adalah pemeriksaan yang dilakukan pada saat eksekusi terhadap nilai yang bentuknya tidak dijamin oleh apa pun. Bahan terdahulu mewarisi keadaan tersebut beserta perangkat operasi rangkapnya. Begitu sarang tak teratur dinyatakan sebagai type alternatif yang rekursif, perangkat rangkap itu tidak lagi diperlukan, dan yang tersisa adalah dua alternatif dengan konstruktor dan selektornya sendiri, tidak berbeda dari `Shape` pada Bab 5.

Sejumlah fungsi pada bagian sebelumnya, khususnya `removeValue`, dikenal luas dari [9], yang membahas struktur ini secara panjang lebar melalui tanya jawab dan tanpa satu pun definisi type.

## Kesalahan yang Sering Terjadi

| Kesalahan | Gejala | Cara memeriksa |
|---|---|---|
| Sarang teratur diperlakukan sebagai tak teratur | type alternatif dibuat padahal tidak diperlukan | apakah kedalamannya diketahui dari type-nya? |
| Satu fungsi dipakai untuk dua type | analisis kasus mencampur `isEmpty` dengan `isAtom` | apakah setiap fungsi mengikuti satu type saja? |
| `and` dipakai pada bentuk tunggal | tidak terdefinisi pada list kosong | adakah kondisi yang memakai `head (L)`? |
| Rekursi hanya bergerak maju | bilangan di dalam sarang tidak ikut terhitung | apakah `items (head (L))` pernah direkursi? |
| Rekursi hanya bergerak turun | elemen sesudah sarang pertama terabaikan | apakah `tail (L)` pernah direkursi? |
| `makeCons` dipakai untuk menggabung dua list | hasil `flatten` bersarang, bukan datar | apakah yang disambung sebuah elemen atau sebuah list? |
| Kasus `[]` disamakan dengan kasus atom | `depth` dan `maxItem` keliru pada sarang kosong | apakah `makeNested (makeNil)` diuji tersendiri? |
| Penulisan singkat dipakai pada penelusuran | struktur sarang tidak terlihat, kekeliruan tersembunyi | apakah konstruktor dituliskan saat dibongkar? |

## Ringkasan Padanan

```haskell
data Item = Atom Integer
          | Nested [Item]
          deriving Show

sumItem :: Item -> Integer
-- sumItem i adalah jumlah seluruh bilangan di dalam i
sumItem (Atom n)   = n
sumItem (Nested l) = sumItemList l

sumItemList :: [Item] -> Integer
-- sumItemList l adalah jumlah seluruh bilangan di dalam elemen l
sumItemList []       = 0
sumItemList (i : is) = sumItem i + sumItemList is
```

**Rekursi tidak langsung pada type-nya lenyap.** Notasi menuntut `List of Item` disebut sebagai type tersendiri; Haskell menuliskannya `[Item]`, dan karena `[]` sudah tersedia sebagai type bawaan, hanya `Item` yang perlu didefinisikan. Yang pada notasi tampak sebagai dua type yang saling menyebut, di sini tampak sebagai satu type saja.

**Pasangan fungsinya dapat diciutkan menjadi satu, dengan perkakas Bab 8:**

```haskell
sumItem :: Item -> Integer
sumItem (Atom n)   = n
sumItem (Nested l) = sum (map sumItem l)
```

`map sumItem l` mengerjakan seluruh isi `sumItemList`, dan `sum` menjumlahkannya. Pasangan fungsi yang dituntut oleh dua type yang saling menyebut adalah sebuah pola berulang, dan fungsi orde tinggi Bab 8 mengangkat pola semacam itu. Hal yang sama berlaku bagi `countAtoms` dan `flatten`, yang masing-masing menjadi satu baris dengan `sum . map` dan `concatMap`.

**Yang tetap tidak dapat dinyatakan** adalah prasyarat `maxItem`, yaitu bahwa sebuah `Item` sekurang-kurangnya memuat satu bilangan. Prasyarat tersebut hidup pada spesifikasi dan tidak diperiksa oleh apa pun, persis sebagaimana invarian pohon terurut pada Bab 10.

| Notasi fungsional | Haskell |
|---|---|
| `type Item : Atom ⟨value⟩ \| Nested ⟨items⟩` | `data Item = Atom Integer \| Nested [Item]` |
| `makeAtom (n)` | `Atom n` |
| `makeNested (L)` | `Nested l` |
| `isAtom (I)`, `value (I)` | pola `(Atom n)` |
| `isNested (I)`, `items (I)` | pola `(Nested l)` |
| pasangan `f`/`fList` | `f` tunggal dengan `map` |
| `List of (List of Integer)` | `[[Integer]]` |

## Latihan

**Membaca.** Untuk setiap pasangan berikut, tentukan definisinya, tuliskan spesifikasinya, sebutkan basis kedua fungsinya, lalu usulkan nama yang sesuai konvensi.

```
p (I) :
    depend on I
        isAtom (I)   : makeAtom (value (I))
        isNested (I) : makeNested (pList (items (I)))

pList (L) :
    depend on L
        isEmpty (L)       : makeNil
        not (isEmpty (L)) : addLast (pList (tail (L)), p (head (L)))


q (I) :
    depend on I
        isAtom (I)   : 1
        isNested (I) : qList (items (I))

qList (L) :
    depend on L
        isEmpty (L)       : 0
        not (isEmpty (L)) : max2 (q (head (L)), qList (tail (L)))
```

Untuk `p`, terapkan atas `[1, [2, 3]]` dan tuliskan hasilnya. Kemudian jawab: apakah `p (p (I))` sama dengan `I`? Untuk `q`, terapkan atas `makeAtom (7)`, `[]`, dan `[1, [2, [3]]]`. Ketiga hasilnya cukup untuk menyimpulkan apa yang dihitung `q`; jelaskan mengapa hasil kedua adalah yang paling menerangkan.

**Memperbaiki.** Fungsi berikut seharusnya mencacah seluruh bilangan di dalam sebuah `Item`.

```
REALISASI
    countAtoms (L) :
        depend on L
            isEmpty (L)                                    : 0
            not (isEmpty (L)) and then isAtom (head (L))   : 1 + countAtoms (tail (L))
            not (isEmpty (L)) and then isNested (head (L)) : countAtoms (items (head (L)))
```

```
APLIKASI
    ⇒ countAtoms ([])
      0
    ⇒ countAtoms ([1, 2, 3])
      3
    ⇒ countAtoms ([[1, 2]])
      2
```

1. Ketiga contoh aplikasi menghasilkan jawaban yang benar. Temukan sebuah masukan yang menghasilkan jawaban keliru, dan sebutkan apa yang dihasilkan serta apa yang seharusnya.
2. Kesalahannya terletak pada kasus ketiga. Sebutkan arah rekursi mana yang ada dan arah mana yang hilang.
3. Jelaskan mengapa `[[1, 2]]` tidak menampakkannya, dan sebutkan ciri masukan yang diperlukan agar kesalahan semacam ini tampak.
4. Perbaiki, lalu tuliskan ulang seluruhnya dalam bentuk pasangan. Jawablah: apakah kesalahan ini mungkin terjadi pada bentuk pasangan? Apa yang mencegahnya, dan apakah pencegahan itu berupa jaminan atau sekadar kemudahan?

**Menulis.** Kerjakan seluruh soal pada bagian *Soal-soal*. Untuk nomor 3 dan 6, sertakan pula penelusuran lengkap atas sebuah masukan berlapis dua.

**Menelusuri.**

1. Telusuri `sumItem ([1, [2, [3]]])` secara lengkap dengan konstruktor. Hitung aplikasi `sumItem` dan aplikasi `sumItemList` secara terpisah. Kemudian nyatakan keduanya sebagai fungsi dari banyaknya `Atom` dan banyaknya `Nested` pada masukannya.
2. Telusuri `sumAll ([1, [2, [3]]])` pada bentuk tunggal. Hitung aplikasinya, dan bandingkan jumlahnya dengan hasil nomor 1. Apakah kedua bentuk mengerjakan pekerjaan yang sama banyak?
3. Telusuri `flatten ([[1], [2]])` menurut realisasi Anda sendiri, dan hitung aplikasi `append` secara terpisah. Bandingkan hasilnya dengan hitungan `reverse` versi pertama pada Bab 7. Apakah kedua fungsi tersebut mahal karena alasan yang sama?

**Merancang.** Tiga pertanyaan, ketiganya tanpa jawaban tunggal yang benar.

1. Bab ini membatasi atom pada `Integer`. Bahan terdahulu mengizinkan pula atom simbolik, sehingga `[a, [b, c]]` adalah nilai yang sah. Rancanglah type yang mengizinkan keduanya, lalu tuliskan definisi dan spesifikasi (tanpa realisasi) `sumItem` atasnya. Apa yang harus dijawab oleh spesifikasi Anda yang sebelumnya tidak perlu dijawab? Kemudian pertimbangkan: keluwesan yang diperoleh sepadan dengan apa yang harus dibayar?
2. Bagian *Pertemuan dengan Bab 10* menyatakan bahwa `Item` adalah `Tree` yang nilainya seluruhnya di daun. Tuliskan sepasang fungsi penerjemah, `itemToTree` dan `treeToItem`, sekurang-kurangnya sebagai definisi dan spesifikasi. Salah satunya tidak dapat ditulis tanpa prasyarat; sebutkan yang mana dan apa prasyaratnya. Apa yang diberitahukan ketidaksimetrisan tersebut tentang hubungan kedua type itu?
3. Seluruh bab ini dapat dihapus tanpa satu pun bab lain berubah. Argumenkan kedua pilihan: mempertahankannya sebagai bab, atau menciutkannya menjadi satu soal latihan pada Bab 7. Apa yang hilang pada masing-masing? Dalam menjawab, pertimbangkan bahwa bahan terdahulu memperlakukannya sebagai pokok bahasan penuh dengan delapan operasi dasar tersendiri.
