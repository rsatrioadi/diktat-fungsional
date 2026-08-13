# Fungsi Sebagai Nilai

Bab 7 ditutup dengan sebuah pertanyaan: apa yang harus dapat diperlakukan sebagai **nilai**, agar sepasang fungsi yang hampir kembar dapat dituliskan sekali saja?

Jawabannya adalah **fungsi itu sendiri**.

Sampai Bab 7, sebuah nilai selalu berupa bilangan, karakter, boolean, sebuah type bentukan, atau sebuah list. Nilai dapat diberi nama lewat `let`, diserahkan sebagai argumen, dan dihasilkan sebagai range. Fungsi tidak pernah diperlakukan demikian. Fungsi adalah sesuatu yang *diaplikasikan*, bukan sesuatu yang *diberikan*.

Pembatasan itu tidak pernah dinyatakan sebagai aturan, dan memang tidak pernah ada aturannya. Bab 2 menyatakan bahwa fungsi adalah pemetaan dari satu type ke type lain, dan tidak menyatakan apa pun tentang type apa saja yang boleh menjadi domain atau range. Bila fungsi adalah nilai, maka sebuah fungsi boleh menerima fungsi, dan boleh menghasilkan fungsi.

Fungsi yang menerima atau menghasilkan fungsi disebut **fungsi orde tinggi**.

Bab ini tidak memperkenalkan satu pun persoalan baru. Setiap fungsi yang ditulis di sini sudah pernah ditulis pada Bab 6 atau Bab 7. Yang berubah hanyalah cara menuliskannya, dan seberapa banyak yang harus ditulis.

## Fungsi sebagai Parameter

Bab 7 menuliskan `squareAll`, yang mengkuadratkan setiap elemen sebuah list. Seandainya kita membutuhkan fungsi yang menegatifkan setiap elemen, atau yang menambahkan satu pada setiap elemen, teksnya akan hampir sama persis. Yang berbeda hanya satu hal: apa yang dikerjakan terhadap setiap elemen.

Jadikan hal itu sebagai parameter.

```
JUDUL   Menerapkan Sebuah Fungsi pada Setiap Elemen
```

```
DEFINISI DAN SPESIFIKASI
    applyToAll : (Element → Element), List → List
    { applyToAll (f, L) adalah list yang elemen ke-i-nya adalah f
      diaplikasikan terhadap elemen ke-i dari L. Banyaknya elemen
      dan urutannya tidak berubah }
    { Basis-0:  isEmpty (L)       → []
      Rekurens: not (isEmpty (L)) → f (head (L)) diikuti applyToAll (f, tail (L)) }
```

```
REALISASI
    applyToAll (f, L) :
        depend on L
            isEmpty (L)       : makeNil
            not (isEmpty (L)) : makeCons (f (head (L)), applyToAll (f, tail (L)))
```

```
APLIKASI
    ⇒ applyToAll (square, [1, 2, 3])
      [1, 4, 9]
    ⇒ applyToAll (abs, [-1, 2, -3])
      [1, 2, 3]
```

Perhatikan baris definisinya. Parameter pertama bertype `(Element → Element)`, yaitu **sebuah fungsi** dari `Element` ke `Element`. Kurung di sekelilingnya **wajib** dituliskan, sebagaimana kurung pada operan `not` di Bab 3 dan dengan alasan yang sama: agar tidak ada keraguan sedikit pun tentang sampai di mana sebuah panah berlaku.

Perhatikan pula bahwa realisasinya adalah realisasi `squareAll` dengan `square` diganti oleh `f`. Tidak ada mekanisme baru. Satu-satunya hal yang baru: `f` datang dari luar.

Argumen pada contoh aplikasi adalah `square` dan `abs`, dituliskan **tanpa kurung dan tanpa argumen**. `square` di situ bukan aplikasi `square`, melainkan fungsi `square` itu sendiri. Perbedaan antara `square` dan `square (3)` adalah perbedaan antara sebuah fungsi dan sebuah bilangan, dan mulai sekarang keduanya sama-sama dapat dituliskan.

## Ekspresi Lambda

Menuliskan `applyToAll (square, L)` menuntut adanya fungsi bernama `square`. Untuk fungsi yang hanya dipakai sekali di satu tempat, mengarangkan nama justru menambah pekerjaan tanpa menambah kejelasan. Bab 3 menghadapi persoalan yang serupa untuk nilai, dan menjawabnya dengan nama antara. Di sini jawabannya adalah **membuang namanya sama sekali**.

Sebuah **ekspresi lambda** adalah fungsi yang dituliskan tanpa nama:

```
λ x : *ekspresi*
λ (x, y) : *ekspresi*
```

