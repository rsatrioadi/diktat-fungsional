# Pendahuluan

## Tentang Diktat Ini

Diktat ini adalah penyusunan ulang atas *Draft Diktat Kuliah Dasar Pemrograman, Bagian: Pemrograman Fungsional* (Revisi Februari 2014) karya Inggriani Liem. Bahan asli tersebut telah dipakai selama bertahun-tahun di lingkungan Institut Teknologi Bandung, dan sebagian besar keputusan mendasarnya (notasi fungsional empat bagian yang diadopsi dari [3], pemisahan tegas antara belajar memrogram dan belajar bahasa, serta urutan materi dari notasi menuju rekursi dan struktur data) dipertahankan di sini karena keputusan-keputusan itu terbukti tepat.

Yang berubah adalah cakupan dan penyajiannya. Bahasa acuan diperbarui ke sebuah bahasa fungsional arus utama, seluruh materi yang terikat pada bahasa tertentu dipindahkan ke sebuah Suplemen terpisah agar tubuh diktat tetap bebas-bahasa, dan sejumlah bab ditambahkan hingga materi merentang dari notasi fungsional sampai pohon. Pada beberapa tempat, terminologi bahan lama sengaja diubah mengikuti istilah yang kini lazim di bahasa fungsional; setiap perubahan semacam itu ditandai dengan peringatan bagi pembaca yang mengenal bahan terdahulu, agar arti nama yang berkebalikan tidak menjadi sumber kekeliruan.

Diktat ini juga dirancang untuk tidak terikat pada satu mata kuliah tertentu. Susunan kurikulum berubah dari waktu ke waktu: nama, kode, urutan, dan bobot mata kuliah disesuaikan dengan kebutuhan program studi. Yang relatif tetap adalah materi pemrograman fungsional itu sendiri, dan cara termudah untuk mempelajarinya. Karena itu diktat ini menyebut dirinya sebagai *modul*, bukan buku pegangan sebuah kuliah. Capaian pembelajaran yang dituliskan di bawah adalah capaian pada tingkat modul; mata kuliah yang menggunakannya dipersilakan memetakannya ke CPMK masing-masing, memilih bab yang dipakai, dan menentukan bahasa eksekusi yang sesuai dengan konteksnya. Bagian *Cara Memakai Diktat Ini* di akhir bab memberikan panduan untuk itu.

## Paradigma Pemrograman

Komputer dipakai sebagai alat bantu penyelesaian persoalan. Masalahnya, persoalan tidak dapat "disodorkan" begitu saja ke depan komputer untuk dijawab. Ada jarak antara persoalan dengan mesin, dan strategi pemecahan masalah masih harus dituangkan manusia ke dalam bentuk program. Untuk menghasilkan program, seseorang dapat memakai berbagai pendekatan yang dalam bidang pemrograman disebut sebagai **paradigma**.

Paradigma adalah sudut pandang tertentu yang diprioritaskan terhadap sekelompok persoalan. Paradigma mengarahkan perhatian kita kepada beberapa atribut dan membuat kita mengabaikan atribut yang lain. Sebuah paradigma karenanya hanya memberikan pandangan yang terbatas terhadap realitas, dan fanatisme terhadap satu paradigma mempersempit wawasan. Satu paradigma tidak akan cocok untuk semua kelas persoalan.

Beberapa paradigma yang penting dikenal:

1. **Prosedural (imperatif).** Didasari konsep mesin Von Neumann: sekelompok tempat penyimpanan yang dapat diberi nama dan nilai, dengan instruksi yang dieksekusi secara berurutan dan data yang diperiksa serta dimodifikasi secara berurutan pula. Kata kuncinya: *algoritma + struktur data = program*. Keuntungannya adalah efisiensi eksekusi, karena dekat dengan mesin; kerugiannya, pemrogram harus berpikir dalam batasan mesin, yang kadang lebih mengikat daripada batasan persoalannya sendiri.

2. **Fungsional.** Didasari konsep pemetaan dan fungsi dalam matematika. Penyelesaian persoalan dinyatakan sebagai definisi sekumpulan fungsi dan aplikasi terhadap fungsi-fungsi tersebut. Tidak ada pengertian "variabel" yang berubah nilai, tidak ada urutan instruksi, dan tidak ada pemilahan tegas antara data dan program. Setiap fungsi adalah "kotak hitam" yang hanya diperhatikan keadaan awal dan akhirnya.

