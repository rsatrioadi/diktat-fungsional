# Dari Notasi ke Program

Delapan bab telah menuliskan rancangan. Setiap rancangan itu pada akhirnya harus dijalankan mesin, dan pada saat itu muncul pertanyaan-pertanyaan yang sepanjang delapan bab tersebut sengaja tidak dijawab.

Bab 2 menyatakan bahwa notasi ini tidak mencakup strategi evaluasi, tidak mencakup efek, masukan, dan keluaran. Bab 3 menyatakan bahwa notasi tidak menetapkan urutan evaluasi, karena tidak perlu. Bab 6 menghitung banyaknya aplikasi tetapi tidak mengatakan apa-apa tentang apa yang terjadi di dalam mesin. Bab 8 memperkenalkan lipatan dan menunda pembicaraan tentang arahnya.

Seluruh penundaan itu bermuara pada tiga pertanyaan:

| Pertanyaan | Dijawab oleh |
|---|---|
| **Kapan** sebuah bagian ekspresi dihitung? | bahasa, bukan rancangan |
| **Berapa kali**, dan dengan sumber daya berapa? | bahasa dan mesin |
| Bagaimana program berurusan dengan **dunia di luar dirinya**? | bahasa dan sistem operasi |

Ketiganya adalah pertanyaan eksekusi. Notasi menolak menjawabnya bukan karena lalai, melainkan karena jawabannya berbeda-beda menurut bahasa yang dipakai, sedangkan rancangan yang baik seharusnya tetap sah ketika bahasanya diganti.

Bab ini menjawab ketiganya **sejauh jawabannya tidak bergantung pada bahasa**, dan menunjukkan di mana jawaban bagi bahasa acuan berada. Pembagian itu bukan kehati-hatian yang berlebihan. Jawaban yang bergantung pada bahasa akan berubah ketika bahasanya diganti, dan sebagian di antaranya berubah sampai terbalik; yang dituliskan di bawah ini tidak. Bab ini adalah bab terakhir Diktat.

## Kapan: Strategi Evaluasi

Bab 3 menelusuri `distance (1.0, 3.0, 5.0, 6.0)` dengan dua urutan yang berbeda dan memperoleh hasil yang sama. Kesimpulannya: urutan evaluasi tidak mengubah hasil, dan karena itu notasi tidak perlu menetapkannya.

Kesimpulan itu benar, dan tidak lengkap. Yang tidak berubah adalah **hasilnya**. Yang berubah adalah **pekerjaannya**.

Perhatikan fungsi berikut:

```
first (a, b) : a
```

lalu perhatikan aplikasi `first (5, factorial (20))`.

Menurut spesifikasinya, hasilnya adalah `5`. Pertanyaannya: apakah `factorial (20)` dihitung?

Ada dua jawaban, dan keduanya dipakai oleh bahasa nyata.

**Evaluasi ketat (*strict*).** Seluruh argumen dihitung lebih dahulu, baru fungsinya diaplikasikan.

```
⇒ first (5, factorial (20))
  → first (5, 2432902008176640000)    { reduksi factorial, 21 aplikasi }
  → 5                                 { ekspansi first }
```

**Evaluasi malas (*lazy*).** Argumen baru dihitung bila dan ketika nilainya benar-benar dibutuhkan.

```
⇒ first (5, factorial (20))
  → 5                                 { ekspansi first }
```

Hasilnya sama. Pekerjaannya berbeda dua puluh satu aplikasi.

Kebanyakan bahasa bersifat ketat: C, Python, Java, OCaml. Sebagian kecil bersifat malas. Pilihan di antara keduanya adalah salah satu keputusan rancangan sebuah bahasa yang paling menentukan, dan akibatnya terasa pada hampir setiap program yang cukup besar.

Perhatikan bahwa notasi kita tetap benar dalam kedua keadaan. Itulah sebabnya urutan tidak ditetapkan. Rancangan yang sama dapat dijalankan oleh bahasa ketat maupun malas, dan spesifikasinya tidak berubah sedikit pun.

Yang berubah adalah **pekerjaannya**, dan pekerjaan adalah urusan bahasa. Pilihan yang diambil bahasa acuan Anda, beserta akibat yang perlu diketahui, dibahas pada Suplemen S10.