Dibaca: "fungsi yang, diberi `x`, menghasilkan *ekspresi*". Tanda `:` adalah tanda yang sama dengan yang dipakai pada realisasi fungsi dan pada `let`, dan artinya pun sama.

```
APLIKASI
    ⇒ applyToAll (λ x : x * x, [1, 2, 3])
      [1, 4, 9]
    ⇒ applyToAll (λ x : x + 1, [1, 2, 3])
      [2, 3, 4]
    ⇒ (λ x : x * 2) (5)
      10
```

Baris ketiga memperlihatkan bahwa sebuah lambda dapat langsung diaplikasikan. Bentuk itu jarang berguna, tetapi ia menegaskan bahwa lambda adalah fungsi biasa yang kebetulan tidak diberi nama.

**Sebuah lambda tidak dapat rekursif.** Realisasi rekursif menuntut nama, karena rekurens bekerja dengan menyebut fungsi yang sedang didefinisikan. Fungsi tanpa nama tidak mempunyai apa pun untuk disebut. Setiap fungsi rekursif pada Bab 6 dan Bab 7 karenanya harus tetap diberi nama, dan lambda hanya berguna bagi bagian yang **tidak** rekursif.

**Kapan memberi nama, kapan tidak.** Pertanyaannya sama dengan pertanyaan Bab 3 tentang nama antara dan fungsi antara: *apakah hal ini pantas diberi nama dan dijelaskan kepada orang lain?* `λ x : x * x` sudah terbaca sepenuhnya dari bentuknya, sehingga menamainya tidak menambah apa-apa. Sebaliknya, sebuah lambda sepanjang tiga baris yang menghitung sesuatu yang mempunyai nama dalam persoalannya hampir selalu lebih baik dijadikan fungsi antara bernama, lengkap dengan definisi dan spesifikasinya. Lambda menghemat nama, dan nama tidak selalu pantas dihemat.

## Pola Pertama: `map`

`applyToAll` sudah merupakan pola umum, dan namanya yang lazim dipakai adalah `map`. Mulai sekarang diktat ini memakai nama itu.

```
DEFINISI DAN SPESIFIKASI
    map : (Element → Element), List → List
    { map (f, L) adalah list yang elemen ke-i-nya adalah f (elemen ke-i L).
      Banyaknya elemen dan urutannya tidak berubah }
```

```
REALISASI
    map (f, L) :
        depend on L
            isEmpty (L)       : makeNil
            not (isEmpty (L)) : makeCons (f (head (L)), map (f, tail (L)))
```

Sekarang seluruh fungsi Bab 7 yang berbentuk "ubah setiap elemen" menjadi satu baris:

```
squareAll (L)  : map (λ x : x * x, L)
negateAll (L)  : map (λ x : -x, L)
absAll (L)     : map (abs, L)
```

Dua hal yang dijamin oleh `map` dan pantas disadari: hasilnya **selalu** sepanjang masukannya, dan urutannya **selalu** terjaga. Pembaca yang melihat `map` sudah mengetahui kedua hal itu tanpa membaca lambdanya. Inilah keuntungan sesungguhnya dari memberi nama pada sebuah pola. Yang dihemat bukan huruf, melainkan hal-hal yang harus diperiksa ulang oleh pembaca.

## Pola Kedua: `filter`

```
DEFINISI DAN SPESIFIKASI
    filter : (Element → Boolean), List → List
    { filter (p, L) adalah list yang berisi seluruh elemen L yang
      memenuhi p, dengan urutan yang sama. Banyaknya elemen dapat berkurang }
```

```
REALISASI
    filter (p, L) :
        depend on L
            isEmpty (L)                                   : makeNil
            not (isEmpty (L)) and then (p (head (L)))     : makeCons (head (L), filter (p, tail (L)))
            not (isEmpty (L)) and then not (p (head (L))) : filter (p, tail (L))
```

```
APLIKASI
    ⇒ filter (λ x : x > 0, [1, -2, 3])
      [1, 3]
    ⇒ filter (isEven, [1, 2, 3, 4])
      [2, 4]
```

Parameter `p` bertype `(Element → Boolean)`, yaitu sebuah **predikat**, sesuai istilah Bab 3.

Perhatikan bahwa rekurens dengan dua kasus dari `keepPositive` tidak hilang. Ia berpindah ke dalam `filter`, dan ditulis **sekali** di sana. Inilah yang sesungguhnya terjadi setiap kali sebuah pola diangkat: pekerjaannya tidak berkurang, tetapi ia berhenti diulang-ulang.

Perhatikan pula bahwa `and then` tetap diperlukan, dengan alasan yang persis sama seperti pada Bab 7.

`keepPositive` kini menjadi `filter (λ x : x > 0, L)`.