3. **Deklaratif/lojik.** Didasari pendefinisian relasi antar individu sebagai predikat. Program adalah kumpulan fakta dan aturan deduksi; pemakai mengajukan query, dan sistem menjawab apakah pernyataan itu dapat dideduksi.

4. **Berorientasi objek.** Didasari gagasan bahwa program disusun dari objek-objek yang saling mengirim pesan. Sebuah objek menyatukan data dengan operasi terhadapnya dan menyembunyikan cara data itu direpresentasikan. Kekuatan utamanya terletak pada polimorfisme: pengirim pesan tidak perlu mengetahui type persis penerimanya, sehingga perilaku dapat ditentukan pada saat eksekusi.

5. **Konkuren dan paralel.** Didasari kenyataan bahwa sistem nyata menangani banyak proses yang berjalan bersama. Pemrogram tidak lagi berpikir sekuensial, melainkan harus menangani komunikasi dan sinkronisasi. Paradigma ini bukan alternatif dari keempat paradigma di atas, melainkan lapisan tambahan di atasnya.

Dalam praktik, hampir tidak ada bahasa pemrograman modern yang murni menganut satu paradigma. Yang lebih tepat dikatakan: sebuah bahasa *mendukung* satu atau beberapa paradigma dengan tingkat kenyamanan yang berbeda-beda.

## Letak Pemrograman Fungsional dalam Belajar Memrogram

Diktat ini tidak mengandaikan bahwa pemrograman fungsional dipelajari pada urutan tertentu. Nilainya berbeda menurut dari mana pembaca datang, dan ada baiknya perbedaan itu disadari sejak awal.

**Jika pemrograman fungsional dipelajari lebih dahulu daripada paradigma lain,** nilainya terletak pada kesederhanaan: pembaca belajar abstraksi data dan proses komputasi tanpa perlu memikirkan alokasi memori, urutan eksekusi, ataupun efek samping. Perhatian dapat dipusatkan sepenuhnya pada *apa* yang dihitung sebelum berpindah ke *bagaimana* menghitungnya.

**Jika pemrograman fungsional dipelajari setelah paradigma prosedural** (inilah situasi yang paling umum) nilainya justru terletak pada kontras. Pembaca yang sudah terbiasa dengan variabel, penugasan, dan perulangan akan menemukan bahwa ketiganya tidak diperlukan untuk menyatakan komputasi. Yang dilatih di sini bukan sekadar sintaks baru, melainkan kemampuan untuk melihat bahwa keputusan-keputusan yang selama ini terasa wajib sebenarnya adalah pilihan paradigma. Sebagian dari proses ini adalah proses melepas kebiasaan, dan wajar bila terasa tidak nyaman pada awalnya.

**Apa pun urutannya,** ada satu alasan yang berlaku umum: gagasan yang dipelajari di sini tidak berhenti di bahasa fungsional. Fungsi sebagai nilai, data yang tidak berubah (*immutable*), pemisahan antara perhitungan dan efek, serta operasi seperti `map` dan `filter` kini tersedia di hampir semua bahasa arus utama. Pembaca yang memahaminya secara mendasar di sini akan mengenalinya kembali dalam bahasa apa pun yang dipakainya kelak.

## Bahasa Pemrograman

Bahasa pemrograman berkembang pesat sejak tahun enam puluhan, seringkali dianalogikan dengan menara Babel. Untuk setiap paradigma tersedia bahasa yang mempermudah implementasi rancangan penyelesaian masalahnya:

| Dukungan utama | Contoh bahasa |
|---|---|
| Prosedural | C, Pascal, Fortran, Go |
| Fungsional | Haskell, OCaml, F#, Elm, Scheme/Racket, Clojure, Erlang, Elixir |
| Deklaratif/lojik | Prolog, Datalog |
| Berorientasi objek | Smalltalk, Eiffel, Java, C#, C++ |
| Multiparadigma | Scala, Rust, Swift, Kotlin, Python, JavaScript |

