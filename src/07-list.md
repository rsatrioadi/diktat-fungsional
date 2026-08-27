# List

## Koleksi Objek

Sampai bab ini, sebuah program hanya mampu mengelola type dasar dan type bentukan yang disusun darinya. Untuk sebagian persoalan, pembatasan itu bukan sekadar menyulitkan, melainkan membuat programnya tidak dapat ditulis sama sekali.

Sebuah `Date` menyimpan satu tanggal. Bila kita perlu mengelola tiga tanggal, kita dapat menuliskan tiga parameter. Bila kita perlu mengelola seluruh tanggal dalam setahun, cara itu berhenti bekerja. Bahkan bila banyaknya tanggal **tidak diketahui pada saat program ditulis**, cara itu tidak pernah dapat bekerja, karena kita harus menuliskan nama sebanyak nilai yang akan diproses, sedangkan jumlahnya baru diketahui kelak.

Yang dibutuhkan adalah sebuah **koleksi**: satu nama yang mewakili sekumpulan nilai, beserta cara mencapai setiap anggotanya. Sebuah koleksi mungkin kosong, mungkin berisi satu anggota, mungkin berisi sejuta.

Ada banyak cara mengorganisasi koleksi, dan masing-masing cara menentukan sebuah type kolektif tersendiri dengan operasi dasarnya sendiri. Bab ini membahas satu di antaranya, yaitu **list**, yang anggotanya berurutan dan dicapai dari satu ujung. List adalah struktur rekursif yang paling sederhana, sehingga seluruh Bab 6 dapat langsung dipakai kembali di sini.

## List sebagai Type Rekursif

Bab 6 menutup dengan menyatakan bahwa type rekursif yang paling penting dalam informatika adalah list. Definisinya:

- **Basis:** *list kosong* adalah sebuah list.
- **Rekurens:** *sebuah elemen yang diikuti oleh sebuah list* adalah sebuah list.

Dua baris itu menghasilkan seluruh isi bab ini.

```
TYPE LIST
```

```
DEFINISI TYPE
    type List : Nil
              | Cons ⟨head : Element, tail : List⟩
    { Sebuah List adalah list kosong, atau sebuah elemen yang diikuti
      oleh sebuah List }

DEFINISI DAN SPESIFIKASI SELEKTOR
    head : Cons → Element
    { head (L) memberikan elemen pertama L }

    tail : Cons → List
    { tail (L) memberikan L tanpa elemen pertamanya }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makeNil : → List
    { makeNil membentuk sebuah list kosong }

    makeCons : Element, List → List
    { makeCons (e, L) membentuk sebuah list dengan e sebagai elemen
      pertama dan L sebagai sisanya }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isEmpty : List → Boolean
    { isEmpty (L) bernilai true bila L adalah list kosong }

    isOneElement : List → Boolean
    { isOneElement (L) bernilai true bila L hanya mempunyai satu elemen }
```

`Element` bukan sebuah type dasar, melainkan "tempat kosong" yang diisi sekali untuk setiap list: seluruh elemen sebuah list bertype sama, dan type itu ditetapkan pada saat list dipakai. Diktat ini menuliskan `List of Integer`, `List of Character`, dan `List of Date` untuk menyebut list yang `Element`-nya masing-masing `Integer`, `Character`, dan `Date`. Cara menyatakan hal ini secara tepat, sehingga kompilator ikut memeriksanya, adalah bagian dari sistem type yang berada di luar cakupan diktat ini.

> **Catatan.** Nama `Cons` adalah kependekan dari *construct*, dan berasal dari Lisp, bahasa fungsional tertua yang masih dipakai hingga kini. Di sana `cons` adalah operasi dasar yang membentuk sebuah list dari sebuah elemen dan sebuah list, dan setiap sel penyusunnya disebut *cons cell*. Bahasa acuan diktat ini menuliskannya sebagai operator `:`, tetapi tetap menyebut selnya dengan nama yang sama, sehingga kata *cons* masih akan ditemui pembaca pada dokumentasi bahasa fungsional masa kini.

### Penulisan singkat

Menuliskan setiap list dengan konstruktornya cepat menjadi melelahkan. Karena itu tersedia penulisan singkat:

```
[]            { setara dengan makeNil }
[7]           { setara dengan makeCons (7, makeNil) }
[1, 2, 3]     { setara dengan makeCons (1, makeCons (2, makeCons (3, makeNil))) }
```

Penulisan singkat adalah **penulisan yang sama**, bukan type yang lain. Pada penelusuran, bentuk panjangnya dipakai kembali setiap kali sebuah konstruktor benar-benar dibongkar atau dibentuk, karena di situlah struktur rekursifnya terlihat.

`[1, 2, 3]` tidak simetris. Elemen `1` berada di lapis paling luar dan dapat diambil dengan satu aplikasi `head`, sedangkan `3` terkubur di lapis paling dalam. Ketidaksimetrisan ini bukan kekurangan yang harus ditutupi, melainkan sifat list yang akan menjelaskan mengapa sebagian operasi murah dan sebagian lain mahal.

> **Peringatan bagi pembaca bahan lama.** Diktat terdahulu di lingkungan ITB memakai nama `Head` untuk **list tanpa elemen terakhir**, dan `FirstElmt` untuk elemen pertama. Diktat ini memakai `head` untuk elemen pertama dan `tail` untuk sisanya, mengikuti terminologi yang kini sangat umum digunakan di bahasa pemrograman fungsional. Arti nama `head` karenanya **berkebalikan** dengan bahan lama. Bila Anda membaca materi arsip atau berdiskusi dengan seseorang yang mempelajarinya dari sana, sepakati lebih dahulu arti kedua nama itu.