## Pola Ketiga: `fold`

Bab 7 meletakkan `sumList` dan `productList` berdampingan dan menunjukkan bahwa keduanya berbeda hanya pada dua tempat: **nilai basisnya** dan **operatornya**. Jadikan keduanya parameter.

```
DEFINISI DAN SPESIFIKASI
    fold : (Element, Element → Element), Element, List → Element
    { fold (g, z, L) menggabungkan seluruh elemen L dengan g, dimulai
      dari elemen terakhir dan berakhir pada elemen pertama, dengan z
      sebagai nilai bagi list kosong:
      fold (g, z, [a, b, c]) adalah g (a, g (b, g (c, z))) }
    { Basis-0:  isEmpty (L)       → z
      Rekurens: not (isEmpty (L)) → g (head (L), fold (g, z, tail (L))) }
```

```
REALISASI
    fold (g, z, L) :
        depend on L
            isEmpty (L)       : z
            not (isEmpty (L)) : g (head (L), fold (g, z, tail (L)))

APLIKASI
    ⇒ fold (λ (a, b) : a + b, 0, [1, 2, 3])
      6
    ⇒ fold (λ (a, b) : a * b, 1, [1, 2, 3])
      6
    ⇒ fold (λ (a, b) : 1 + b, 0, [7, 8, 9])
      3
```

Sekarang seluruh fungsi Bab 7 yang menghasilkan nilai tunggal menjadi satu baris:

```
sumList (L)     : fold (λ (a, b) : a + b, 0, L)
productList (L) : fold (λ (a, b) : a * b, 1, L)
length (L)      : fold (λ (a, b) : 1 + b, 0, L)
```

Perhatikan `length`. Lambdanya mengabaikan `a` sama sekali dan hanya menambahkan satu, yang memang persis yang dikerjakan `length` pada Bab 7. Bahwa pencacahan ternyata adalah penjumlahan yang mengabaikan isinya adalah pengamatan yang tidak terlihat sampai kedua fungsi dituliskan dalam bentuk yang sama.

**Sekarang aturan nilai basis dari Bab 7 dapat dijelaskan.** Bab 7 menyatakan bahwa nilai basis harus netral terhadap operator rekurensnya: nol untuk penjumlahan, satu untuk perkalian. Dalam `fold`, nilai itu adalah `z`, dan ia bukan lagi bagian dari realisasi melainkan sebuah argumen yang harus dipilih pemakainya. Aturan yang pada Bab 7 berupa nasihat, di sini menjadi kewajiban yang terlihat: `z` adalah jawaban bagi list kosong, dan memilihnya keliru adalah kesalahan yang hanya menampakkan diri pada list kosong.

`fold` adalah yang paling umum di antara ketiga pola ini. `map` maupun `filter` dapat dituliskan dengan `fold`, dan salah satunya ditanyakan pada Latihan.

## Arah Lipatan

`fold` di atas menggabungkan dari elemen **terakhir** menuju elemen pertama. Itulah bentuk yang dimiliki setiap fungsi pada Bab 7, karena setiap rekurensnya menggabungkan `head (L)` dengan hasil atas `tail (L)`, sehingga penggabungan sesungguhnya baru terjadi setelah rekurens mencapai dasar.

Ada satu fungsi pada Bab 7 yang tidak berbentuk demikian, yaitu `reverseOnto`. Ia membawa hasil sementara **maju** sebagai akumulator, sehingga penggabungan terjadi dari elemen pertama menuju elemen terakhir. Bentuk itu adalah lipatan ke arah yang berlawanan.

Kedua arah menghasilkan jawaban yang sama bila operatornya asosiatif dan nilai netralnya benar, misalnya pada penjumlahan. Untuk operator lain, keduanya berbeda. Bahasa acuan menyediakan keduanya dengan nama `foldr` dan `foldl`, dan pemilihan di antaranya mempunyai akibat terhadap eksekusi yang cukup halus untuk pantas dibicarakan tersendiri. Bab 9 dan Suplemen S10 membahasnya. Diktat ini memakai satu arah saja, yaitu arah yang sudah dikenal pembaca dari seluruh Bab 7.

## Fungsi sebagai Hasil

Sebuah fungsi boleh pula **menghasilkan** fungsi.

```
JUDUL   Pembuat Fungsi Penambah
```

```
DEFINISI DAN SPESIFIKASI
    makeAdder : Integer → (Integer → Integer)
    { makeAdder (n) adalah sebuah fungsi yang, diberi sebuah bilangan
      bulat, menghasilkan bilangan itu ditambah n }
```

```
REALISASI
    makeAdder (n) : λ x : x + n
```

```
APLIKASI
    ⇒ makeAdder (3) (10)
      13
    ⇒ map (makeAdder (1), [1, 2, 3])
      [2, 3, 4]
```