Pembagian di atas semakin lama semakin kabur, dan itu bukan kebetulan. Bahasa-bahasa arus utama secara bertahap mengadopsi fasilitas fungsional: fungsi anonim (*lambda*), fungsi sebagai parameter dan hasil, struktur data yang tidak dapat diubah, dan pustaka `map`/`filter`/`reduce`. Sebaliknya, bahasa fungsional menyediakan mekanisme untuk menangani hal-hal yang secara alamiah bersifat berurutan, seperti masukan dan keluaran.

Karena itu, pertanyaan "bahasa ini fungsional atau bukan" jarang berguna. Pertanyaan yang lebih berguna adalah: *seberapa mudah dan seberapa aman gaya fungsional dapat ditulis dalam bahasa ini?*

## Belajar Memrogram Tidak Sama dengan Belajar Bahasa Pemrograman

**Belajar memrogram** adalah belajar strategi pemecahan masalah, metodologi, dan sistematika, kemudian menuangkannya ke dalam notasi yang disepakati. Sifatnya analisis dan sintesis.

**Belajar bahasa pemrograman** adalah belajar memakai suatu bahasa: aturan sintaks, instruksi yang tersedia, pustaka, serta cara mengoperasikan kompilator atau interpreternya. Sifatnya keterampilan.

Keduanya berbeda tingkat kesulitannya, dan mahasiswa seringkali dihadapkan pada kedua kesulitan itu sekaligus, sehingga kesalahan berpikir sulit dibedakan dari kesalahan mengetik. Diktat ini memisahkan keduanya secara sengaja. *Diktat Pemrograman Fungsional* melatih cara berpikir dengan notasi yang tidak dapat dieksekusi, dan menyisipkan terjemahan singkat ke bahasa acuan pada tempat-tempat yang ditandai: secukupnya agar rancangan dapat dijalankan, tidak lebih. *Suplemen Diktat Pemrograman Fungsional* (selanjutnya disebut sebagai Suplemen) mengambil alih dari situ dan membahas eksekusi secara sistematis.

Pemisahan ini tidak berarti bahwa program tidak perlu dijalankan. Memaksa pembaca hanya bekerja di atas kertas, membuat spesifikasi tanpa pernah mengeksekusi program, tidaklah benar. Sebaliknya, mencetak pemrogram yang langsung mengetik dan mengeksekusi tanpa analisis dan spesifikasi yang dapat dipertanggungjawabkan juga tidak benar. Keduanya saling melengkapi dan tidak dapat dipisahkan.

## Notasi Fungsional dan Bahasa Eksekusi

Pada diktat ini dipakai sebuah **notasi fungsional**, yang diadopsi dari [3] dan dikembangkan lebih lanjut. Setiap fungsi dituliskan dalam empat bagian:

**definisi → spesifikasi → realisasi → aplikasi**

Notasi ini tidak mempunyai pemroses bahasa dan karena itu tidak dapat dieksekusi. Justru di situlah kegunaannya: notasi memaksa pembaca menyatakan *apa* yang dihitung (definisi dan spesifikasi) sebelum menyatakan *bagaimana* menghitungnya (realisasi), dan memaksa keduanya dituliskan agar dapat dibaca serta diperiksa oleh manusia lain.

Meskipun demikian, rancangan pada akhirnya harus dieksekusi oleh mesin. Karena itu diktat ini dilengkapi Suplemen, yang berisi pola penerjemahan dari notasi fungsional ke sebuah bahasa nyata beserta aspek eksekusinya.

Kriteria pemilihan bahasa acuan:

- Mendukung fungsi sebagai nilai dan definisi rekursif secara langsung dan wajar.
- Mempunyai notasi type yang eksplisit, sehingga baris **definisi** pada notasi fungsional dapat diperiksa oleh kompilator, bukan sekadar menjadi komentar.
- Mendukung *pattern matching*, sehingga analisis basis-rekurens dapat diterjemahkan secara langsung.
- Tersedia bebas, terpasang mudah, dan mempunyai lingkungan interaktif untuk mencoba ekspresi satu per satu.