## Berapa Kali: Posisi Ekor

Bab 6 meletakkan `fibFrom` berdampingan dengan sebuah loop dan menyatakan bahwa keduanya berbentuk sama. Pertanyaan yang ditunda saat itu adalah apakah keduanya juga **dijalankan** dengan cara yang sama.

Perbedaannya terletak pada satu hal. Perhatikan kedua rekurens ini:

```
factorial (n) : ... n * factorial (n - 1)
fibFrom (k, a, b) : ... fibFrom (k - 1, b, a + b)
```

Pada `factorial`, aplikasi rekursifnya **bukan** hal terakhir yang dikerjakan. Setelah `factorial (n - 1)` menghasilkan nilai, masih ada perkalian yang menunggu. Pada `fibFrom`, aplikasi rekursifnya **adalah** hal terakhir; hasilnya langsung menjadi hasil keseluruhan tanpa ada yang menunggu.

Aplikasi yang merupakan hal terakhir yang dikerjakan disebut berada pada **posisi ekor** (*tail position*), dan fungsi yang seluruh rekurensnya berada pada posisi ekor disebut **rekursif ekor** (*tail-recursive*).

Perbedaan ini terlihat pada penelusuran Bab 6. `factorial (4)` membesar lebih dahulu sampai basis, baru mengecil, karena setiap lapis menyimpan perkalian yang tertunda. `fibFrom` tidak pernah membesar; setiap langkah menggantikan langkah sebelumnya seluruhnya.

Pada bahasa yang menjaminnya (Scheme, OCaml, Lua, dan lainnya), aplikasi pada posisi ekor tidak memerlukan tempat baru sama sekali: fungsi rekursif ekor dijalankan dengan sumber daya tetap, persis seperti loop. Jaminan itu mempunyai nama, yaitu *tail call optimisation*, dan pada bahasa-bahasa tersebut jaminan itu adalah bagian dari definisi bahasanya, bukan kemurahan hati kompilatornya.

Tidak semua bahasa memberikan jaminan tersebut. Yang lebih perlu diwaspadai: pada sebagian bahasa yang memberikannya, bentuk berakumulator **tetap** tidak lebih hemat, karena sumber daya yang kritis di sana bukan tempat penyimpanan perhitungan yang tertunda melainkan hal lain sama sekali. Karena itu kalimat yang sering diucapkan, yaitu *fungsi rekursif ekor diterjemahkan menjadi loop*, tidak berlaku umum dan tidak boleh dipercaya begitu saja. Apa yang sesungguhnya terjadi pada bahasa acuan Anda dibahas pada Suplemen S10, dan jawabannya mungkin mengejutkan.

Satu hal yang pantas dibawa dari sini, dan sifatnya rancangan alih-alih eksekusi: **posisi ekor dapat dilihat langsung dari teks realisasi**, sebelum bahasa apa pun dipilih. Kemampuan melihatnya adalah milik perancang. Apa yang kemudian dilakukan mesin terhadapnya adalah milik bahasanya, dan kedua hal itu sebaiknya tidak dicampur.

## Mengapa Bentuk Non-Akumulator Tetap Diajarkan

Bab 6 menunda pertanyaan ini, dan sekarang jawabannya dapat diberikan utuh.

**Karena spesifikasinya jauh lebih mudah ditulis dengan benar.** Inilah alasan yang paling kuat dan paling sering diremehkan. Bab 6 sudah memperlihatkannya pada `fibFrom`, dan Bab 7 pada `reverseOnto`: realisasinya lebih pendek, spesifikasinya jauh lebih sulit. Kesalahan pada fungsi berakumulator hampir selalu terletak pada nilai awal akumulatornya, dan kesalahan itu hanya dapat ditemukan oleh orang yang sudah menuliskan artinya dengan tepat.

**Karena sebagian besar fungsi tidak diuntungkan olehnya.** Bab 7 menyatakan hal ini dan menyebutkan bahwa dari seluruh fungsi di sana, hanya `reverse` yang jelas diuntungkan. Untuk `squareAll`, bentuk berakumulator bahkan menuntut satu `reverse` tambahan di ujung.