Bacalah baris definisinya dengan cermat. Range `makeAdder` adalah `(Integer → Integer)`, yaitu sebuah fungsi. Kurungnya wajib.

Perhatikan aplikasi pertama. `makeAdder (3)` menghasilkan sebuah fungsi, dan fungsi itu kemudian diaplikasikan terhadap `10`. Dua pasang kurung berurutan berarti dua aplikasi berurutan: yang pertama menghasilkan fungsi, yang kedua menghasilkan bilangan.

Perhatikan pula bahwa `makeAdder (3)` tidak dapat disederhanakan menjadi sebuah bilangan. Ia **adalah** sebuah nilai, dan nilai itu kebetulan berupa fungsi. Ia dapat diberi nama lewat `let`, disimpan di dalam list, dan diserahkan kepada `map`, persis seperti nilai lainnya.

Sesuatu yang penting terjadi di sini, dan pantas dinyatakan terpisah. Ekspresi `λ x : x + n` menyebut `n`, padahal `n` bukan parameternya melainkan parameter `makeAdder`. Fungsi yang dihasilkan karenanya **membawa serta** nilai `n` yang berlaku pada saat ia dibentuk. `makeAdder (3)` dan `makeAdder (5)` adalah dua fungsi berbeda yang lahir dari satu realisasi yang sama. Notasi ini tidak menyediakan istilah bagi hal tersebut, dan bahasa acuan menyebutnya *closure*. Yang perlu disadari sekarang hanyalah bahwa hal itu memang terjadi, dan bahwa transparansi referensial dari Bab 2 tetap utuh: `n` tidak pernah berubah, ia hanya terikat pada nilai yang berbeda pada dua pembentukan yang berbeda.

## Komposisi

Merangkai dua fungsi, yaitu menerapkan yang satu terhadap hasil yang lain, sering dilakukan sehingga pantas mempunyai operatornya sendiri.

```
DEFINISI DAN SPESIFIKASI
    ∘ : (B → C), (A → B) → (A → C)
    { (f ∘ g) adalah fungsi yang, diberi x, menghasilkan f (g (x)) }
```

```
REALISASI
    (f ∘ g) (x) : f (g (x))
```

```
APLIKASI
    ⇒ (square ∘ abs) (-3)
      9
    ⇒ map (square ∘ abs, [-1, 2, -3])
      [1, 4, 9]
```

`g` bekerja lebih dahulu, `f` kemudian. Urutan penulisannya karenanya **berlawanan** dengan urutan pengerjaannya, dan itu adalah sumber kekeliruan yang wajar bagi siapa pun yang baru memakainya. Bentuk ini diwarisi dari matematika, tempat `f ∘ g` dibaca "f setelah g".

Nilai komposisi terlihat ketika rangkaiannya panjang. Bandingkan:

```
map (λ x : square (abs (x)), L)
map (square ∘ abs, L)
```

Keduanya benar. Yang kedua menyebutkan kedua fungsi tanpa mengarang nama parameter yang tidak diperlukan, dan ketika rangkaiannya berisi empat atau lima fungsi, perbedaannya menjadi jauh lebih terasa.

## Contoh Terpadu: Pengurutan dengan Perbandingan yang Diserahkan

Bab 7 meminta pembaca menuliskan `sort` dari `insertSorted`. Kedua fungsi itu mengurutkan `List of Integer` menaik, dan hanya itu. Untuk mengurutkan menurun, atau untuk mengurutkan `List of Date`, seluruh teksnya harus ditulis ulang.

Yang berbeda di antara semua versi itu hanyalah satu hal: **cara membandingkan dua elemen**. Jadikan hal itu sebagai parameter.

```
JUDUL   Pengurutan dengan Perbandingan yang Diserahkan
```

```
DEFINISI DAN SPESIFIKASI
    insertBy : (Element, Element → Boolean), Element, List → List
    { insertBy (before, x, L) menyisipkan x ke dalam L pada tempat yang
      menjaga keterurutan menurut before.
      Prasyarat: L sudah terurut menurut before }

    sortBy : (Element, Element → Boolean), List → List
    { sortBy (before, L) adalah list yang berisi seluruh elemen L,
      terurut menurut before.
      before (a, b) harus bernilai true bila a layak mendahului b }
```

```
REALISASI
    insertBy (before, x, L) :
        depend on L
            isEmpty (L)                                           : makeCons (x, makeNil)
            not (isEmpty (L)) and then before (x, head (L))       : makeCons (x, L)
            not (isEmpty (L)) and then not (before (x, head (L))) :
                makeCons (head (L), insertBy (before, x, tail (L)))

    sortBy (before, L) :
        depend on L
            isEmpty (L)       : makeNil
            not (isEmpty (L)) : insertBy (before, head (L), sortBy (before, tail (L)))
```