### Basis-0 dan basis-1

Pada fungsi rekursif yang hanya melibatkan type dasar (misalnya `Integer`), tidak ada yang membatasi di mana basis boleh diletakkan, dan Bab 6 memperlihatkan basis pada `n = 0`, `n = 1`, `n < 10`, `b = 0`, serta sepasang `n = 0` dan `n = 1`. Memberi nomor pada basis semacam itu tidak menerangkan apa-apa.

Tidak demikian dengan type koleksi seperti `List`. Type-nya mempunyai tepat dua konstruktor, sehingga kasus terkecil yang mungkin hanya ada dua, dan keduanya pantas diberi nama:

| Istilah | Basis pada | Predikat | Dipakai bila |
|---|---|---|---|
| **Basis-0** | list kosong | `isEmpty` | fungsi terdefinisi atas list kosong |
| **Basis-1** | list satu elemen | `isOneElement` | fungsi **tidak** terdefinisi atas list kosong |

Pemilihan di antara keduanya ditentukan oleh satu pertanyaan: *apakah fungsi ini mempunyai jawaban untuk list kosong?* Banyaknya elemen sebuah list kosong adalah nol, sehingga `length` berbasis-0. Nilai terbesar dalam list kosong tidak ada, sehingga `maxList` berbasis-1 dan list kosong dikeluarkan dari domainnya lewat pembatasan, persis sebagaimana `quadrant` mengeluarkan titik pada sumbu (Bab 4).

Dua kewajiban yang pada Bab 6 harus dijaga sendiri oleh penulisnya kini datang cuma-cuma. Untuk list, keduanya diberikan oleh type-nya: alternatif `Nil` tidak menyebut dirinya sendiri sehingga ia adalah basisnya, alternatif `Cons` menyebut dirinya sendiri sehingga ia adalah rekurensnya, dan `tail (L)` selalu berisi satu elemen lebih sedikit daripada `L` sehingga konvergensinya terjamin. Yang pada Bab 6 harus dibuktikan, di sini tinggal disebutkan.

Satu kebiasaan yang berlaku di seluruh bab ini: kondisi rekurens **selalu ditulis utuh** sebagai `not (isEmpty (L))`, tidak pernah sebagai `else`. Alasannya sama dengan alasan Bab 5 menuliskan `isRectangle (s)` secara terbuka. Menuliskannya membuat jaminan yang diberikan type terlihat alih-alih tersembunyi.

## Contoh 1: Banyaknya Elemen

```
JUDUL   Banyaknya Elemen Sebuah List
```

```
DEFINISI DAN SPESIFIKASI
    length : List → Integer ≥ 0
    { length (L) adalah banyaknya elemen L, nol bila L kosong }
    { Basis-0:  isEmpty (L)       → 0
      Rekurens: not (isEmpty (L)) → 1 + length (tail (L)) }
```

```
REALISASI
    length (L) :
        depend on L
            isEmpty (L)       : 0
            not (isEmpty (L)) : 1 + length (tail (L))
```

```
APLIKASI
    ⇒ length ([])
      0
    ⇒ length ([7])
      1
    ⇒ length ([1, 2, 3])
      3
```

Penelusuran `length ([1, 2, 3])`:

```
⇒ length (makeCons (1, makeCons (2, makeCons (3, makeNil))))
  → 1 + length (makeCons (2, makeCons (3, makeNil)))    { ekspansi, rekurens }
  → 1 + (1 + length (makeCons (3, makeNil)))            { ekspansi, rekurens }
  → 1 + (1 + (1 + length (makeNil)))                    { ekspansi, rekurens }
  → 1 + (1 + (1 + 0))                                   { ekspansi, basis-0 }
  → 1 + (1 + 1)                                         { reduksi + }
  → 1 + 2                                               { reduksi + }
  → 3                                                   { reduksi + }
```

Bentuknya persis bentuk `factorial (4)` pada Bab 6: ekspresi membesar satu lapis pada setiap ekspansi sampai basis tercapai, lalu mengecil satu reduksi pada satu waktu. Yang berbeda hanyalah apa yang mengecil: pada `factorial` sebuah bilangan, di sini sebuah struktur.

Penelusuran ditulis dengan konstruktor dan bukan dengan `[1, 2, 3]`, karena pembongkaran konstruktor itulah yang sedang diperlihatkan.

## Contoh 2: Keanggotaan

```
JUDUL   Keanggotaan Sebuah Elemen
```

```
DEFINISI DAN SPESIFIKASI
    isMember : Element, List → Boolean
    { isMember (x, L) bernilai true bila x adalah salah satu elemen L.
      Bernilai false bila L kosong }
    { Basis-0:  isEmpty (L)       → false
      Rekurens: not (isEmpty (L)) → head (L) = x, atau x anggota tail (L) }
```

```
REALISASI
    isMember (x, L) :
        depend on L
            isEmpty (L)       : false
            not (isEmpty (L)) : (head (L) = x) or else isMember (x, tail (L))
```

```
APLIKASI
    ⇒ isMember (5, [])
      false
    ⇒ isMember (2, [1, 2, 3])
      true
    ⇒ isMember (9, [1, 2, 3])
      false
```

Realisasi ini mempunyai dua sifat yang belum muncul sebelumnya.

**`or else` dipakai, dan memang diperlukan.** Bab 4 menganjurkan memakai hubung-singkat hanya bila operan kedua tidak selalu terdefinisi. Di sini alasannya berbeda dan sama sahnya: operan kedua **selalu terdefinisi, tetapi mahal**. Dengan `or else`, `isMember (1, [1, 2, ..., 1000])` berhenti pada elemen pertama. Dengan `or` biasa, seluruh seribu elemen tetap ditelusuri untuk menghasilkan jawaban yang sama.

