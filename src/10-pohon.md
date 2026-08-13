# Pohon

Bab ini berdiri di luar alur utama Diktat. Bab 9 sudah menutup pembahasan, dan pembaca yang berhenti di sana sudah menyelesaikan modul ini secara utuh.

Tujuan bab ini berbeda dari kesembilan bab sebelumnya. Sepanjang Bab 2 sampai Bab 9, perbandingan yang berulang adalah perbandingan antara notasi fungsional dan bahasa acuannya. Perbandingan tersebut sudah selesai. Yang dibandingkan di sini adalah dua **paradigma**: sebuah struktur yang sama dirancang dua kali, sekali secara fungsional dan sekali secara prosedural, lalu keduanya diletakkan berdampingan.

Pohon dipilih sebagai bahan perbandingan karena dua alasan. Pertama, pohon adalah type rekursif, sehingga seluruh Bab 5, 6, dan 7 langsung dapat dipakai kembali tanpa mesin baru. Kedua, pohon adalah struktur pertama yang perbedaan kedua rancangannya benar-benar terasa. Untuk bilangan dan untuk list, kedua paradigma menghasilkan teks yang tidak terlalu jauh berbeda. Untuk pohon, perbedaannya besar dan pantas diperiksa.

Bab ini hampir tidak memperkenalkan mesin baru. Yang dituliskan di sini adalah type alternatif dari Bab 5, analisis rekurens dari Bab 6, dan list sebagai hasil dari Bab 7.

## Pohon

Sebuah **pohon** adalah struktur yang menghubungkan sekumpulan **simpul** secara berjenjang. Satu simpul berkedudukan sebagai **akar**, dan setiap simpul lain mempunyai tepat satu simpul induk.

Istilah yang dipakai sepanjang bab ini:

| Istilah | Arti |
|---|---|
| Simpul (*node*) | satu titik pada pohon, berisi sebuah nilai |
| Akar (*root*) | simpul yang tidak mempunyai induk |
| Anak (*child*) | simpul yang berinduk pada simpul lain |
| Daun (*leaf*) | simpul yang tidak mempunyai anak |
| Upapohon (*subtree*) | sebuah simpul beserta seluruh keturunannya, yang dengan sendirinya juga sebuah pohon |
| Tinggi (*height*) | banyaknya simpul pada jalur terpanjang dari akar sampai daun |

Baris terakhir pada tabel di atas adalah kunci seluruh bab ini. Sebuah upapohon **adalah sebuah pohon**, dan pernyataan tersebut persis merupakan bentuk sebuah definisi rekursif.

Persoalan yang berbentuk pohon banyak ditemui: struktur bab sebuah buku, klasifikasi makhluk hidup, silsilah keluarga, susunan berkas dalam sebuah sistem operasi, menu sebuah aplikasi, dan ekspresi aritmatika. Yang terakhir dibahas tersendiri pada bab ini.

## Pohon N-aire

Bentuk yang paling umum tidak membatasi banyaknya anak sebuah simpul. Definisi rekursifnya:

- Sebuah pohon adalah sebuah simpul berisi nilai, beserta sebuah **list** pohon sebagai anak-anaknya.

Perhatikan bahwa tidak ada basis yang terpisah. Basisnya tersembunyi di dalam list: sebuah daun adalah simpul yang list anaknya kosong.

```
TYPE TREE
```

```
DEFINISI TYPE
    type Tree : Node ⟨info : Element, children : List of Tree⟩
    { Sebuah Tree adalah sebuah simpul berisi info, beserta list anaknya.
      Sebuah simpul tanpa anak adalah daun }

DEFINISI DAN SPESIFIKASI SELEKTOR
    info : Tree → Element
    { info (T) memberikan nilai yang tersimpan pada akar T }

    children : Tree → List of Tree
    { children (T) memberikan list upapohon anak dari akar T }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makeTree : Element, List of Tree → Tree
    { makeTree (e, F) membentuk pohon berakar bernilai e dengan anak F }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isLeaf : Tree → Boolean
    { isLeaf (T) bernilai true bila akar T tidak mempunyai anak }
```

```
REALISASI
    isLeaf (T) : isEmpty (children (T))
```

Perhatikan bahwa type ini hanya mempunyai satu alternatif, dan alternatif tersebut menuntut sebuah `info`. Karena itu **tidak ada pohon kosong**: setiap `Tree` sekurang-kurangnya mempunyai akar. Hal ini sesuai dengan pengertian pohon sebagai struktur yang berakar; "pohon tanpa akar" bukan pohon yang kebetulan kecil, melainkan sesuatu yang lain. Yang boleh kosong adalah **list anaknya**, dan di situlah basis rekurensnya berada. Perhatikan akibatnya pada range `sizeTree`, yaitu `Integer > 0` dan bukan `Integer ≥ 0`.