**Karena bentuknya sesuai dengan type-nya.** Rekursi yang menggabungkan `head (L)` dengan hasil atas `tail (L)` mengikuti bentuk definisi list itu sendiri, dan justru karena itulah Bab 7 dapat menyatakan bahwa basis dan konvergensinya datang cuma-cuma dari type-nya. Bentuk berakumulator meninggalkan kesesuaian tersebut dan menggantinya dengan sesuatu yang harus dijaga sendiri.

**Karena keputusan ini berumur lebih pendek daripada rancangannya.** Bahasa berganti, kompilator membaik, dan apa yang hari ini mahal mungkin besok tidak. Spesifikasi yang jelas bertahan melewati semuanya.

## Dunia Luar: Kemurnian

Ada satu sifat yang dimiliki **setiap** fungsi yang ditulis sejak Bab 2, dan yang belum pernah diberi nama.

Sebuah fungsi disebut **murni** (*pure*) bila:

- hasilnya ditentukan sepenuhnya oleh argumennya, sehingga argumen yang sama selalu menghasilkan nilai yang sama; dan
- mengaplikasikannya tidak mengubah apa pun di luar dirinya.

Seluruh isi Bab 2 sampai Bab 8 murni, tanpa satu perkecualian. Hal itu bukan kebetulan, dan bukan pula pembatasan yang diberlakukan diam-diam. Notasi ini memang tidak menyediakan cara untuk menulis yang tidak murni.

Bandingkan dengan fungsi berikut, yang ditulis dalam bahasa yang mungkin lebih dikenal pembaca:

```c
int counter = 0;

int next(void) {
    counter = counter + 1;
    return counter;
}
```

`next()` menghasilkan nilai yang berbeda setiap kali dipanggil dengan argumen yang sama, yaitu tanpa argumen sama sekali. Fungsi tersebut juga mengubah sesuatu di luar dirinya, sehingga kedua syarat kemurnian dilanggar sekaligus.

Sekarang perhatikan apa yang hilang bersama kemurnian. Bab 2 menyatakan bahwa sebuah ekspresi dapat digantikan oleh nilainya tanpa mengubah arti program, dan menyebutnya transparansi referensial. Untuk `next()`, penggantian itu tidak sah: `next() + next()` tidak sama dengan `2 * next()`. Untuk `square (3)`, penggantian itu selalu sah.

**Apa yang dibeli oleh kemurnian**, dikumpulkan dari delapan bab:

| Yang diperoleh | Pertama muncul |
|---|---|
| Ekspresi dapat digantikan oleh nilainya | Bab 2 |
| Urutan evaluasi tidak mengubah hasil | Bab 3 |
| Bagian yang saling bebas dapat dievaluasi bersamaan | Bab 3 |
| Sebuah ekspresi dapat dipahami dengan membacanya saja | Bab 3 |
| Penelusuran di atas kertas dapat dipercaya | Bab 3 dan seterusnya |
| Fungsi dapat diserahkan ke mana saja tanpa khawatir | Bab 8 |
| Pengujian tidak memerlukan penyiapan keadaan | bab ini |

Baris terakhir pantas diperhatikan. Untuk menguji `next()`, kita harus tahu sudah berapa kali fungsi itu dipanggil sebelumnya. Untuk menguji `sumDigits`, tidak ada yang perlu disiapkan. Seluruh yang dibutuhkan adalah argumennya.

## Masukan dan Keluaran

Kemurnian menimbulkan pertanyaan yang wajar. Membaca dari papan ketik menghasilkan nilai yang berbeda setiap kali. Menulis ke layar mengubah sesuatu di luar program. Bila seluruh fungsi harus murni, bagaimana program fungsional dapat berguna sama sekali?

Bab 2 sudah memberikan jawabannya dalam satu kalimat: aksi membaca dan menulis diwakili oleh interaksi pemakai dengan lingkungan eksekusi, bukan oleh program. Sekarang kalimat itu dapat dijelaskan.

Kuncinya adalah membedakan **deskripsi sebuah aksi** dari **pelaksanaan aksi tersebut**. "Bacalah sebuah baris dari papan ketik" adalah sebuah kalimat; menuliskan kalimat itu, menyalinnya, atau merangkainya dengan kalimat lain tidak membaca apa pun. Yang membaca adalah pihak yang akhirnya menjalankannya, dan pihak itu adalah lingkungan eksekusi, bukan program. Menyusun deskripsi tetap murni sepenuhnya. Sebuah program fungsional karenanya bukan rangkaian aksi, melainkan sebuah **nilai** yang menggambarkan aksi apa yang hendak dilakukan, yang lalu diserahkan kepada lingkungannya untuk dijalankan.