```
APLIKASI
    ⇒ sortBy (λ (a, b) : a ≤ b, [3, 1, 2])
      [1, 2, 3]
    ⇒ sortBy (λ (a, b) : a ≥ b, [3, 1, 2])
      [3, 2, 1]
    ⇒ sortBy (isBefore, [makeDate (1, 3, 2024), makeDate (31, 12, 2023)])
      [⟨31, 12, 2023⟩, ⟨1, 3, 2024⟩]
```

Perhatikan aplikasi ketiga. `isBefore` berasal dari Bab 5, ditulis untuk membandingkan dua `Date` dan sama sekali tidak ditulis dengan pengurutan dalam pikiran. Ia diserahkan kepada `sortBy` tanpa penyesuaian satu huruf pun, dan hasilnya adalah pengurutan tanggal.

Inilah yang dimaksud ketika dikatakan bahwa fungsi orde tinggi menaikkan tingkat abstraksi. Satu `sortBy` adalah **setiap** pengurutan yang mungkin atas type apa pun, asalkan pemakainya dapat menyatakan apa artinya "mendahului" bagi type itu.

Perhatikan pula bahwa prasyarat `insertBy` kini menyebut `before`, yaitu sebuah parameter. Sebuah prasyarat dapat berbicara tentang fungsi yang diserahkan kepadanya, dan di sini prasyarat itu memikul beban yang nyata: bila `before` yang diserahkan tidak konsisten, misalnya menyatakan bahwa `a` mendahului `b` sekaligus `b` mendahului `a`, hasilnya tidak dapat dipertanggungjawabkan. Tanggung jawab itu jatuh kepada pemakai, sebagaimana seluruh prasyarat sejak Bab 3.

> **Pola yang sama di luar paradigma fungsional.** Menyerahkan "cara membandingkan" kepada fungsi pengurutan bukan gagasan yang khas fungsional. Pustaka standar C menyediakan `qsort`, yang menerima sebuah *pointer to function* sebagai pembandingnya. Python menuliskan `sorted(xs, key=...)`. Java menyerahkan sebuah `Comparator`.
>
> Ketiganya adalah `sortBy`. Yang berbeda hanyalah seberapa nyaman bahasanya menuliskan fungsi tanpa nama, dan seberapa jauh bahasanya memperlakukan fungsi sebagai nilai secara umum ketimbang sebagai perkecualian yang disediakan untuk keperluan tertentu. Gagasan yang dipelajari di sini karenanya tidak berhenti di bahasa fungsional, sebagaimana sudah dinyatakan pada Bab 1.

## Batas Notasi: Mengapa Haskell Menuliskan Dua Panah

Bab 2 menyisakan sebuah janji. Ketika `max2` diterjemahkan, type-nya dituliskan `Integer -> Integer -> Integer` dan bukan `(Integer, Integer) -> Integer`, dan pembaca diminta membacanya untuk sementara sebagai "menerima dua Integer". Alasannya dijanjikan pada Bab 8.

Inilah alasannya.

**Dalam bahasa acuan, setiap fungsi menerima tepat satu argumen.** Tidak ada perkecualian. Fungsi yang tampak menerima dua argumen sesungguhnya menerima satu argumen dan menghasilkan **sebuah fungsi** yang menunggu argumen berikutnya. Panah `->` berasosiasi ke kanan, sehingga

```haskell
max2 :: Integer -> Integer -> Integer
```

sesungguhnya berarti

```haskell
max2 :: Integer -> (Integer -> Integer)
```

Bandingkan baris kedua itu dengan definisi `makeAdder` pada bab ini. Bentuknya sama persis. `max2` adalah fungsi yang, diberi sebuah bilangan, menghasilkan sebuah fungsi.

Akibatnya langsung dapat dipakai:

```haskell
max2 3           -- sebuah fungsi: "yang lebih besar antara 3 dan argumennya"
max2 3 7         -- 7

map (max2 0) [-3, 5, -1]     -- [0, 5, 0]
```

`max2 0` diserahkan kepada `map` sebagaimana `makeAdder (1)` diserahkan kepada `map` pada bab ini. Menyediakan sebagian argumen dan menyisakan sisanya disebut **aplikasi parsial**, dan dalam bahasa acuan ia tersedia bagi **setiap** fungsi tanpa persiapan apa pun.