Sekarang perhatikan apa yang terjadi ketika kita menghitung banyaknya simpul.

```
JUDUL   Banyaknya Simpul Sebuah Pohon N-aire
```

```
DEFINISI DAN SPESIFIKASI
    sizeTree : Tree → Integer > 0
    { sizeTree (T) adalah banyaknya simpul pada T, termasuk akarnya }
    { Rekurens: 1 + banyaknya simpul pada seluruh anak T }

    sizeForest : List of Tree → Integer ≥ 0
    { sizeForest (F) adalah jumlah banyaknya simpul pada seluruh pohon dalam F }
    { Basis-0:  isEmpty (F)       → 0
      Rekurens: not (isEmpty (F)) → sizeTree (head (F)) + sizeForest (tail (F)) }
```

```
REALISASI
    sizeTree (T) : 1 + sizeForest (children (T))

    sizeForest (F) :
        depend on F
            isEmpty (F)       : 0
            not (isEmpty (F)) : sizeTree (head (F)) + sizeForest (tail (F))
```

```
APLIKASI
    ⇒ sizeTree (makeTree (1, []))
      1
    ⇒ sizeTree (makeTree (1, [makeTree (2, []), makeTree (3, [])]))
      3
```

`sizeTree` mengaplikasikan `sizeForest`, dan `sizeForest` mengaplikasikan `sizeTree`. Inilah **rekursi tidak langsung** dari Bab 6, yang di sana diperkenalkan lewat `isEven` dan `isOdd` dengan catatan terbuka bahwa contohnya tidak berguna dan bentuknya baru akan diperlukan pada bab ini.

Alasan kemunculannya sekarang jelas. Type-nya sendiri saling merujuk: sebuah `Tree` menyebut `List of Tree`, dan sebuah `List of Tree` menyebut `Tree`. Fungsi yang mengikuti bentuk type-nya karenanya juga harus saling merujuk. Kedua kewajiban Bab 6 tetap terpenuhi dan kini datang dari type-nya: `sizeForest` mempunyai basis pada list kosong, dan setiap aplikasi bekerja atas struktur yang lebih kecil daripada semula.

Perhatikan pula bahwa `sizeForest` berbentuk lipatan. Dengan Bab 8, seluruh fungsi tersebut dapat diganti oleh satu baris:

```
sizeTree (T) : 1 + sumList (map (sizeTree, children (T)))
```

Rekursi tidak langsungnya hilang, dan yang tersisa hanyalah satu fungsi rekursif biasa. Hal ini tidak selalu mungkin, tetapi cukup sering mungkin sehingga pantas diperiksa setiap kali sepasang fungsi saling memanggil.

## Pohon Biner

Membatasi banyaknya anak menjadi paling banyak dua menghasilkan **pohon biner**, yang jauh lebih sering dipakai dan lebih mudah ditangani. Definisi rekursifnya kembali mempunyai bentuk yang sudah akrab sejak Bab 7:

- **Basis:** pohon kosong adalah sebuah pohon biner.
- **Rekurens:** sebuah upapohon kiri, sebuah nilai, dan sebuah upapohon kanan membentuk sebuah pohon biner.

```
TYPE BINARYTREE
```

```
DEFINISI TYPE
    type BinaryTree : Empty
                    | Node ⟨left : BinaryTree, info : Element, right : BinaryTree⟩
    { Sebuah BinaryTree adalah pohon kosong, atau sebuah simpul berisi info
      dengan upapohon kiri dan upapohon kanan }

DEFINISI DAN SPESIFIKASI SELEKTOR
    left  : Node → BinaryTree
    info  : Node → Element
    right : Node → BinaryTree
    { masing-masing memberikan komponen yang bersesuaian dari sebuah simpul }

DEFINISI DAN SPESIFIKASI KONSTRUKTOR
    makeEmptyTree : → BinaryTree
    { makeEmptyTree membentuk pohon kosong }

    makeNode : BinaryTree, Element, BinaryTree → BinaryTree
    { makeNode (L, e, R) membentuk pohon berakar bernilai e
      dengan upapohon kiri L dan upapohon kanan R }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isEmptyTree : BinaryTree → Boolean
    { isEmptyTree (T) bernilai true bila T adalah pohon kosong }

    isLeafNode : BinaryTree → Boolean
    { isLeafNode (T) bernilai true bila T mempunyai info tanpa upapohon.
      Prasyarat: not (isEmptyTree (T)) }
```

```
REALISASI
    isLeafNode (T) : isEmptyTree (left (T)) and isEmptyTree (right (T))
```