Bahasa berbeda dalam satu hal, dan perbedaannya besar: **apakah perbedaan antara perhitungan dan aksi ditulis ke dalam type, atau tidak.**

- Bila **ya**, sebuah fungsi yang boleh membaca papan ketik mempunyai type yang berbeda dari fungsi yang tidak boleh, dan kompilator menolak yang melanggar. Batas antara inti yang murni dan tepi yang berefek berhenti menjadi janji dan menjadi sesuatu yang diperiksa mesin.
- Bila **tidak**, batas itu tetap ada dan tetap sama pentingnya, hanya saja penegakannya diserahkan kepada disiplin penulisnya. Keadaannya persis seperti invarian `Set` pada Bab 7: benar, penting, dan tidak diperiksa siapa pun.

Yang dilakukan bahasa acuan Anda dibahas pada Suplemen S3 dan S6.

Perhatikan bahwa kemungkinan pertama adalah **pembalikan** dari apa yang berulang sepanjang Diktat. Bab 4 dan Bab 5 memperlihatkan notasi menuliskan pembatasan yang tidak dapat diperiksa bahasanya. Sebuah bahasa yang menuliskan efek ke dalam type melakukan yang sebaliknya: menuliskan pembatasan yang notasi kita tidak mempunyai sarana untuk menyatakannya sama sekali.

Satu bentuk berlaku dalam kedua keadaan dan pantas dijadikan kebiasaan sejak sekarang: **inti yang murni, dikelilingi tepi yang menangani efek**, dengan tepi itu dibuat setipis mungkin. Seluruh fungsi yang pembaca tulis sejak Bab 2 adalah calon isi inti tersebut dan tidak perlu diubah sedikit pun untuk dipakai di dalam sebuah program yang berinteraksi; yang ditambahkan hanyalah lapisan tipis di sekelilingnya. Gagasan ini berlaku jauh melampaui bahasa fungsional, dan pembaca yang tertarik dirujuk ke [7].

Mekanisme yang dipakai bahasa-bahasa fungsional untuk merangkai deskripsi aksi mempunyai nama, yaitu **monad**. Mekanisme tersebut adalah salah satu gagasan penting dalam pemrograman fungsional modern, dan berada di luar cakupan diktat ini sebagaimana dinyatakan pada Bab 1. Namanya disebutkan di sini agar pembaca dapat mencarinya sendiri.

## Menguji Rancangan terhadap Spesifikasi

Pembaca sudah menguji program sejak Bab 2 tanpa istilah itu dipakai.

Bab 2 menganjurkan menuliskan hasil yang diharapkan **sebelum** menjalankan, dan menjelaskan alasannya: hal itu mengubah eksekusi dari "mari lihat apa yang keluar" menjadi "mari periksa apakah dugaan saya benar". Bab 3 memilih empat aplikasi `isLeapYear` sehingga setiap cabang tersentuh. Bab 4 menuntut setiap nilai batas diuji. Bab 5 dan Bab 7 menuntut list kosong dan list satu elemen selalu ada di antara contoh aplikasinya.

Semua itu adalah pengujian, dilakukan dengan tangan.

Yang berubah pada saat program dijalankan hanyalah siapa yang mengerjakannya. Setiap baris `APLIKASI` yang sudah pembaca tuliskan adalah sepasang ekspresi dan hasil yang diharapkan, dan pasangan semacam itu dapat dituliskan sebagai program yang memeriksa dirinya sendiri: kumpulkan seluruhnya, bandingkan hasil sesungguhnya dengan hasil yang diharapkan, dan hasilkan `true` bila semuanya cocok. Bentuknya dalam bahasa acuan dibahas pada Suplemen S8. Cara ini sederhana sampai terasa remeh, dan sudah menangkap sebagian besar kesalahan yang dibuat pada saat menulis.