**Notasi kita tidak dapat melakukannya.** Bab 2 menyatakan, tentang koma pada `max2 : Integer, Integer → Integer`, bahwa koma menandakan fungsinya mempunyai dua parameter dan bukan sebuah type tersendiri. Kalimat itu tampak sepele ketika ditulis. Akibatnya baru terlihat sekarang: koma mengikat kedua parameter menjadi satu aplikasi yang tidak dapat dibelah. `max2 (3)` tidak berarti apa-apa dalam notasi ini. Ia bukan sebuah fungsi, bukan sebuah nilai, melainkan sebuah aplikasi yang kekurangan argumen.

Yang dapat dituliskan notasi ini hanyalah menyediakan lambda secara manual:

```
map (λ x : max2 (0, x), L)
```

dan itu memang selalu mungkin. Tetapi ia adalah pekerjaan yang dalam bahasa acuan tidak perlu dilakukan sama sekali.

**Inilah pertama kalinya notasi kalah tanpa imbalan.** Setiap perbedaan sejak Bab 2 selalu berupa pertukaran. Notasi lebih ketat pada syarat disjoint, bahasa memeriksa kelengkapan pola. Notasi dapat menuliskan `Integer [1..4]`, bahasa tidak. Notasi membedakan `and` dari `and then`, bahasa tidak. Kali ini tidak ada yang diterima sebagai gantinya. Koma membuat baris definisi lebih mudah dibaca oleh manusia yang belum terbiasa, dan harganya adalah sebuah kemampuan yang hilang seluruhnya.

Notasi ini adalah alat berpikir, dan Bab 2 sudah menyatakan bahwa ia tidak mencakup seluruh konsep pemrograman fungsional. Di sinilah pernyataan itu menjadi konkret. Sebuah alat berpikir yang tidak mengakui batasnya sendiri berhenti menjadi alat berpikir yang jujur, dan mulai dari sini yang terbaik adalah membaca dan menulis langsung dalam bahasanya. Bab 9 dan Suplemen mengambil alih, dan Suplemen S5 mengumpulkan seluruh batas semacam ini dalam satu daftar.

Latihan Merancang pada akhir bab ini menanyakan apa yang harus dikorbankan seandainya notasi ini hendak diperbaiki agar aplikasi parsial menjadi mungkin.

## Kapan Fungsi Orde Tinggi Tidak Dipakai

Tiga pola pada bab ini kuat, dan justru karena itu ada godaan untuk memaksakan setiap fungsi ke dalam salah satunya. Tahanlah godaan itu.

**Tidak setiap rekursi berbentuk `map`, `filter`, atau `fold`.** Perhatikan `insertSorted` dari Bab 7. Rekurensnya berhenti di tengah list begitu tempat penyisipan ditemukan, dan sisanya dipakai kembali utuh tanpa disentuh. Tidak ada satu pun dari ketiga pola yang berbentuk demikian. Memaksakannya menghasilkan teks yang lebih panjang, lebih lambat, dan lebih sulit diyakini kebenarannya.

Hal yang sama berlaku bagi `gcd` pada Bab 6, yang sama sekali tidak bekerja atas list, dan bagi `insertBy` pada bab ini, yang justru **ditulis rekursif** meskipun bab ini seluruhnya tentang menghindari penulisan rekursi berulang.

**Nama yang jelas mengalahkan pola yang benar.** Sebuah `fold` yang lambdanya sepanjang empat baris hampir selalu lebih sulit dibaca daripada fungsi rekursif bernama yang mempunyai definisi dan spesifikasinya sendiri. Pola dipakai ketika ia membuat maksudnya lebih terlihat, bukan ketika ia sekadar dapat dipakai.

Aturan praktisnya: bila Anda dapat menyebutkan dalam satu kalimat pendek apa yang dikerjakan terhadap setiap elemen, pakailah pola. Bila kalimat itu menjadi panjang, atau bila kalimat itu mengandung kata "kecuali", tulislah fungsi rekursif bernama.

## Kesalahan yang Sering Terjadi

| Kesalahan | Gejala | Cara memeriksa |
|---|---|---|
| Menyerahkan aplikasi, bukan fungsi | tidak berarti apa-apa | apakah tertulis `square` atau `square (x)`? argumen fungsi orde tinggi tidak berkurung |
| Nilai awal `fold` keliru | benar untuk list tidak kosong, salah untuk list kosong | apakah `z` netral terhadap operatornya? ujilah dengan `[]` |
| `map` dipakai padahal panjangnya berubah | hasilnya selalu sepanjang masukan, padahal seharusnya menyaring | apakah setiap elemen menghasilkan tepat satu elemen? bila tidak, itu `filter` |
| `filter` dipakai padahal elemennya berubah | elemennya tidak berubah, padahal seharusnya berubah | `filter` hanya memilih, tidak pernah mengubah |
| Urutan komposisi terbalik | hasilnya salah pada fungsi yang tidak komutatif | pada `f ∘ g`, `g` bekerja lebih dahulu |
| Lambda dibuat rekursif | tidak ada nama untuk disebut | fungsi rekursif harus bernama |
| Pola dipaksakan | teksnya lebih panjang daripada versi rekursifnya | tulislah keduanya dan bandingkan; yang lebih pendek biasanya yang lebih benar |