**Hasilnya dapat dipastikan sebelum basis tercapai.** Ini adalah fungsi pertama pada bab ini yang tidak selalu berjalan sampai ke ujung list. Sebagian besar fungsi list tidak mempunyai sifat ini.

## Contoh 3: Sepasang Fungsi yang Hampir Kembar

```
JUDUL   Jumlah dan Hasil Kali Seluruh Elemen
```

```
DEFINISI DAN SPESIFIKASI
    sumList : List of Integer → Integer
    { sumList (L) adalah jumlah seluruh elemen L, nol bila L kosong }
    { Basis-0:  isEmpty (L)       → 0
      Rekurens: not (isEmpty (L)) → head (L) + sumList (tail (L)) }

    productList : List of Integer → Integer
    { productList (L) adalah hasil kali seluruh elemen L, satu bila L kosong }
    { Basis-0:  isEmpty (L)       → 1
      Rekurens: not (isEmpty (L)) → head (L) * productList (tail (L)) }
```

```
REALISASI
    sumList (L) :
        depend on L
            isEmpty (L)       : 0
            not (isEmpty (L)) : head (L) + sumList (tail (L))

    productList (L) :
        depend on L
            isEmpty (L)       : 1
            not (isEmpty (L)) : head (L) * productList (tail (L))
```

```
APLIKASI
    ⇒ sumList ([])
      0
    ⇒ sumList ([1, 2, 3])
      6
    ⇒ productList ([])
      1
    ⇒ productList ([1, 2, 3])
      6
```

Letakkan kedua realisasi berdampingan. Keduanya berbeda **hanya pada dua tempat**: nilai basisnya, dan operatornya.

Mengapa basis `productList` bernilai `1` dan bukan `0`? Karena basis harus merupakan nilai yang tidak mengubah apa-apa ketika operator diterapkan padanya. Menjumlahkan nol tidak mengubah jumlah, dan mengalikan satu tidak mengubah hasil kali. Bila basis `productList` diisi `0`, setiap hasil kali menjadi nol. Aturan ini berlaku umum: **nilai basis ditentukan oleh operator rekurensnya**.

Kekembaran kedua fungsi ini disengaja diperlihatkan, dan akan dibicarakan lagi pada akhir bab.

## Contoh 4: Nilai Terbesar, dan Basis-1

```
JUDUL   Elemen Terbesar Sebuah List
```

```
DEFINISI DAN SPESIFIKASI
    maxList : List of Integer tidak kosong → Integer
    { maxList (L) adalah nilai elemen terbesar L.
      Prasyarat: L tidak kosong }
    { Basis-1:  isOneElement (L)       → head (L)
      Rekurens: not (isOneElement (L)) → max2 (head (L), maxList (tail (L))) }
    { Fungsi antara yang dipakai: max2 }
```

```
REALISASI
    maxList (L) :
        depend on L
            isOneElement (L)       : head (L)
            not (isOneElement (L)) : max2 (head (L), maxList (tail (L)))
```

```
APLIKASI
    ⇒ maxList ([7])
      7
    ⇒ maxList ([3, 9, 2])
      9
    ⇒ maxList ([-5, -1, -8])
      -1
```

**Mengapa basis-1.** Nilai terbesar dalam list kosong tidak ada, dan tidak ada bilangan yang dapat dituliskan sebagai jawaban. Menuliskan basis-0 dengan hasil `0` akan membuat `maxList ([-5, -1, -8])` menghasilkan `0`, yaitu sebuah nilai yang bahkan bukan anggota list-nya. Kekeliruan semacam itu tampak wajar dan bertahan lama, persis seperti yang diperingatkan Bab 4 tentang `else`.

Yang benar adalah mengeluarkan list kosong dari domain. Pembatasan itu muncul di **dua tempat**, yaitu pada baris definisi dan pada prasyarat, sebagaimana dianjurkan sejak Bab 4.

**Konvergensinya tetap terjamin, tetapi argumennya berubah.** Rekurens mengaplikasikan `maxList` terhadap `tail (L)`, dan `tail (L)` harus tetap memenuhi prasyarat. Karena kondisi rekurens adalah `not (isOneElement (L))`, list-nya berisi sekurang-kurangnya dua elemen, sehingga `tail (L)` berisi sekurang-kurangnya satu. Prasyarat terpenuhi pada setiap langkah. Fungsi berbasis-1 selalu menuntut pemeriksaan ini.

`max2` diambil kembali dari Bab 2 tanpa ditulis ulang.

## Contoh 5: Menghasilkan Sebuah List

Seluruh fungsi sejauh ini menghasilkan nilai tunggal. Sebuah fungsi list dapat pula menghasilkan list.

```
JUDUL   Mengkuadratkan Seluruh Elemen
```

```
DEFINISI DAN SPESIFIKASI
    squareAll : List of Integer → List of Integer
    { squareAll (L) adalah list yang elemennya adalah kuadrat setiap
      elemen L, dengan urutan yang sama. Banyaknya elemen tidak berubah }
    { Basis-0:  isEmpty (L)       → []
      Rekurens: not (isEmpty (L)) → square (head (L)) diikuti squareAll (tail (L)) }
```

```
REALISASI
    squareAll (L) :
        depend on L
            isEmpty (L)       : makeNil
            not (isEmpty (L)) : makeCons (square (head (L)), squareAll (tail (L)))
```

```
APLIKASI
    ⇒ squareAll ([])
      []
    ⇒ squareAll ([1, 2, 3])
      [1, 4, 9]
```

Sekarang bandingkan dengan persoalan yang terdengar serupa.

```
JUDUL   Menyaring Elemen Positif
```