Berbeda dengan `Tree` pada bagian sebelumnya, type ini **mempunyai** alternatif kosong. Sekali lagi, perbedaannya bukan soal selera. Sebuah simpul biner mempunyai dua tempat yang keduanya harus terisi, sehingga "tidak mempunyai anak kiri" harus dapat dituliskan secara eksplisit. Tanpa `Empty`, type ini menuntut empat alternatif, yaitu daun, bercabang kiri saja, bercabang kanan saja, dan bercabang keduanya, dan setiap fungsi atasnya menuntut analisis empat kasus.

Akibatnya, basis kedua type ini berada di tempat yang berbeda. Pada `Tree`, basis rekurensnya adalah list anak yang kosong. Pada `BinaryTree`, basisnya adalah pohonnya sendiri yang kosong. Keduanya sah, dan pilihan di antara keduanya ditentukan oleh bentuk strukturnya, bukan oleh kebiasaan.

Dua fungsi dasar:

```
JUDUL   Ukuran dan Tinggi Pohon Biner
```

```
DEFINISI DAN SPESIFIKASI
    size : BinaryTree → Integer ≥ 0
    { size (T) adalah banyaknya simpul pada T, nol bila T kosong }
    { Basis-0:  isEmptyTree (T)       → 0
      Rekurens: not (isEmptyTree (T)) → 1 + size (left (T)) + size (right (T)) }

    height : BinaryTree → Integer ≥ 0
    { height (T) adalah banyaknya simpul pada jalur terpanjang dari akar T
      sampai sebuah daun. height dari pohon kosong adalah nol, dan height
      dari pohon dengan satu simpul adalah satu }
    { Basis-0:  isEmptyTree (T)       → 0
      Rekurens: not (isEmptyTree (T)) → 1 + max2 (height (left (T)), height (right (T))) }
```

```
REALISASI
    size (T) :
        depend on T
            isEmptyTree (T)       : 0
            not (isEmptyTree (T)) : 1 + size (left (T)) + size (right (T))

    height (T) :
        depend on T
            isEmptyTree (T)       : 0
            not (isEmptyTree (T)) : 1 + max2 (height (left (T)), height (right (T)))
```

```
APLIKASI
    ⇒ size (makeEmptyTree)
      0
    ⇒ height (makeEmptyTree)
      0
    ⇒ size (makeNode (makeEmptyTree, 5, makeEmptyTree))
      1
    ⇒ height (makeNode (makeNode (makeEmptyTree, 3, makeEmptyTree), 5, makeEmptyTree))
      2
```

Kedua fungsi tersebut mengandung **dua** aplikasi rekursif dalam satu ekspresi, sebagaimana `fibonacci` pada Bab 6, sehingga penelusurannya berupa pohon dan bukan garis. Perbedaannya dengan `fibonacci`: di sini tidak ada pekerjaan yang terulang, karena upapohon kiri dan kanan memang berisi simpul yang berbeda.

Perhatikan bahwa nilai basis `height` adalah keputusan yang harus dinyatakan, bukan sesuatu yang terberi. Sebagian penulis menetapkan tinggi pohon dengan satu simpul sebagai nol dan bukan satu. Kedua pilihan sah, dan yang tidak sah adalah membiarkannya tidak tertulis. Spesifikasi di atas menyatakannya secara terbuka pada dua nilai batas sekaligus.

## Rancangan yang Sama dalam Paradigma Prosedural

Sekarang letakkan rancangan yang sama dalam notasi prosedural. Rincian notasinya berbeda-beda menurut bahan yang dipakai; yang penting di sini adalah bentuknya, bukan lambangnya.

```
KAMUS
    type Address : pointer to ElmtNode
    type ElmtNode : ⟨left : Address, info : Integer, right : Address⟩

    { Sebuah pohon diwakili oleh alamat akarnya.
      Pohon kosong diwakili oleh Nil }
```

```
function size (P : Address) → Integer
{ size (P) adalah banyaknya simpul pada pohon yang berakar di P }
ALGORITMA
    if P = Nil then
        → 0
    else
        → 1 + size (P↑.left) + size (P↑.right)
```

Perbandingannya:

| | Fungsional | Prosedural |
|---|---|---|
| Pohon kosong | sebuah **alternatif** dari type-nya, yaitu `Empty` | sebuah **nilai alamat** istimewa, yaitu `Nil` |
| Mencapai komponen | selektor `left (T)` | dereferensi `P↑.left` |
| Yang menjamin kasus kosong tertangani | type-nya; setiap alternatif harus dijawab | tidak ada yang menjamin |
| Membangun pohon | konstruktor menghasilkan nilai baru | alokasi memori dan penugasan |
| Mengubah pohon | menghasilkan pohon baru | mengubah simpul di tempatnya |
| Pohon lama sesudah perubahan | tetap ada dan tetap sah | tidak ada lagi |