## Ringkasan Padanan

```haskell
map' :: (a -> b) -> [a] -> [b]
-- map' f l menerapkan f pada setiap elemen l
map' _ []       = []
map' f (x : xs) = f x : map' f xs

filter' :: (a -> Bool) -> [a] -> [a]
-- filter' p l berisi seluruh elemen l yang memenuhi p
filter' _ [] = []
filter' p (x : xs)
  | p x       = x : filter' p xs
  | otherwise = filter' p xs

fold' :: (a -> b -> b) -> b -> [a] -> b
-- fold' g z l menggabungkan elemen l dengan g, mulai dari kanan
fold' _ z []       = z
fold' g z (x : xs) = g x (fold' g z xs)
```

Empat hal yang berbeda.

**Ketiganya sudah tersedia.** Bahasa acuan menyediakan `map`, `filter`, dan `foldr` dalam pustaka standarnya, dan itulah sebabnya versi di atas diberi tanda kutip. Sikapnya sama dengan sikap terhadap `gcd` pada Bab 6 dan `length` pada Bab 7: keduanya tetap ditulis sendiri, karena menuliskannya adalah latihannya.

**Type-nya lebih longgar daripada notasi kita.** Perhatikan `map' :: (a -> b) -> [a] -> [b]`. Type masukan dan keluaran boleh **berbeda**, sehingga `map` dapat mengubah list bilangan menjadi list boolean. Notasi kita menuliskan `(Element → Element)` dan karenanya mengharuskan keduanya sama. Ini adalah pembatasan notasi kita, bukan pembatasan gagasannya, dan ia berasal dari keputusan Bab 7 untuk memakai satu nama `Element` saja.

**Lambda hampir sama.** `\x -> x * x` berbanding `λ x : x * x`. Inilah salah satu di antara sedikit tempat sepanjang diktat ini yang notasi dan bahasanya nyaris berimpit.

**Tersedia jalan pintas yang tidak dimiliki notasi.** Karena setiap fungsi menerima satu argumen, ekspresi seperti `(+1)`, `(*2)`, dan `(> 0)` adalah fungsi yang sah tanpa lambda sama sekali, sehingga `map (+1) [1,2,3]` menghasilkan `[2,3,4]`. Bentuk ini adalah aplikasi parsial yang dibicarakan di atas, dalam bentuknya yang paling ringkas.

| Notasi fungsional | Haskell |
|---|---|
| `λ x : e` | `\x -> e` |
| `λ (x, y) : e` | `\x y -> e` |
| `(A → B)` sebagai domain | `(a -> b)` |
| `map (f, L)` | `map f l` |
| `filter (p, L)` | `filter p l` |
| `fold (g, z, L)` | `foldr g z l` |
| `f ∘ g` | `f . g` |
| `sortBy (before, L)` | `sortBy` pada `Data.List`, dengan hasil `Ordering` dan bukan `Boolean` |
| `makeAdder (n) : λ x : x + n` | `makeAdder n = \x -> x + n`, atau `makeAdder n x = x + n` |
| `max2 (3)` | *tidak ada padanan; lihat §Batas Notasi* |

Baris terakhir adalah satu-satunya baris pada seluruh tabel padanan sepanjang diktat ini yang kosong ke arah ini. Setiap tabel sebelumnya mempunyai baris yang kosong ke arah sebaliknya, yaitu hal yang dapat dinyatakan notasi tetapi tidak dapat dinyatakan bahasanya. Perhatikan pula baris kedua dari bawah: apa yang dalam notasi menuntut lambda, dalam bahasanya cukup ditulis sebagai fungsi dengan dua parameter, dan keduanya adalah hal yang persis sama.

## Latihan

**Membaca.** Untuk setiap ekspresi berikut, tuliskan dalam bahasa Indonesia apa yang dihasilkannya, lalu tuliskan ulang sebagai fungsi rekursif bernama lengkap dengan definisi dan spesifikasinya.

```
map (λ x : x mod 2 = 0, L)

fold (λ (a, b) : max2 (a, b), 0, L)

filter (λ x : isMember (x, S), L)

length (filter (λ x : x > 0, L))

(map (λ x : x * 2)) ∘ (filter (λ x : x > 0))
```

Untuk ekspresi kedua, jawablah juga: untuk masukan seperti apa hasilnya keliru, dan apa hubungannya dengan pilihan basis `maxList` pada Bab 7?