```
DEFINISI DAN SPESIFIKASI
    keepPositive : List of Integer → List of Integer
    { keepPositive (L) adalah list yang berisi seluruh elemen L yang
      bernilai positif, dengan urutan yang sama. Banyaknya elemen dapat
      berkurang }
    { Basis-0:  isEmpty (L) → []
      Rekurens: elemen pertama disertakan atau dilewati }
```

```
REALISASI
    keepPositive (L) :
        depend on L
            isEmpty (L)                               : makeNil
            not (isEmpty (L)) and then (head (L) > 0) : makeCons (head (L), keepPositive (tail (L)))
            not (isEmpty (L)) and then (head (L) ≤ 0) : keepPositive (tail (L))
```

```
APLIKASI
    ⇒ keepPositive ([])
      []
    ⇒ keepPositive ([1, -2, 3])
      [1, 3]
    ⇒ keepPositive ([-1, -2])
      []
```

Realisasi ini menuntut tiga pemeriksaan.

**`and then` wajib di sini.** Ekspresi `head (L)` tidak terdefinisi bila `L` kosong. Menuliskan `not (isEmpty (L)) and (head (L) > 0)` dengan `and` biasa adalah kesalahan yang persis sama dengan `isDivisor` pada Bab 4. Keperluan ini muncul dengan sendirinya begitu kondisi memeriksa isi list (bukan hanya bentuknya), sehingga bentuk ini akan sering muncul mulai sekarang.

**Rekurensnya mempunyai dua kasus, bukan satu.** `squareAll` selalu membentuk `makeCons`; `keepPositive` kadang membentuknya dan kadang tidak. Perbedaan antara "mengubah setiap elemen" dan "memilih sebagian elemen" terlihat langsung pada bentuk teksnya.

**Ketiga kondisinya lengkap dan disjoint.** Diperiksa sebagaimana Bab 4.

## Menyusun List: Harga Sebuah Ujung

`makeCons` menambahkan elemen di **depan** dan menghasilkan list baru dengan satu aplikasi konstruktor. Menambahkan di **belakang** tidak tersedia sebagai konstruktor, dan harus diturunkan.

```
JUDUL   Menambahkan Elemen di Akhir List
```

```
DEFINISI DAN SPESIFIKASI
    addLast : List, Element → List
    { addLast (L, e) adalah list yang berisi seluruh elemen L dengan
      urutan yang sama, diikuti oleh e sebagai elemen terakhir }
    { Basis-0:  isEmpty (L)       → [e]
      Rekurens: not (isEmpty (L)) → head (L) diikuti addLast (tail (L), e) }
```

```
REALISASI
    addLast (L, e) :
        depend on L
            isEmpty (L)       : makeCons (e, makeNil)
            not (isEmpty (L)) : makeCons (head (L), addLast (tail (L), e))
```

```
APLIKASI
    ⇒ addLast ([], 9)
      [9]
    ⇒ addLast ([1, 2], 9)
      [1, 2, 9]
```

Hitunglah pekerjaannya. `makeCons (0, L)` menyisipkan di depan dengan **satu** aplikasi konstruktor, berapa pun panjang `L`. `addLast (L, e)` untuk `L` sepanjang *n* menuntut *n* + 1 aplikasi `addLast` dan membangun ulang seluruh *n* lapis konstruktor.

Ketidaksimetrisan yang disebut di awal bab bermuara di sini. Sebuah list mempunyai satu ujung yang murah dan satu ujung yang mahal, sebagai akibat langsung dari definisi type-nya, yang menyebut dirinya sendiri pada posisi `tail`.

Diktat ini karenanya hanya mempunyai **satu** konstruktor. Bahan terdahulu menyediakan konstruktor kedua untuk menambahkan di kanan, sehingga kedua ujung tampak setara. Kesetaraan itu menyesatkan. Dengan menurunkan `addLast` sebagai fungsi biasa, harganya menjadi terlihat, dan pilihan rancangan yang bersandar padanya menjadi pilihan yang sadar.

### Pembalikan, dan tempat akumulator bekerja

```
JUDUL   Membalik Urutan Elemen
```

```
DEFINISI DAN SPESIFIKASI
    reverse : List → List
    { reverse (L) adalah list yang berisi seluruh elemen L dengan
      urutan terbalik }
```

```
REALISASI (VERSI PERTAMA)
    reverse (L) :
        depend on L
            isEmpty (L)       : makeNil
            not (isEmpty (L)) : addLast (reverse (tail (L)), head (L))
```

Realisasi ini benar dan langsung mengikuti gagasannya, tetapi mahal: setiap lapis memanggil `addLast` yang sendirinya menelusuri seluruh list. Untuk `[1, 2, 3]`, `reverse` diaplikasikan empat kali dan `addLast` enam kali.

Bab 6 memperkenalkan akumulator, yaitu parameter tambahan yang membawa hasil sementara maju. Persoalan ini adalah tempatnya bekerja:

```
REALISASI (VERSI KEDUA)
    reverseOnto : List, List → List
    { reverseOnto (L, acc) adalah elemen-elemen L dengan urutan terbalik,
      diikuti oleh seluruh elemen acc dengan urutannya semula }
    { Basis-0:  isEmpty (L)       → acc
      Rekurens: not (isEmpty (L)) → reverseOnto (tail (L), head (L) diikuti acc) }

    reverseOnto (L, acc) :
        depend on L
            isEmpty (L)       : acc
            not (isEmpty (L)) : reverseOnto (tail (L), makeCons (head (L), acc))

    reverse2 (L) : reverseOnto (L, makeNil)
```

```
APLIKASI
    ⇒ reverse2 ([])
      []
    ⇒ reverse2 ([1, 2, 3])
      [3, 2, 1]
```