Baris ketiga adalah perbedaan yang paling berakibat. Pada rancangan fungsional, `Empty` dan `Node` adalah dua alternatif dari satu type, sehingga analisis kasus atasnya lengkap dan disjoint dengan sendirinya sebagaimana dinyatakan Bab 5. Melupakan kasus kosong berarti melupakan salah satu alternatif, dan hal itu terlihat pada teksnya.

Pada rancangan prosedural, `Nil` bukan bagian dari type melainkan sebuah nilai yang kebetulan dipakai sebagai penanda. `P↑.left` ketika `P` bernilai `Nil` adalah kekeliruan yang tidak dicegah oleh apa pun dalam notasinya. Pemeriksaan `if P = Nil` harus ditulis karena penulisnya ingat, bukan karena ada yang menuntutnya.

## Apa yang Diperoleh dan Apa yang Hilang

Perbandingan di atas akan menyesatkan bila berhenti di situ, karena baris terakhir tabel bekerja ke dua arah.

- **Yang diperoleh rancangan fungsional.** Kasus kosong tidak dapat terlupakan tanpa terlihat. Basis dan konvergensi rekurensnya datang dari type-nya. Penelusuran di atas kertas dapat dipercaya, karena tidak ada simpul yang diam-diam berubah di tengah jalan. Sesudah sebuah penyisipan pun, pohon yang lama **tetap ada dan tetap sah**, sehingga dua versi sebuah struktur dapat hidup berdampingan tanpa disalin. Sifat terakhir tersebut disebut *persistence*, dan merupakan salah satu keunggulan struktur data fungsional yang paling nyata.

- **Yang hilang.** Sebuah penyisipan fungsional membangun ulang seluruh simpul di sepanjang jalur dari akar sampai tempat penyisipan. Untuk pohon setinggi *h*, hal itu berarti *h* simpul baru dibentuk, sedangkan rancangan prosedural cukup mengubah satu pengait. Simpul lama memang masih dipakai bersama oleh kedua versi, sehingga yang dibangun ulang hanyalah jalurnya dan bukan seluruh pohon, tetapi pekerjaan tambahan itu nyata. Bila sebuah struktur diubah berjuta kali dan versi lamanya tidak pernah dibutuhkan, rancangan prosedural lebih hemat.

- **Kesimpulan yang jujur.** Pertanyaan "paradigma mana yang lebih baik" tidak terjawab oleh perbandingan ini, dan memang bukan pertanyaan yang terumuskan dengan baik. Yang terjawab adalah pertanyaan lain: *apa yang dipermudah dan apa yang dipersulit oleh masing-masing.* Bab 1 sudah menyatakan bahwa satu paradigma tidak akan cocok untuk semua kelas persoalan, dan bahwa fanatisme terhadap satu paradigma mempersempit wawasan. Pohon adalah tempat pernyataan tersebut dapat diperiksa sendiri alih-alih diterima begitu saja.

## Penelusuran Pohon

**Menelusuri** sebuah pohon berarti mengunjungi seluruh simpulnya dalam suatu urutan, dan menghasilkan nilainya sebagai sebuah list. Ada tiga urutan yang lazim, dan ketiganya berbeda hanya pada **kapan akar dikunjungi** relatif terhadap kedua upapohonnya.

```
JUDUL   Tiga Penelusuran Pohon Biner
```

```
DEFINISI DAN SPESIFIKASI
    preorder  : BinaryTree → List of Element
    { preorder (T) adalah list nilai T dengan urutan: akar, kiri, kanan }

    inorder   : BinaryTree → List of Element
    { inorder (T) adalah list nilai T dengan urutan: kiri, akar, kanan }

    postorder : BinaryTree → List of Element
    { postorder (T) adalah list nilai T dengan urutan: kiri, kanan, akar }
    { Fungsi antara yang dipakai: append }
```

```
REALISASI
    preorder (T) :
        depend on T
            isEmptyTree (T)       : makeNil
            not (isEmptyTree (T)) : makeCons (info (T),
                                        append (preorder (left (T)),
                                                preorder (right (T))))

    inorder (T) :
        depend on T
            isEmptyTree (T)       : makeNil
            not (isEmptyTree (T)) : append (inorder (left (T)),
                                        makeCons (info (T), inorder (right (T))))

    postorder (T) :
        depend on T
            isEmptyTree (T)       : makeNil
            not (isEmptyTree (T)) : append (postorder (left (T)),
                                        append (postorder (right (T)),
                                                makeCons (info (T), makeNil)))
```

`append` berasal dari Latihan Menulis Bab 7. Ketiga realisasi tersebut memakai kembali seluruh mesin list tanpa menambah apa pun.

Perhatikan bahwa ketiganya mempunyai struktur rekurens yang **persis sama**, dan berbeda hanya pada tempat `info (T)` disisipkan. Kekembaran ini adalah kekembaran keempat sepanjang diktat ini, sesudah tiga pasang pada Bab 7, dan pembaca yang sudah mengerjakan Bab 8 kini mempunyai perkakas untuk menanganinya.