Ada bentuk pengujian yang lebih kuat dan sangat cocok bagi program murni. Alih-alih memeriksa contoh satu per satu, kita menyatakan **sifat** yang harus berlaku bagi seluruh masukan, lalu mesin mencobanya dengan ratusan masukan yang dibangkitkan sendiri. Sifat semacam "membalik sebuah list dua kali menghasilkan list semula" atau "hasil `sortBy` selalu sepanjang masukannya" dapat dituliskan langsung sebagai program. Gagasan ini disebut **pengujian berbasis sifat** (*property-based testing*), dan perkakasnya tersedia bagi hampir setiap bahasa. Cara memilih sifat dibahas pada Suplemen S7, dan cara menjalankannya pada S8.

Perhatikan bahwa kemurnian membuat semua ini murah. Tidak ada keadaan yang harus disiapkan sebelum menguji dan tidak ada yang harus dibersihkan sesudahnya, karena tidak ada fungsi yang meninggalkan jejak.

## Arah Lanjutan

Bab 1 menyatakan bahwa setelah menyelesaikan diktat ini, pembaca **belum** menjadi pemrogram fungsional. Pernyataan itu sekarang dapat dirinci.

**Yang sudah dapat dilakukan pembaca.** Menyatakan sebuah persoalan sebagai definisi, spesifikasi, realisasi, dan aplikasi. Melakukan analisis kasus yang lengkap dan disjoint, dan mengenali di mana kesalahan batas bersembunyi. Merancang type bentukan beserta invariannya. Melakukan analisis rekurens atas bilangan dan atas struktur, dan membuktikan bahwa evaluasinya berhenti. Mengenali pola berulang dan mengangkatnya menjadi fungsi orde tinggi. Membaca dan menelusuri program yang ditulis orang lain. Menerjemahkan semuanya ke sebuah bahasa nyata dan memeriksanya terhadap spesifikasi yang ditulis sendiri.

**Yang belum.** Sistem type secara mendalam, termasuk polimorfisme yang membuat `map` dapat mengubah type elemennya, dan cara sebuah bahasa menuliskan prasyarat pada type. Monad dan penanganan efek secara sistematis. Struktur data fungsional yang efisien, karena list bukan satu-satunya dan sering bukan yang terbaik. Analisis kompleksitas, yang akan pembaca temui dalam materi lain dan yang akan terasa akrab karena sudah terbiasa menghitung aplikasi sejak Bab 6. Pemrograman fungsional untuk perangkat lunak skala besar.

**Ke mana melanjutkan.** Untuk pendalaman bahasa acuan secara sistematis, [1] atau [2]. Untuk cara berpikir fungsional yang lebih matang dengan contoh yang lebih besar, [4]. Untuk argumen tentang mengapa semua ini penting, dalam dua puluh halaman yang masih relevan setelah tiga dekade, [5]. Untuk penerapan gagasan ini di luar bahasa fungsional, yaitu di tempat kebanyakan pembaca akan bekerja, [7]. Untuk pembaca yang mempertanyakan efisiensi struktur data fungsional, [8].

**Suplemen** melanjutkan langsung dari bab ini dan menjawab bagian yang sengaja tidak dijawab di sini: apa yang sesungguhnya dilakukan bahasa acuan terhadap ketiga pertanyaan di atas, padanan yang lengkap bagi seluruh konstruk notasi, perkakas, dan pengujian. Suplemen juga memuat sebagian besar latihan di depan mesin untuk bab ini.

**Bab 10** membahas pohon, dan bersifat opsional. Bab tersebut berdiri di luar alur utama Diktat karena tujuannya berbeda: sebuah struktur yang sama dirancang dua kali, secara fungsional dan secara imperatif, lalu dibandingkan. Pembaca yang mempelajari struktur data dalam paradigma lain akan menemukan bab itu paling berguna sebagai jembatan.

Satu hal terakhir. Notasi yang dipakai sepanjang delapan bab ini tidak dapat dieksekusi, dan Bab 8 memperlihatkan bahwa ada hal yang bahkan tidak dapat dinyatakannya. Pembaca berhak bertanya apakah notasi tersebut sepadan. Jawaban diktat ini sudah dinyatakan sejak Bab 2 dan tidak berubah: notasi memaksa penulisnya menyatakan apa yang dihitung sebelum bagaimana menghitungnya, dan paksaan itu mudah dilewati bila kita langsung mengetik di depan mesin. Mulai sekarang pembaca akan mengetik langsung. Pertanyaan yang pantas dibawa serta adalah apakah kebiasaan menyatakan definisi dan spesifikasi lebih dahulu ikut terbawa, atau ditinggalkan bersama notasinya.