Untuk `[1, 2, 3]`, `reverseOnto` diaplikasikan empat kali dan `addLast` tidak dipakai sama sekali. Setiap langkah hanya memerlukan satu `makeCons`, yaitu operasi yang murah.

Sebagaimana Bab 6, harganya terletak pada spesifikasinya. Spesifikasi `reverse` dapat dituliskan dalam satu kalimat. Spesifikasi `reverseOnto` menuntut kita menjelaskan apa arti `acc` pada setiap langkah, dan kalimat itu jauh lebih sulit ditulis dengan tepat daripada realisasinya.

**Keuntungan ini tidak berlaku umum.** Dari seluruh fungsi pada bab ini, hanya `reverse` yang jelas diuntungkan oleh akumulator. Menuliskan `length`, `sumList`, atau `squareAll` dalam bentuk berakumulator menghasilkan teks yang lebih panjang tanpa penghematan apa pun, dan untuk `squareAll` hasilnya bahkan terbalik urutannya sehingga menuntut satu `reverse` tambahan di ujung. Bentuk berakumulator adalah salah satu alat, bukan bentuk yang lebih maju.

### Menyisipkan pada tempatnya

```
JUDUL   Penyisipan Terurut
```

```
DEFINISI DAN SPESIFIKASI
    insertSorted : Integer, List of Integer → List of Integer
    { insertSorted (x, L) adalah list yang berisi seluruh elemen L
      beserta x, terurut menaik.
      Prasyarat: L terurut menaik }
```

```
REALISASI
    insertSorted (x, L) :
        depend on L
            isEmpty (L)                               : makeCons (x, makeNil)
            not (isEmpty (L)) and then (x ≤ head (L)) : makeCons (x, L)
            not (isEmpty (L)) and then (x > head (L)) : makeCons (head (L), insertSorted (x, tail (L)))
```

```
APLIKASI
    ⇒ insertSorted (5, [])
      [5]
    ⇒ insertSorted (5, [7, 9])
      [5, 7, 9]
    ⇒ insertSorted (5, [1, 3])
      [1, 3, 5]
    ⇒ insertSorted (5, [1, 7])
      [1, 5, 7]
```

Keterurutan di sini adalah sebuah **invarian**, dalam arti yang persis sama dengan invarian `Fraction` pada Bab 5. Invarian ini dituliskan sebagai prasyarat, tidak dijamin oleh apa pun, dan ditegakkan dengan disiplin: setiap operator yang menghasilkan list terurut harus memastikan hasilnya tetap terurut. Kasus kedua bersandar penuh pada invarian itu, karena `makeCons (x, L)` hanya benar bila seluruh isi `L` sudah dipastikan tidak lebih kecil daripada `x`, dan yang menjaminnya bukan pemeriksaan melainkan prasyarat.

## List of Character

Teks adalah list yang elemennya karakter. Tidak ada mekanisme baru yang diperlukan.

```
JUDUL   Banyaknya Kemunculan Sebuah Karakter
```

```
DEFINISI DAN SPESIFIKASI
    countChar : Character, List of Character → Integer ≥ 0
    { countChar (c, L) adalah banyaknya kemunculan c di dalam L }
```

```
REALISASI
    countChar (c, L) :
        depend on L
            isEmpty (L)                               : 0
            not (isEmpty (L)) and then (head (L) = c) : 1 + countChar (c, tail (L))
            not (isEmpty (L)) and then (head (L) ≠ c) : countChar (c, tail (L))
```

```
APLIKASI
    ⇒ countChar ('a', [])
      0
    ⇒ countChar ('a', ['s', 'a', 't', 'r', 'i', 'o'])
      1
```

Bentuknya adalah `keepPositive` dan `length` yang digabung: rekurensnya bercabang seperti penyaringan, hasilnya sebuah bilangan seperti pencacahan.

Yang penting pada contoh ini adalah **apa yang tidak perlu ditulis**: tidak ada type teks tersendiri, tidak ada operasi teks tersendiri, dan `length`, `isMember`, serta `reverse` yang sudah ditulis di atas berlaku atas teks tanpa perubahan satu huruf pun.

## List of Type Bentukan

Elemen sebuah list boleh bertype bentukan. Bab 5 menyediakan `Date` beserta `isBefore`, dan keduanya dipakai kembali di sini tanpa penyesuaian.

```
JUDUL   Tanggal Paling Awal
```

```
DEFINISI DAN SPESIFIKASI
    earliest : List of Date tidak kosong → Date
    { earliest (L) adalah tanggal paling awal di dalam L.
      Prasyarat: L tidak kosong }
    { Basis-1:  isOneElement (L)       → head (L)
      Rekurens: not (isOneElement (L)) → yang lebih awal antara head (L)
                                          dan earliest (tail (L)) }
```

```
REALISASI
    earliest (L) :
        let
            rest : earliest (tail (L))
        in
            depend on L
                isOneElement (L)                                           : head (L)
                not (isOneElement (L)) and isBefore (head (L), rest)       : head (L)
                not (isOneElement (L)) and not (isBefore (head (L), rest)) : rest
```

Letakkan realisasi ini berdampingan dengan `maxList`. Keduanya berbasis-1, keduanya membandingkan elemen pertama terhadap hasil rekurens atas sisanya, dan keduanya hanya berbeda pada **cara membandingkan**. Yang satu memakai `max2` atas `Integer`, yang lain memakai `isBefore` atas `Date`.

Ini adalah kekembaran ketiga pada bab ini, dan satu-satunya yang melintasi type: `Integer` dan `Date` tidak mempunyai kesamaan apa pun, tetapi bentuk kedua fungsinya sama.

## Himpunan