## Pohon Ekspresi

Bab 3 menuliskan sebuah tabel yang membandingkan tiga bentuk penulisan ekspresi, lalu menyatakan bahwa bentuk prefix dan postfix akan muncul kembali sebagai representasi pohon ekspresi. Janji tersebut dibayar di sini.

Sebuah ekspresi aritmatika berbentuk pohon: setiap operator biner mempunyai dua operan, dan setiap operan dapat berupa bilangan atau ekspresi lain.

```
TYPE EXPRESSION
```

```
DEFINISI TYPE
    type Expression : Operand ⟨value : Integer⟩
                    | Operation ⟨left : Expression, operator : Character,
                                 right : Expression⟩
    { Sebuah Expression adalah sebuah bilangan, atau sebuah operator
      biner beserta kedua operannya yang juga Expression }

DEFINISI DAN SPESIFIKASI PREDIKAT
    isOperand : Expression → Boolean
    { isOperand (E) bernilai true bila E adalah sebuah bilangan }

DEFINISI DAN SPESIFIKASI OPERATOR LAIN
    applyOp : Character, Integer, Integer → Integer
    { applyOp (c, a, b) adalah hasil operasi c terhadap a dan b.
      Prasyarat: c adalah salah satu dari '+', '-', '*';
      operator lain dapat ditambahkan dengan cara yang sama }

    evaluate : Expression → Integer
    { evaluate (E) adalah nilai ekspresi E }
```

```
REALISASI
    applyOp (c, a, b) :
        depend on c
            c = '+' : a + b
            c = '-' : a - b
            c = '*' : a * b

    evaluate (E) :
        depend on E
            isOperand (E)       : value (E)
            not (isOperand (E)) : applyOp (operator (E),
                                           evaluate (left (E)),
                                           evaluate (right (E)))
```

```
APLIKASI
    ⇒ evaluate (Operand ⟨7⟩)
      7
    ⇒ evaluate (Operation ⟨Operation ⟨Operand ⟨3⟩, '+', Operand ⟨4⟩⟩,
                           '*', Operand ⟨5⟩⟩)
      35
```

Enam baris `evaluate` mengerjakan sesuatu yang tampak besar. Kuncinya: struktur pohonnya sudah menyatakan seluruh pengelompokan, sehingga presedensi dan kurung dari Bab 3 tidak berperan sama sekali. Presedensi adalah aturan untuk **membaca teks**, bukan bagian dari ekspresinya.

### Tiga penelusuran, tiga notasi

Sekarang telusuri pohon ekspresi `(3 + 4) * 5` dengan ketiga cara di atas:

| Penelusuran | Hasil | Bentuk pada Bab 3 |
|---|---|---|
| preorder | `* + 3 4 5` | prefix |
| inorder | `3 + 4 * 5` | infix |
| postorder | `3 4 + 5 *` | postfix |

Bandingkan baris pertama dan ketiga dengan tabel Bab 3. Keduanya sama persis, sampai ke urutan lambangnya.

Baris kedua **tidak** sama, dan justru di situlah letak pelajarannya. Bab 3 menuliskan bentuk infix sebagai `(3 + 4) * 5`, lengkap dengan kurung. Penelusuran inorder menghasilkan `3 + 4 * 5`, tanpa kurung, dan ekspresi tersebut bernilai `23` alih-alih `35`.

Kesimpulannya tegas. Dari hasil preorder maupun postorder, pohon semula dapat dibangun kembali dengan tepat. Dari hasil inorder, pohon semula **tidak** dapat dibangun kembali, kecuali kurung ikut dituliskan. Inilah alasan sesungguhnya di balik pernyataan Bab 3 bahwa bentuk prefix dan postfix tidak memerlukan kurung maupun presedensi, sehingga lebih mudah diproses mesin. Kedua bentuk tersebut menyimpan strukturnya; bentuk infix membuangnya dan menggantinya dengan aturan pembacaan.

## Pohon Terurut

Sebuah **pohon terurut** (*binary search tree*) adalah pohon biner dengan sebuah invarian: bagi setiap simpul, seluruh nilai pada upapohon kirinya lebih kecil daripada nilai simpul tersebut, dan seluruh nilai pada upapohon kanannya lebih besar.

Invarian ini adalah invarian dalam arti yang persis sama dengan invarian `Fraction` pada Bab 5 dan invarian keterurutan pada `insertSorted` di Bab 7. Tidak ada yang menjaminnya selain disiplin, dan setiap operator yang menghasilkan pohon baru wajib mempertahankannya.

```
JUDUL   Penyisipan dan Pencarian pada Pohon Terurut
```