## Latihan

Latihan bab ini terbagi dua. Yang di bawah ini dikerjakan **di atas kertas** dan tidak memerlukan mesin, karena seluruhnya menyangkut hal yang berlaku bagi bahasa fungsional mana pun. Latihan yang dikerjakan **di depan mesin** berada pada Suplemen S3 sampai S10, dan sebaiknya dikerjakan berdampingan dengan bab ini.

**Membaca.** Untuk setiap realisasi berikut, tentukan apakah aplikasi rekursifnya berada pada posisi ekor. Jawablah dengan menunjuk pekerjaan apa yang masih menunggu setelah aplikasi rekursifnya menghasilkan nilai; bila tidak ada, aplikasi tersebut berada pada posisi ekor.

```
sumDigits (n)      : depend on n
                         n < 10  : n
                         n ≥ 10  : (n mod 10) + sumDigits (n div 10)

fibFrom (k, a, b)  : depend on k
                         k = 0   : a
                         k > 0   : fibFrom (k - 1, b, a + b)

isMember (x, L)    : depend on L
                         isEmpty (L)       : false
                         not (isEmpty (L)) : (head (L) = x) or else isMember (x, tail (L))

reverseOnto (L, R) : depend on L
                         isEmpty (L)       : R
                         not (isEmpty (L)) : reverseOnto (tail (L), makeCons (head (L), R))

union (S1, S2)     : depend on S1
                         isEmpty (S1)       : S2
                         not (isEmpty (S1)) : insertSet (head (S1), union (tail (S1), S2))
```

Untuk `isMember`, jawabannya bergantung pada satu hal yang halus. Sebutkan apa.

**Menelusuri.** Perhatikan `first (a, b) : a` dan aplikasi `first (5, factorial (20))`.

1. Tuliskan penelusurannya menurut evaluasi ketat, dan hitung banyaknya aplikasi.
2. Tuliskan penelusurannya menurut evaluasi malas, dan hitung banyaknya aplikasi.
3. Kedua penelusuran menghasilkan nilai yang sama. Apa yang membuat hal itu terjamin, dan sifat apa dari Bab 2 yang menjaminnya?
4. Susunlah sebuah aplikasi yang, dengan evaluasi ketat, **tidak menghasilkan nilai sama sekali**, tetapi dengan evaluasi malas menghasilkan nilai. Petunjuk: pakailah sebuah fungsi yang tidak terdefinisi bagi argumen tertentu, misalnya pembagian dengan nol.

**Merancang.** Tiga pertanyaan, ketiganya tanpa jawaban tunggal yang benar.

1. Bab 8 menunjukkan bahwa notasi ini tidak dapat menyatakan aplikasi parsial, dan bab ini menunjukkan bahwa perbedaan antara perhitungan dan aksi juga tidak dapat dinyatakannya. Susunlah daftar hal-hal yang tidak dapat dinyatakan notasi tetapi dapat dinyatakan sebuah bahasa nyata, dan daftar sebaliknya. Setelah kedua daftar itu tersusun, jawablah: apakah notasi ini sepadan dengan waktu yang dihabiskan untuk mempelajarinya? Jawaban "tidak" dapat dipertanggungjawabkan bila alasannya kuat. Bandingkan daftar Anda dengan Suplemen S5 sesudahnya, bukan sebelumnya.
2. Bab ini menganjurkan inti yang murni dikelilingi tepi yang menangani efek. Ambil sebuah program yang pernah Anda tulis dalam paradigma prosedural, dan tandai bagian mana yang sesungguhnya murni dan bagian mana yang tidak. Seberapa besar bagian yang murni, dan apakah batasnya terlihat dari teksnya?
3. Suatu saat Anda akan menulis program dalam bahasa yang tidak fungsional, tanpa siapa pun yang menuntut Anda menuliskan definisi dan spesifikasi lebih dahulu. Kebiasaan mana dari diktat ini yang akan Anda bawa, kebiasaan mana yang akan Anda tinggalkan, dan apa yang akan menjadi ongkosnya masing-masing?