Sebuah **himpunan** adalah list yang seluruh elemennya berbeda, dengan kata lain, sebuah list dengan sebuah tambahan **invarian**.

```
TYPE SET
```

```
DEFINISI TYPE
    type Set : List
    { Sebuah Set adalah sebuah List yang seluruh elemennya berbeda.
      Urutan elemen tidak berarti }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makeEmptySet : → Set
    { makeEmptySet membentuk himpunan kosong }

    insertSet : Element, Set → Set
    { insertSet (x, S) adalah himpunan yang beranggotakan seluruh anggota S
      beserta x. Bila x sudah menjadi anggota S, hasilnya adalah S }

DEFINISI DAN SPESIFIKASI OPERATOR LAIN
    union : Set, Set → Set
    { union (S1, S2) adalah himpunan yang beranggotakan seluruh anggota
      S1 maupun S2 }
```

```
REALISASI
    insertSet (x, S) :
        if isMember (x, S) then
            S
        else
            makeCons (x, S)

    union (S1, S2) :
        depend on S1
            isEmpty (S1)       : S2
            not (isEmpty (S1)) : insertSet (head (S1), union (tail (S1), S2))
```

```
APLIKASI
    ⇒ insertSet (5, [])
      [5]
    ⇒ insertSet (5, [5, 3])
      [5, 3]
    ⇒ union ([1, 2], [2, 3])
      [1, 2, 3]
```

**`makeCons` bukan konstruktor yang sah bagi `Set`.** Ia dapat menghasilkan list yang mengandung dua elemen sama, sehingga melanggar invarian `Set`. Setiap pembentukan anggota baru harus melalui `insertSet`, dan `union` di atas taat kepada aturan itu dengan memanggil `insertSet` alih-alih `makeCons`.

Ini adalah "bekerja lewat konstruktor" dari Bab 5, kini dengan akibat yang lebih tajam: satu pemanggilan `makeCons` yang lolos akan menghasilkan `Set` yang tidak sah, dan gejalanya akan muncul jauh dari sebabnya.

Harganya nyata: `insertSet` memanggil `isMember` yang menelusuri seluruh himpunan, sehingga menyisipkan ke dalam himpunan jauh lebih mahal daripada menyisipkan ke dalam list. Invarian dibayar oleh setiap operasi yang harus menjaganya, dan dibeli karena hal lain menjadi lebih mudah dipercaya.

## Resume Analisis Rekurens

Seluruh fungsi rekursif pada Bab 6 dan Bab 7 mengikuti sejumlah kecil pola. Tabel berikut merangkumnya.

**Rekurens terhadap bilangan bulat `n`:**

| Bagian | Bentuk |
|---|---|
| Basis | `n = 0`, atau kondisi lain yang jawabannya diketahui langsung |
| Rekurens | `f (n - 1)`, `f (n div 10)`, `f (n div 2)`, dan sebagainya |
| Yang wajib dinyatakan | besaran yang mengecil beserta batas bawahnya |

**Rekurens terhadap list `L`, basis-0:**

| Bagian | Bentuk |
|---|---|
| Basis | `isEmpty (L)` : nilai netral terhadap operator rekurensnya |
| Rekurens | `not (isEmpty (L))` : gabungkan `head (L)` dengan `f (tail (L))` |
| Konvergensi | dijamin oleh type |

**Rekurens terhadap list `L`, basis-1:**

| Bagian | Bentuk |
|---|---|
| Basis | `isOneElement (L)` : jawaban bagi satu elemen |
| Rekurens | `not (isOneElement (L))` : gabungkan `head (L)` dengan `f (tail (L))` |
| Yang wajib diperiksa | apakah `tail (L)` masih memenuhi prasyarat |
| Dipakai bila | fungsi tidak terdefinisi atas list kosong |

Tiga bentuk rekurens yang berulang pada bab ini, menurut apa yang dihasilkannya:

| Yang dihasilkan | Rekurensnya | Contoh pada bab ini |
|---|---|---|
| Sebuah nilai tunggal | gabungkan `head (L)` dengan hasil atas `tail (L)` | `length`, `sumList`, `productList`, `maxList`, `earliest`, `countChar` |
| List dengan panjang sama | `makeCons (`*ubah* `head (L), f (tail (L)))` | `squareAll` |
| List yang mungkin lebih pendek | sertakan atau lewati `head (L)` | `keepPositive` |

Sekarang lihatlah kembali ketiga pasangan yang sengaja diletakkan berdampingan: `sumList` dan `productList`, `maxList` dan `earliest`, `squareAll` dan `keepPositive`. Beberapa di antaranya berbeda hanya pada dua tanda. Menuliskan hal yang hampir sama berulang kali adalah pekerjaan yang membosankan sekaligus rawan salah, dan ketika sebuah pola muncul sesering ini, wajar bertanya apakah polanya sendiri dapat dituliskan sekali saja. Pertanyaan itu adalah isi Bab 8.

## Kesalahan yang Sering Terjadi

| Kesalahan | Gejala | Cara memeriksa |
|---|---|---|
| Basis-0 dipakai padahal seharusnya basis-1 | jawaban yang tampak wajar tetapi bukan berasal dari list-nya | adakah jawaban yang benar untuk list kosong? bila tidak ada, keluarkan dari domain |
| Nilai basis salah | benar untuk list panjang, salah untuk list pendek atau kosong | apakah nilai basis netral terhadap operator rekurensnya? |
| `tail (L)` melanggar prasyarat | gagal pada list satu elemen | pada fungsi berbasis-1, apakah kondisi rekurens menjamin sekurangnya dua elemen? |
| `and` dipakai padahal `and then` diperlukan | tidak terdefinisi pada list kosong | adakah kondisi yang memakai `head (L)` atau `tail (L)`? |
| Rekursi pada list yang salah | menelusuri list yang sama berulang kali, tidak berhenti | apakah aplikasi rekursif memakai `tail (L)` dan bukan `L`? |
| `makeCons` dipakai atas `Set` | himpunan mengandung elemen kembar | apakah setiap pembentukan melalui `insertSet`? |
| Hasil terbalik urutannya | seluruh elemen benar, urutannya terbalik | biasanya akibat akumulator; perlukah `reverse` di ujungnya? |