```
DEFINISI DAN SPESIFIKASI
    insertTree : Integer, BinaryTree → BinaryTree
    { insertTree (x, T) adalah pohon terurut yang berisi seluruh nilai T
      beserta x. Bila x sudah ada pada T, hasilnya adalah T.
      Prasyarat: T adalah pohon terurut }

    isMemberTree : Integer, BinaryTree → Boolean
    { isMemberTree (x, T) bernilai true bila x adalah salah satu nilai pada T.
      Prasyarat: T adalah pohon terurut }
```

```
REALISASI
    insertTree (x, T) :
        depend on T
            isEmptyTree (T)                              : makeNode (makeEmptyTree, x, makeEmptyTree)
            not (isEmptyTree (T)) and then (x < info (T)) : makeNode (insertTree (x, left (T)), info (T), right (T))
            not (isEmptyTree (T)) and then (x > info (T)) : makeNode (left (T), info (T), insertTree (x, right (T)))
            not (isEmptyTree (T)) and then (x = info (T)) : T

    isMemberTree (x, T) :
        depend on T
            isEmptyTree (T)                              : false
            not (isEmptyTree (T)) and then (x = info (T)) : true
            not (isEmptyTree (T)) and then (x < info (T)) : isMemberTree (x, left (T))
            not (isEmptyTree (T)) and then (x > info (T)) : isMemberTree (x, right (T))
```

```
APLIKASI
    ⇒ isMemberTree (5, makeEmptyTree)
      false
    ⇒ inorder (insertTree (3, insertTree (7, insertTree (5, makeEmptyTree))))
      [3, 5, 7]
```

Tiga hal patut diperhatikan.

- **Bandingkan dengan `insertSorted` pada Bab 7.** Keduanya menyisipkan sebuah nilai ke dalam struktur terurut, keduanya bersandar pada invarian yang dinyatakan sebagai prasyarat, dan keduanya membangun ulang bagian struktur yang dilewatinya sambil memakai kembali sisanya utuh. Yang berbeda hanyalah strukturnya. `insertSorted` melewati rata-rata separuh list; `insertTree` melewati sepanjang tingginya saja. Rancangan yang sama, ditempatkan pada struktur yang berbeda, memberikan hasil yang berbeda.

- **`isMemberTree` membuang separuh pohon pada setiap langkah.** Bandingkan dengan `isMember` atas list pada Bab 7, yang harus memeriksa elemen satu per satu. Inilah imbalan dari invariannya, dan sebagaimana Bab 7 sudah menyatakan tentang himpunan, invarian tidak pernah gratis: harganya dibayar oleh setiap penyisipan yang harus menjaganya.

- **Penelusuran inorder sebuah pohon terurut menghasilkan list yang terurut menaik.** Aplikasi terakhir di atas memperlihatkannya. Hal ini bukan kebetulan, melainkan akibat langsung dari invariannya, dan merupakan salah satu cara mengurutkan sebuah kumpulan nilai.

### Ketika pohon terurut kehilangan keunggulannya

Sisipkan `1`, lalu `2`, lalu `3`, lalu `4`, lalu `5` ke dalam pohon kosong. Karena setiap nilai lebih besar daripada seluruh nilai sebelumnya, setiap penyisipan jatuh ke upapohon kanan, dan hasilnya adalah pohon setinggi lima tanpa satu pun cabang kiri. Pohon tersebut adalah sebuah list yang ditulis dengan cara yang lebih rumit, dan seluruh keunggulan `isMemberTree` lenyap.

Ada cara merancang pohon yang menjaga tingginya tetap kecil apa pun urutan penyisipannya, dan cara tersebut merupakan pokok bahasan tersendiri yang termasuk dalam materi struktur data dan algoritma. Pembaca yang tertarik pada versi fungsionalnya dirujuk ke [8]. Yang perlu dibawa dari sini hanyalah kesadaran bahwa keunggulan sebuah struktur data selalu bersyarat, dan bahwa syaratnya pantas ditanyakan sebelum struktur itu dipilih.

## Kesalahan yang Sering Terjadi

| Kesalahan | Gejala | Cara memeriksa |
|---|---|---|
| Kasus pohon kosong terlupa | tidak terdefinisi pada pohon kosong atau pada daun | apakah kedua alternatif type-nya dijawab? |
| Selektor dipakai atas pohon kosong | tidak terdefinisi | apakah `left`, `info`, `right` hanya dipakai sesudah `not (isEmptyTree (T))`? |
| `and` dipakai padahal `and then` diperlukan | tidak terdefinisi pada pohon kosong | adakah kondisi yang memakai `info (T)`? |
| Rekursi hanya pada satu sisi | sebagian simpul tidak pernah dikunjungi | apakah kedua upapohon disebut? |
| Nilai basis `height` tidak dinyatakan | keliru tepat satu pada seluruh hasil | apakah tinggi pohon kosong dan tinggi pohon satu simpul tertulis pada spesifikasi? |
| Invarian pohon terurut tidak dijaga | pencarian gagal menemukan nilai yang ada | apakah setiap pembentukan melalui `insertTree`? |
| Upapohon yang tergeser hilang | sebagian nilai lenyap sesudah penyisipan | pada `insertTree`, apakah sisi yang tidak direkursi diteruskan utuh? |