Untuk ekspresi kelima, sebutkan fungsi mana yang bekerja lebih dahulu, dan tuliskan hasilnya untuk `[-1, 2, -3, 4]`.

**Memperbaiki.** Fungsi berikut seharusnya menghasilkan hasil kali seluruh elemen positif sebuah list.

```
REALISASI
    productOfPositive (L) :
        fold (λ (a, b) : a * b, 0, filter (λ x : x > 0, L))
```

```
APLIKASI
    ⇒ productOfPositive ([2, 3])
      0
    ⇒ productOfPositive ([1, -2, 5])
      0
```

1. Kedua contoh aplikasi menghasilkan jawaban yang keliru, dan keduanya keliru dengan cara yang sama. Apa yang seharusnya dihasilkan?
2. Kesalahannya terletak pada satu argumen. Sebutkan yang mana, dan jelaskan mengapa akibatnya sama untuk setiap masukan.
3. Aturan mana dari Bab 7 yang dilanggar, dan mengapa aturan itu lebih mudah dilupakan dalam bentuk `fold` daripada dalam bentuk rekursif?
4. Perbaiki, lalu sebutkan berapa hasilnya untuk `[]` dan untuk `[-1, -2]`. Apakah kedua jawaban itu masuk akal menurut spesifikasinya?

**Menulis.** Untuk setiap persoalan, tuliskan realisasinya dengan fungsi orde tinggi, **dan** tuliskan versi rekursif bernamanya. Bandingkan keduanya dan sebutkan mana yang Anda pilih beserta alasannya.

1. `countEven (L)` dari Bab 7, dengan memakai `filter` dan `length`.
2. `any (p, L)`, yang bernilai true bila sekurang-kurangnya satu elemen memenuhi `p`, dan `all (p, L)`, yang bernilai true bila seluruhnya memenuhi. Tuliskan keduanya dengan `fold`, dan perhatikan nilai `z` masing-masing.
3. `makeMultiplier (n)`, yang menghasilkan fungsi pengali. Kemudian tuliskan `map (makeMultiplier (3), [1, 2, 3])`.
4. `compose3 (f, g, h)`, yang menghasilkan komposisi ketiganya. Berapa panjang realisasinya bila `∘` sudah tersedia?
5. `map` dituliskan dengan `fold`. Petunjuk: apa yang harus menjadi `z`, dan apa yang harus dikerjakan `g` terhadap `head (L)`?

**Menelusuri.**

1. Telusuri `fold (λ (a, b) : a + b, 0, [1, 2, 3])` secara lengkap. Pada langkah keberapa `0` benar-benar dipakai?
2. Telusuri `(square ∘ abs) (-3)` dan `(abs ∘ square) (-3)`. Jelaskan mengapa hasilnya sama, dan sebutkan sebuah masukan yang membuat keduanya berbeda.
3. Telusuri `sortBy (λ (a, b) : a ≤ b, [3, 1, 2])` sampai selesai. Hitung berapa kali `insertBy` diaplikasikan dan berapa kali lambdanya dievaluasi.
4. Telusuri `map (makeAdder (2), [1, 2])`. Pada langkah mana `makeAdder (2)` menghasilkan sebuah fungsi, dan berapa kali fungsi itu dibentuk?

**Merancang.** Tiga pertanyaan, ketiganya tanpa jawaban tunggal yang benar.

1. Pada Latihan Merancang Bab 7 Anda diminta mengusulkan sebuah mekanisme agar sepasang fungsi kembar dapat dituliskan sekali saja. Bacalah kembali jawaban Anda dan bandingkan dengan `fold`. Bagian mana yang sudah Anda temukan sendiri, bagian mana yang tidak, dan apa yang menghalangi Anda menemukannya saat itu?
2. `insertSorted` menolak dituliskan sebagai `map`, `filter`, maupun `fold`. Jelaskan sifat apa dari rekurensnya yang menyebabkan hal itu. Kemudian carilah satu fungsi lain dari Bab 6 atau Bab 7 yang menolak dengan alasan yang sama, dan satu yang menolak dengan alasan yang berbeda.
3. Notasi ini tidak dapat menyatakan aplikasi parsial, karena koma pada `f : A, B → C` mengikat kedua parameter menjadi satu. Usulkan perubahan notasi yang membuat aplikasi parsial menjadi mungkin. Kemudian, dan inilah bagian yang sesungguhnya dinilai: telusurilah Bab 2 sampai Bab 7 dan sebutkan apa saja yang menjadi lebih sulit dibaca akibat usulan Anda. Setelah menimbang keduanya, apakah Anda tetap mengusulkannya untuk pembaca yang baru pertama kali belajar?