Kesalahan pada baris kelima, yaitu menuliskan `f (L)` alih-alih `f (tail (L))`, adalah salah ketik yang mudah dilakukan, dan akibatnya bukan jawaban yang keliru melainkan tidak ada jawaban sama sekali. Ini adalah pelanggaran kewajiban kedua dari Bab 6, dalam bentuknya yang paling sederhana.

## Ringkasan Padanan

```haskell
-- type List tidak perlu didefinisikan; Haskell sudah menyediakannya
-- []           adalah makeNil
-- (x : xs)     adalah makeCons (x, xs)

length' :: [a] -> Integer
-- length' l adalah banyaknya elemen l
length' []       = 0
length' (_ : xs) = 1 + length' xs

isMember :: Eq a => a -> [a] -> Bool
-- isMember x l bernilai True bila x adalah salah satu elemen l
isMember _ []       = False
isMember x (y : ys) = y == x || isMember x ys

keepPositive :: [Integer] -> [Integer]
-- keepPositive l berisi seluruh elemen l yang bernilai positif
keepPositive []       = []
keepPositive (x : xs)
  | x > 0     = x : keepPositive xs
  | otherwise = keepPositive xs
```

Empat hal yang berbeda.

**Pola `(x : xs)` mengerjakan tiga hal sekaligus.** Pola ini memeriksa bahwa list tidak kosong, mengambil elemen pertamanya, dan mengambil sisanya, dalam satu tulisan. Predikat `isEmpty` beserta selektor `head` dan `tail` tidak lagi perlu disebut. Ini adalah *pattern matching* yang sudah muncul pada Bab 5 untuk `Shape` dan pada Bab 6 untuk bilangan, kini dalam bentuk yang paling sering dipakainya.

**Karena itu, keperluan akan `and then` menghilang.** Tidak ada `head (L)` yang dapat dievaluasi terlalu dini, karena tidak ada `head` sama sekali. Perbedaan yang pada notasi harus dijaga dengan hati-hati, di sini lenyap bersama sebabnya.

**Teks adalah list, secara harfiah.** Dalam Haskell, `String` adalah nama lain bagi `[Char]`. Seluruh fungsi di atas berlaku atas teks tanpa perubahan apa pun, dan `length' "satrio"` menghasilkan `6`. Yang pada notasi merupakan keputusan rancangan, di sini merupakan kenyataan bahasa.

**Pustaka Haskell mengandung fungsi parsial.** `head []` tidak menghasilkan nilai dan menghentikan program pada saat dijalankan. Notasi menuliskan pembatasannya pada baris definisi (`List tidak kosong`), sedangkan Haskell menuliskan `[a] -> a` dan menyerahkan sisanya kepada kedisiplinan pemakainya. Ini adalah keadaan yang sama dengan `Integer [1..4]` pada Bab 4, kini dengan akibat yang dapat diamati.

| Notasi fungsional | Haskell |
|---|---|
| `type List : Nil \| Cons ⟨head, tail⟩` | sudah tersedia; `data [a] = [] \| a : [a]` |
| `makeNil` | `[]` |
| `makeCons (e, L)` | `e : L` |
| `head (L)`, `tail (L)` | `head l`, `tail l`, atau pola `(x : xs)` |
| `isEmpty (L)` | pola `[]`, atau `null l` |
| `isOneElement (L)` | pola `[x]` |
| `[1, 2, 3]` | `[1, 2, 3]` |
| `List of Character` | `String`, yaitu `[Char]` |
| `List of Integer tidak kosong` | `[Integer]`; pembatasannya hidup pada spesifikasi |
| `type Set : List` dengan invarian | tidak ada padanan; invariannya hidup pada spesifikasi |

Baris terakhir adalah satu-satunya yang tidak mempunyai padanan. Sepanjang bab ini `Set` adalah gagasan yang seluruhnya ditegakkan oleh disiplin, dan terjemahannya ke Haskell tidak mengubah keadaan itu: sistem type Haskell memeriksa banyak hal, tetapi tidak memeriksa yang satu ini.

## Latihan

**Membaca.** Untuk setiap realisasi berikut, tentukan definisinya, tuliskan spesifikasinya, sebutkan basisnya (basis-0 atau basis-1) dan rekurensnya, lalu usulkan nama yang sesuai konvensi.

```
p (L) :
    depend on L
        isEmpty (L)       : 0
        not (isEmpty (L)) : head (L) + p (tail (L))

q (L) :
    depend on L
        isOneElement (L)       : head (L)
        not (isOneElement (L)) : q (tail (L))

r (x, L) :
    depend on L
        isEmpty (L)                               : makeNil
        not (isEmpty (L)) and then (head (L) = x) : r (x, tail (L))
        not (isEmpty (L)) and then (head (L) ≠ x) : makeCons (head (L), r (x, tail (L)))
```

Untuk `q`, jawablah juga: mengapa fungsi ini berbasis-1, dan apa yang terjadi bila basisnya diubah menjadi basis-0? Untuk `r`, sebutkan sebuah aplikasi yang membuat hasilnya lebih pendek daripada masukannya, dan sebuah aplikasi yang membuatnya sama panjang.