## Ringkasan Padanan

```haskell
data BinaryTree a = Empty
                  | Node (BinaryTree a) a (BinaryTree a)
                  deriving Show

size :: BinaryTree a -> Integer
-- size t adalah banyaknya simpul pada t
size Empty          = 0
size (Node l _ r)   = 1 + size l + size r

height :: BinaryTree a -> Integer
-- height t adalah tinggi t; nol bila t kosong
height Empty        = 0
height (Node l _ r) = 1 + max (height l) (height r)

inorder :: BinaryTree a -> [a]
-- inorder t adalah nilai t dengan urutan kiri, akar, kanan
inorder Empty        = []
inorder (Node l v r) = inorder l ++ [v] ++ inorder r
```

Dua hal yang layak dicatat, dan keduanya kecil karena bab ini memang hampir tidak memperkenalkan mesin baru.

- **Definisi type diterjemahkan hampir huruf demi huruf,** dan pola `Node l v r` mengerjakan empat hal sekaligus: memastikan pohonnya tidak kosong, lalu mengambil ketiga komponennya. Pengamatan yang sama sudah dibuat pada Bab 5 untuk `Shape` dan pada Bab 7 untuk list. Pada pohon, penghematannya paling terasa, karena tanpa pola tersebut ketiga selektor harus disebut berulang kali di dalam satu ekspresi.

- **`deriving Show` menghasilkan bentuk tercetak secara cuma-cuma,** dan bentuk tercetak tersebut ternyata adalah penelusuran preorder dengan nama konstruktor ikut dituliskan. Hal ini bukan kebetulan: bentuk preorder memang menyimpan seluruh struktur pohonnya, sebagaimana ditunjukkan pada bagian pohon ekspresi.

| Notasi fungsional | Haskell |
|---|---|
| `type BinaryTree : Empty \| Node ⟨left, info, right⟩` | `data BinaryTree a = Empty \| Node (BinaryTree a) a (BinaryTree a)` |
| `makeEmptyTree` | `Empty` |
| `makeNode (L, e, R)` | `Node l e r` |
| `isEmptyTree (T)` | pola `Empty` |
| `left (T)`, `info (T)`, `right (T)` | pola `(Node l v r)` |
| `append (A, B)` | `a ++ b` |
| `type Tree : Node ⟨info, children : List of Tree⟩` | `data Tree a = Node a [Tree a]` |
| invarian pohon terurut | *tidak ada padanan; hidup pada spesifikasi* |

## Latihan

**Membaca.** Untuk setiap realisasi berikut, tentukan definisinya, tuliskan spesifikasinya, sebutkan basis dan rekurensnya, lalu usulkan nama yang sesuai konvensi.

```
p (T) :
    depend on T
        isEmptyTree (T)       : 0
        not (isEmptyTree (T)) : if isLeafNode (T) then
                                    1
                                else
                                    p (left (T)) + p (right (T))

q (T) :
    depend on T
        isEmptyTree (T)       : makeEmptyTree
        not (isEmptyTree (T)) : makeNode (q (right (T)), info (T), q (left (T)))

r (T)  : 1 + rList (children (T))
rList (F) :
    depend on F
        isEmpty (F)       : 0
        not (isEmpty (F)) : max2 (r (head (F)), rList (tail (F)))
```

Untuk `q`, gambarkan hasilnya atas sebuah pohon kecil, lalu jawab: apakah `q (q (T))` sama dengan `T`? Untuk pasangan `r` dan `rList`, jelaskan mengapa keduanya harus saling memanggil, dan apakah pasangan tersebut dapat digabung menjadi satu fungsi dengan perkakas Bab 8.

**Memperbaiki.** Fungsi berikut seharusnya menyisipkan sebuah nilai ke dalam pohon terurut.

```
REALISASI
    insertTree (x, T) :
        depend on T
            isEmptyTree (T)                              : makeNode (makeEmptyTree, x, makeEmptyTree)
            not (isEmptyTree (T)) and then (x < info (T)) : makeNode (insertTree (x, left (T)), info (T), makeEmptyTree)
            not (isEmptyTree (T)) and then (x ≥ info (T)) : makeNode (makeEmptyTree, info (T), insertTree (x, right (T)))
```

```
APLIKASI
    ⇒ inorder (insertTree (5, makeEmptyTree))
      [5]
    ⇒ inorder (insertTree (3, insertTree (5, makeEmptyTree)))
      [3, 5]
    ⇒ inorder (insertTree (7, insertTree (5, makeEmptyTree)))
      [5, 7]
```