Diktat ini memakai **Haskell** sebagai bahasa acuan, terutama karena kriteria kedua: dalam Haskell, kesalahan berpikir tentang domain dan range terdeteksi pada saat kompilasi, bukan pada saat program dijalankan. Bahasa lain yang memenuhi kriteria di atas (misalnya OCaml, F#, Elm, atau Racket) dapat menggantikannya.

**Apa yang berubah bila bahasa acuannya diganti.** Pernyataan yang jujur bukanlah "tidak ada", melainkan sesuatu yang dapat dihitung. Setiap bab Diktat ini terdiri dari **badan** dan **koda**. Badan bab hanya memakai notasi fungsional dan tidak berubah sedikit pun oleh pergantian bahasa. Koda adalah bagian yang menerjemahkan isi bab tersebut ke bahasa acuan, dan seluruhnya khas bahasa. Daftar lengkap letak koda pada kesepuluh bab tercantum pada Suplemen S1, dan daftar itulah yang harus dikerjakan ulang oleh siapa pun yang mengganti bahasanya. Selain koda, yang perlu ditulis ulang hanyalah bab-bab Suplemen yang bertanda **[B]** pada tabel di bawah.

Batas ini juga menjelaskan mengapa Suplemen dibagi dua lapis alih-alih ditulis satu kali per bahasa. Bagian bertanda **[U]** berisi hal yang benar bagi bahasa acuan mana pun (cara memilih contoh uji, batas apa yang selalu hilang dalam penerjemahan, syarat yang harus dipenuhi sebuah bahasa pengganti) dan ditulis **sekali saja**. Menyalinnya ke setiap bahasa hanya akan membuat salinan-salinan itu berbeda diam-diam.

## Ruang Lingkup dan Batasan

Diktat ini adalah **pengantar**, dirancang untuk porsi waktu yang setara dengan sekitar setengah semester dan tidak berusaha menjadi buku teks pemrograman fungsional yang lengkap. Menyatakan batas ini secara terbuka lebih jujur daripada mencantumkan daftar topik yang tidak akan sempat dibahas dengan layak.

**Yang dicakup:**

- Notasi fungsional: definisi, spesifikasi, realisasi, aplikasi.
- Ekspresi dasar dan evaluasi ekspresi.
- Ekspresi kondisional dan analisis kasus.
- Type dasar dan type bentukan (produk dan alternatif), konstruktor, selektor, predikat.
- Rekursi dan analisis rekurens: basis dan rekurens sebagai alat rancang, bukan sekadar teknik.
- List sebagai type rekursif, beserta persoalan-persoalan tipikalnya.
- Fungsi sebagai nilai: ekspresi lambda, fungsi orde tinggi, `map`/`filter`/`fold`, komposisi.
- Penerjemahan ke bahasa nyata dan aspek eksekusinya.

**Yang tidak dicakup, dan sengaja demikian:**

- *Type class*, polimorfisme lanjut, dan sistem type secara mendalam. Diktat hanya memakai type sejauh diperlukan untuk menyatakan domain dan range.
- Monad dan penanganan efek secara sistematis. Aspek masukan/keluaran hanya diperkenalkan secukupnya pada Bab 9, agar pembaca tidak menyimpulkan bahwa program fungsional tidak dapat berinteraksi dengan dunia luar.
- Evaluasi malas (*lazy evaluation*) dan struktur data tak hingga, selain sebagai catatan singkat.
- Analisis kompleksitas dan struktur data fungsional yang efisien. Pembaca yang tertarik dirujuk ke [8].
- Pemrograman fungsional untuk perangkat lunak skala besar: arsitektur, pustaka, dan perkakas.

Batasan ini berarti bahwa setelah menyelesaikan diktat ini, pembaca **belum** menjadi pemrogram fungsional. Yang diharapkan lebih sederhana dan lebih mendasar: pembaca mampu berpikir dalam paradigma fungsional untuk persoalan berukuran kecil, dan mempunyai landasan yang cukup untuk melanjutkan sendiri.

## Capaian Pembelajaran Modul

Setelah menyelesaikan modul ini, pembaca mampu:

1. **Menjelaskan** ciri paradigma fungsional dan membedakannya dari paradigma prosedural, khususnya dalam hal variabel, urutan eksekusi, dan efek samping.
2. **Menuliskan** definisi, spesifikasi, dan realisasi sebuah fungsi dalam notasi fungsional, dengan domain dan range yang tepat.
3. **Merancang** solusi rekursif untuk persoalan pada bilangan, type bentukan, dan list, dengan menyatakan basis dan rekurens secara eksplisit.
4. **Membaca dan menelusuri** evaluasi sebuah ekspresi fungsional yang ditulis orang lain, serta menjelaskan hasilnya.
5. **Menggunakan** fungsi sebagai parameter dan sebagai hasil, dan **mengenali** kapan sebuah solusi rekursif dapat dinyatakan lebih ringkas sebagai fungsi orde tinggi.
6. **Menerjemahkan** program dalam notasi fungsional ke sebuah bahasa fungsional nyata, mengeksekusinya, dan memeriksa kebenaran hasilnya terhadap spesifikasi yang telah ditulis.

Capaian 4 sering luput dari perhatian dalam pengajaran: kemampuan membaca program adalah aktivitas yang paling banyak dilakukan seorang pemrogram dalam praktik, namun paling jarang dilatih secara eksplisit. Karena itu diktat ini menyediakan latihan membaca berdampingan dengan latihan menulis.

## Ikhtisar Diktat dan Suplemen

**Diktat**

| Bab | Judul | Isi |
|---|---|---|
| 1 | **Pendahuluan** | paradigma, notasi, ruang lingkup |
| 2 | **Notasi Fungsional** | definisi, spesifikasi, realisasi, aplikasi |
| 3 | **Ekspresi Dasar dan Evaluasi** | ekspresi aritmatika, relasional, boolean; nama antara; penelusuran evaluasi |
| 4 | **Ekspresi Kondisional** | analisis kasus, `depend on`, evaluasi hubung-singkat |
| 5 | **Type Bentukan** | produk (tuple/rekord) dan alternatif; konstruktor, selektor, predikat; contoh Point, Fraction, Date |
| 6 | **Ekspresi Rekursif** | analisis rekurens pada bilangan bulat; type rekursif |
| 7 | **List** | list sebagai type rekursif; list of karakter, integer, type bentukan; himpunan; resume analisis rekurens |
| 8 | **Fungsi sebagai Nilai** | ekspresi lambda, fungsi orde tinggi, `map`/`filter`/`fold`, komposisi, aplikasi parsial |
| 9 | **Dari Notasi ke Program** | kemurnian fungsi, masukan/keluaran, strategi evaluasi, arah lanjutan |
| 10 | **Pohon** | pohon n-aire dan biner, representasi prefix/infix/postfix, pohon ekspresi |
| 11 | **List of List** — sarang teratur dan tak teratur; type alternatif rekursif; hubungannya dengan pohon |

Bab 6 dan 7 memikul bobot terbesar dan sebaiknya diberi porsi waktu paling banyak; analisis rekurens adalah landasan bagi seluruh bab sesudahnya. Bab 8 sengaja diletakkan **sesudah** Bab 7, bukan sebelumnya: fungsi orde tinggi baru terasa nilainya ketika pembaca sudah menuliskan sendiri sejumlah fungsi list secara rekursif dan dapat melihat pola yang berulang di antaranya.

Bab 9 berdiri sebagai jembatan: masih memakai notasi, tetapi seluruh pertanyaannya adalah pertanyaan eksekusi. Bab 9 menjawabnya sejauh jawabannya tidak bergantung pada bahasa, dan menyerahkan sisanya kepada Suplemen. Karena itu Bab 9 sekaligus menjadi pintu masuknya.

Bab 10 dan 11 bersifat opsional dan berdiri di luar alur utama. Bila modul ini dipakai berdampingan dengan materi struktur data pada paradigma lain, bab Pohon justru paling berguna sebagai **jembatan antar-paradigma**: struktur yang sama dirancang dua kali, secara fungsional dan secara imperatif, lalu dibandingkan. Bab 11 berbentuk soal panjang dan tidak memperkenalkan apa pun yang baru; ia menutup pertanyaan yang sudah diajukan pada latihan Bab 7 dan mengandaikan Bab 10 sudah dibaca.

**Suplemen — Panduan Eksekusi**

(Sedang disusun, belum tersedia.)

Suplemen dibaca berurutan dari S1 sampai S10 dan merupakan satu bacaan yang utuh. Tanda **[U]** dan **[B]** hanya menyangkut pemeliharaan: **[U]** berlaku bagi bahasa acuan mana pun, **[B]** khas bagi bahasa acuan yang dipakai.

| Bab | Judul | Isi | |
|---|---|---|---|
| S1 | **Dari Notasi ke Mesin** | apa yang ada di Suplemen, alur kerja, letak koda pada setiap bab Diktat | [U] |
| S2 | **Syarat Bahasa Acuan** | daftar periksa bagi bahasa pengganti; apa yang berubah dan apa yang tidak | [U] |
| S3 | **Lingkungan Kerja** | berkas, titik masuk program, lingkungan interaktif, cara menjalankan | [B] |
| S4 | **Padanan Lengkap** | seluruh konstruk notasi dalam satu tabel | [B] |
| S5 | **Batas Penerjemahan** | yang tidak berpadanan ke dua arah; fungsi yang sengaja tidak diterjemahkan | [U] |
| S6 | **Yang Ditambahkan Bahasa** | prasyarat pada type, polimorfisme, teks, pustaka standar | [B] |
| S7 | **Menguji Rancangan terhadap Spesifikasi** | memilih contoh, dari contoh ke sifat, batas pengujian | [U] |
| S8 | **Perkakas Pengujian** | menjalankan contoh dan sifat dengan mesin | [B] |
| S9 | **Membaca Pesan Kesalahan** | dari pesan kompilator ke kesalahan rancangan | [B] |
| S10 | **Eksekusi dan Sumber Daya** | strategi evaluasi, posisi ekor, harga sebuah realisasi | [B] |

Tidak semua fungsi pada Diktat ini diterjemahkan; sebagian sengaja disisakan sebagai latihan, dan daftarnya tercantum pada S5.

## Cara Memakai Diktat Ini

**Bagi pembaca.** Diktat ini adalah buku kerja, bukan buku teks. Membacanya tanpa menuliskan sendiri definisi, spesifikasi, dan realisasi tidak akan banyak berguna. Untuk setiap contoh, tutuplah bagian realisasi dan cobalah menuliskannya sendiri dari spesifikasi yang diberikan, lalu bandingkan. Untuk setiap fungsi yang Anda tulis, jalankan dalam bahasa acuan dan periksa apakah hasilnya sesuai dengan spesifikasi yang Anda tulis sendiri. Inilah bentuk pengujian yang paling sederhana dan paling sering terlupakan.

**Bagi pengajar.** Bab 2–8 merupakan inti yang saling bergantung dan sebaiknya diambil utuh; Bab 9 dapat diringkas menjadi satu pertemuan bila waktu sempit, tetapi tidak dihilangkan. Bab 10, Bab 11, dan sebagian besar contoh pada Bab 7 dapat dipilih sesuai waktu yang tersedia. Bab 11 dirancang untuk dibaca dan dikerjakan sendiri, bukan untuk diajarkan, dan dapat diberikan sebagai bahan pengayaan tanpa satu pertemuan pun. Bila modul ini menempati kurang dari setengah semester, kompresi yang disarankan adalah mengurangi jumlah contoh pada Bab 7, bukan menghapus Bab 8, karena tanpa Bab 8, pembaca akan menyimpulkan bahwa pemrograman fungsional identik dengan rekursi manual.

**Bagi pengajar yang mengganti bahasa acuan.** Kerjakan S2 lebih dahulu: bab tersebut adalah daftar periksa yang menyatakan apa yang harus disediakan sebuah bahasa pengganti, dan sekaligus daftar pekerjaan yang menanti. Sesudahnya, tulis ulang koda Diktat menurut daftar pada S1, lalu bab Suplemen bertanda **[B]**. Jangan menyentuh badan bab dan jangan menyentuh bab bertanda **[U]**; bila salah satunya terasa perlu diubah, kemungkinan besar yang keliru adalah pembagiannya, dan hal itu pantas dilaporkan alih-alih ditambal setempat.

Contoh-contoh pada diktat ini disusun sebagai **contoh tipikal**: pola solusi dari kelas persoalan, bukan sekadar soal. Sebagian dibahas rinci di kelas, sisanya dipakai sebagai bahan latihan membaca dan memahami program.