**Memperbaiki.** Fungsi berikut seharusnya menghapus elemen kembar yang berdampingan, sehingga `[1, 1, 2]` menjadi `[1, 2]`.

```
JUDUL   Menghapus Kembar Berdampingan
```

```
DEFINISI DAN SPESIFIKASI
    removeAdjacent : List of Integer → List of Integer
    { removeAdjacent (L) adalah L tanpa elemen yang sama dengan elemen
      tepat sebelumnya }
```

```
REALISASI
    removeAdjacent (L) :
        depend on L
            isEmpty (L)                                  : makeNil
            isOneElement (L)                             : L
            not (isEmpty (L)) and not (isOneElement (L)) and then
                (head (L) = head (tail (L)))             : removeAdjacent (tail (L))
            not (isEmpty (L)) and not (isOneElement (L)) and then
                (head (L) ≠ head (tail (L)))             : makeCons (head (L), removeAdjacent (tail (L)))
```

```
APLIKASI
    ⇒ removeAdjacent ([])
      []
    ⇒ removeAdjacent ([1, 1, 2])
      [1, 2]
    ⇒ removeAdjacent ([1, 2, 3])
      [1, 2, 3]
```

1. Ketiga contoh aplikasi menghasilkan jawaban yang benar. Temukan sebuah masukan yang menghasilkan jawaban keliru, dan sebutkan apa yang dihasilkan serta apa yang seharusnya.
2. Kekeliruannya terletak pada kasus ketiga. Jelaskan apa yang terjadi terhadap elemen yang **baru saja** menjadi elemen pertama sesudah rekurens dilakukan.
3. Perbaiki realisasinya. Periksa bahwa perbaikan Anda masih memenuhi syarat lengkap dan disjoint.
4. Tambahkan contoh aplikasi yang, seandainya sudah ada sejak semula, akan menampakkan kekeliruan itu. Berapa contoh yang diperlukan, dan apa cirinya?

**Menulis.** Tuliskan keempat bagian secara lengkap. Sebutkan basisnya, nyatakan prasyarat bila ada, dan pilih contoh aplikasi yang mencakup list kosong, list satu elemen, dan list yang lebih panjang.

1. `append (L1, L2)`, yang menghasilkan seluruh elemen `L1` diikuti seluruh elemen `L2`. Kemudian jawab: mengapa rekursinya dilakukan terhadap `L1` dan bukan terhadap `L2`?
2. `nth (n, L)`, yang menghasilkan elemen ke-`n` dari `L` dengan elemen pertama bernomor 1. Nyatakan prasyaratnya dengan cermat. Berapa aplikasi yang diperlukan untuk mencapai elemen terakhir sebuah list sepanjang seribu?
3. `countIf`-serupa: `countEven (L)`, yang mencacah elemen genap. Bandingkan bentuknya dengan `countChar` pada bab ini.
4. `sort (L)`, yang mengurutkan sebuah list menaik, dengan memakai kembali `insertSorted`. Rekursinya hanya membutuhkan tiga baris. Sebutkan invarian apa yang dijamin oleh `insertSorted` dan mengapa hal ini membuat `sort` menjadi sesingkat itu.
5. `intersection (S1, S2)` atas `Set`. Pastikan hasilnya tetap memenuhi invarian himpunan, dan sebutkan di mana Anda menjaminnya.

**Menelusuri.**

1. Telusuri `reverse ([1, 2, 3])` versi pertama secara lengkap, dengan konstruktor dan bukan penulisan singkat. Hitung aplikasi `reverse` dan aplikasi `addLast` secara terpisah.
2. Telusuri `reverse2 ([1, 2, 3])`. Hitung aplikasinya, dan sebutkan isi `acc` pada setiap langkah.
3. Telusuri `isMember (1, [1, 2, 3])` dan `isMember (9, [1, 2, 3])`. Jelaskan mengapa panjang keduanya berbeda, dan operator apa yang menyebabkannya.
4. Telusuri `union ([1, 2], [2, 3])` dan hitung berapa kali `isMember` diaplikasikan seluruhnya.

**Merancang.** Tiga pertanyaan, ketiganya tanpa jawaban tunggal yang benar.

1. Bahan terdahulu menyediakan dua konstruktor list, satu untuk menambahkan di kiri dan satu di kanan, sehingga kedua ujung tampak setara. Diktat ini hanya menyediakan satu dan menurunkan `addLast` sebagai fungsi biasa. Dengan memakai hitungan aplikasi pada bagian *Menyusun List*, argumenkan kedua pilihan itu. Apa yang menjadi lebih mudah dan apa yang menjadi tersembunyi pada masing-masing?
2. Bab ini mengandaikan seluruh elemen sebuah list bertype sama. Rancanglah teks type bagi sebuah list yang elemennya boleh berupa `Integer` **atau** berupa list, dengan memakai type alternatif dari Bab 5. Kemudian tuliskan definisi dan spesifikasi (tanpa realisasi) sebuah fungsi yang menjumlahkan seluruh bilangan di dalamnya, betapa pun dalam sarangnya. Berapa banyak kasus yang harus ditangani rekurensnya, dan mengapa lebih banyak daripada fungsi mana pun pada bab ini?
3. `sumList` dan `productList` berbeda hanya pada dua tanda, dan `maxList` serta `earliest` berbeda hanya pada cara membandingkan. Usulkan **mekanisme apa pun**, dalam notasi apa pun yang Anda rancang sendiri, yang memungkinkan pasangan semacam itu dituliskan sekali saja. Kemudian jawab pertanyaan yang menentukan: apa yang harus dapat diperlakukan sebagai **nilai** oleh mekanisme Anda, yang sampai sekarang belum pernah diperlakukan demikian dalam diktat ini?