1. Ketiga contoh aplikasi menghasilkan jawaban yang benar. Temukan masukan yang menghasilkan jawaban keliru, dan sebutkan nilai mana yang hilang.
2. Kesalahannya terletak pada dua tempat yang sama bentuknya. Sebutkan keduanya, dan jelaskan apa yang seharusnya dituliskan di sana.
3. Mengapa ketiga contoh aplikasi tidak menampakkannya? Apa ciri masukan yang diperlukan untuk menampakkan kesalahan semacam ini?
4. Kasus ketiga memakai `x ≥ info (T)` dan bukan `x > info (T)`. Jelaskan akibatnya terhadap nilai kembar, dan bandingkan dengan spesifikasi `insertTree` pada bab ini.
5. Perbaiki, lalu tambahkan contoh aplikasi sehingga setiap kasus terwakili.

**Menulis.** Tuliskan keempat bagian secara lengkap. Sebutkan basisnya, nyatakan prasyarat bila ada, dan pilih contoh aplikasi yang mencakup pohon kosong, pohon satu simpul, dan pohon yang lebih besar.

1. `countLeaves (T)`, yang mencacah daun sebuah pohon biner.
2. `maxTree (T)`, yang menghasilkan nilai terbesar pada sebuah pohon biner **sembarang**, bukan pohon terurut. Basis apa yang Anda pilih, dan mengapa? Bandingkan dengan `maxList` pada Bab 7.
3. `mapTree (f, T)`, yang menerapkan `f` pada setiap nilai sebuah pohon. Bandingkan realisasinya dengan `map` pada Bab 8, dan sebutkan apa yang dijamin oleh keduanya.
4. `depthOf (x, T)` atas pohon terurut, yang menghasilkan banyaknya simpul yang dilewati untuk mencapai `x`. Nyatakan dengan jelas apa yang dihasilkan bila `x` tidak ada.
5. `isBalanced (T)`, yang bernilai true bila tinggi kedua upapohon setiap simpul berbeda paling banyak satu. Berapa kali `height` diaplikasikan oleh realisasi Anda, dan dapatkah jumlahnya dikurangi?

**Menelusuri.**

1. Telusuri `evaluate` atas pohon ekspresi `(3 + 4) * 5` secara lengkap. Pada langkah keberapa `applyOp` yang pertama dapat direduksi, dan mengapa tidak lebih awal?
2. Gambarkan pohon ekspresi bagi `2 * 3 + 4 * 5`, lalu tuliskan ketiga penelusurannya. Periksa bahwa hasil postorder Anda dapat dikembalikan menjadi pohon semula, dan bahwa hasil inorder tidak.
3. Telusuri `insertTree (4, T)` dengan `T` berisi `5`, `3`, dan `7`. Sebutkan simpul mana yang dibentuk baru dan simpul mana yang dipakai bersama dengan pohon semula.
4. Sisipkan `1, 2, 3, 4, 5` berurutan ke dalam pohon kosong, lalu sisipkan kelima nilai yang sama dengan urutan `3, 1, 5, 2, 4`. Gambarkan kedua hasilnya dan bandingkan tingginya. Berapa langkah yang diperlukan `isMemberTree (5, T)` pada masing-masing?

**Merancang.** Tiga pertanyaan, ketiganya tanpa jawaban tunggal yang benar.

1. Penyisipan fungsional membangun ulang jalur dari akar sampai tempat penyisipan, sedangkan penyisipan prosedural mengubah satu pengait di tempatnya. Sebutkan satu jenis persoalan yang jelas diuntungkan oleh rancangan fungsional, dan satu yang jelas diuntungkan oleh rancangan prosedural. Untuk masing-masing, sebutkan sifat persoalannya yang menentukan.
2. Ketiga penelusuran pada bab ini berbeda hanya pada tempat `info (T)` disisipkan, dan `mapTree` pada Latihan Menulis berbentuk seperti `map`. Rancanglah sebuah `foldTree`, yaitu lipatan atas pohon. Apa yang harus menjadi nilai basisnya, berapa parameter yang dibutuhkan penggabungnya, dan dapatkah `size`, `height`, serta ketiga penelusuran itu seluruhnya dinyatakan dengannya?
3. Struktur yang sama dirancang dua kali pada bab ini. Setelah mengerjakan seluruh latihan di atas, jawablah: untuk persoalan seperti apa Anda akan memilih rancangan yang mana, dan atas dasar apa? Kemudian jawab pertanyaan yang lebih tajam: apakah "paradigma mana yang lebih baik" merupakan pertanyaan yang terumuskan dengan baik? Bila menurut Anda tidak, rumuskan penggantinya.
