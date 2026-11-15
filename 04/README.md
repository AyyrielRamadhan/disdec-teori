# Ringkasan Materi Minggu ke-4: Konsistensi dan Replikasi pada Sistem Terdistribusi

Mata kuliah: Sistem Terdistribusi dan Terdesentralisasi<br>
Topik: Konsistensi dan Replikasi pada Sistem Terdistribusi<br>
Dosen: Dr. Bambang Purnomosidi D. P.<br>
Kode: FIF25002T | SKS: 3 | Prodi: Informatika, FTI UTDI

---

## Tujuan pembelajaran

- Memahami dan mampu menjelaskan konsep konsistensi dalam sistem terdistribusi
- Mampu menjelaskan berbagai model konsistensi
- Mampu menjelaskan konsep replikasi data
- Mengetahui dan mampu menjelaskan berbagai protokol dan strategi replikasi
- Mampu melakukan analisis studi kasus replikasi pada sistem nyata yang banyak digunakan di industri

---

## 1. Konsistensi itu sebenarnya apa

<p align="justify">Bayangkan sebuah aplikasi yang datanya tidak disimpan di satu tempat saja. Karena jumlah pengguna bertambah dan satu server tidak lagi cukup, salinan data diletakkan di beberapa server yang lokasi fisiknya berbeda. Satu di Jakarta, satu di Yogyakarta, satu lagi di Denpasar. Selama semuanya sehat dan sepi, tidak ada masalah. Masalah muncul begitu ada perubahan data.</p>

<p align="justify"><strong>Konsistensi adalah jaminan bahwa semua node dalam sistem memiliki data yang sama, atau setidaknya tampak sama, pada waktu tertentu.</strong> Jaminan ini harus tetap berlaku walaupun data tersebar di banyak tempat dan bisa diubah secara bersamaan oleh pengguna atau proses yang berbeda.</p>

<p align="justify">Kalimat "atau setidaknya tampak sama" itu penting. Ada sistem yang benar-benar menyamakan isi semua salinan sebelum menjawab pengguna, ada juga sistem yang membiarkan salinan berbeda sebentar asal dari sudut pandang pengguna tidak terlihat aneh. Keduanya disebut konsisten, hanya levelnya berbeda. Persoalan ini yang akan dibedah sepanjang materi minggu ini.</p>

<p align="justify">Intinya sederhana: kalau aplikasi mengubah data di satu server, sistem harus memastikan server lain <em>akhirnya</em> punya data yang sama. Kata "akhirnya" inilah yang nanti menjadi bahan perdebatan panjang, karena jarak antara "saat ini" dan "akhirnya" bisa sangat terasa bagi pengguna.</p>

---

## 2. Mengapa konsistensi penting

<p align="justify">Kalau konsistensi diabaikan, pengguna bisa melihat versi data yang berbeda hanya karena dia kebetulan membaca dari server yang berbeda. Pada aplikasi media sosial, mungkin itu tidak terlalu terasa. Pada aplikasi keuangan, itu bencana.</p>

<p align="justify">Slide memakai contoh transfer bank yang cukup menggambarkan masalahnya. Seorang nasabah memindahkan uang dari rekening A ke rekening B. Prosesnya terdiri atas dua langkah: mengurangi saldo A, lalu menambah saldo B. Keduanya terjadi di server yang berbeda. Server 1 berhasil mengurangi saldo A, tetapi server 2 belum sempat menambah saldo B.</p>

- Server 1 sudah mengurangi saldo A
- Server 2 belum menambah saldo B
- Nasabah tidak bisa bertransaksi dari rekening B karena saldonya belum bertambah, sehingga dianggap tidak mencukupi

<p align="justify"><strong><em>Uangnya tidak hilang, tetapi untuk sementara waktu uang itu tidak ada di mana pun. Dari sudut pandang nasabah, sistem tidak konsisten.</em></strong></p>

<p align="justify">Akar masalahnya adalah data yang tersebar di banyak node. Selama ada lebih dari satu salinan, selalu ada peluang satu node sudah berubah sementara node lain belum. Tujuan konsistensi adalah memastikan pengguna melihat data yang benar dan <em>up-to-date</em>, bukan versi acak tergantung server mana yang menjawab permintaannya.</p>

<p align="justify">Sayangnya tujuan itu tidak gratis. Ada tiga hal yang harus diperebutkan sekaligus, yaitu <em>Consistency</em>, <em>Availability</em>, dan <em>Partition Tolerance</em>. Perdebatan itulah yang dirumuskan dalam Teorema CAP.</p>

---

## 3. Teorema CAP

<p align="justify">Teorema CAP menyatakan bahwa pada sistem terdistribusi kita tidak bisa mendapatkan ketiganya sekaligus. Slide menekankan satu premis yang sering dilupakan: <strong>partisi jaringan adalah hal yang wajar.</strong> Sebagus apa pun rancangan jaringan, kabel bisa putus, <em>switch</em> bisa mati, dan pusat data bisa kehilangan koneksi. Artinya, kondisi ideal CAP dalam praktik nyata hampir tidak mungkin dipertahankan terus-menerus.</p>

<p align="justify">Karena partisi pasti terjadi, pilihan sebenarnya menyusut menjadi dua: saat partisi terjadi, apakah sistem memilih tetap melayani atau memilih tetap konsisten.</p>

<p align="center">Tabel 1: Tiga sifat dalam Teorema CAP</p>

<table align="center">
  <thead>
    <tr>
      <th>Sifat</th>
      <th>Makna</th>
      <th>Konsekuensi bila diutamakan</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><em>Consistency</em> (C)</td>
      <td>Data konsisten, semua node mempunyai data yang sama</td>
      <td>Jawaban selalu versi terbaru, tetapi sebagian permintaan harus ditolak saat ada gangguan</td>
    </tr>
    <tr>
      <td><em>Availability</em> (A)</td>
      <td>Data tersedia saat pengguna memerlukan</td>
      <td>Permintaan selalu dijawab, tetapi jawabannya bisa berasal dari salinan yang belum diperbarui</td>
    </tr>
    <tr>
      <td><em>Partition Tolerance</em> (P)</td>
      <td>Sistem tetap berjalan meskipun satu atau beberapa komponen jaringan gagal atau terputus</td>
      <td>Wajib dipenuhi; tanpa ini sistem akan mati total ketika koneksi antar lokasi terputus</td>
    </tr>
  </tbody>
</table>

<p align="justify">Contoh yang dipakai adalah aplikasi e-Commerce dengan dua server. Server A melayani wilayah barat Indonesia, Server B melayani wilayah timur. Suatu ketika jaringan antara dua wilayah terputus. Stok barang di kedua sisi sekarang tidak bisa disamakan secara langsung, dan sistem harus memilih.</p>

- Memilih <strong>Availability</strong> (AP): pengguna di kedua wilayah tetap bisa berbelanja, tetapi stok bisa sementara tidak sinkron. Barang yang sama berpotensi terjual dua kali
- Memilih <strong>Consistency</strong> (CP): sistem menolak transaksi sampai jaringan pulih, sehingga data stok tetap konsisten, tetapi ketersediaan layanan dikorbankan

<p align="justify"><strong><em>Tidak ada jawaban yang benar untuk semua kasus. Yang ada adalah pilihan sesuai nilai bisnis: kerugian apa yang lebih bisa ditanggung, kehilangan penjualan sementara atau data stok yang salah.</em></strong></p>

---

## 4. Model konsistensi: peta besarnya

<p align="justify">Model konsistensi adalah aturan atau jaminan tentang bagaimana dan kapan perubahan data pada sistem terdistribusi terlihat oleh node atau klien lain. Setiap model pada dasarnya menjawab satu pertanyaan yang sama: <em>seberapa cepat semua salinan data menjadi sama setelah ada pembaruan?</em></p>

<p align="justify">Jawaban atas pertanyaan itu membentuk sebuah spektrum. Di ujung atas ada model yang paling ketat dan paling mahal. Di ujung bawah ada model yang paling longgar dan paling murah. Slide menampilkannya sebagai tangga dari <em>Strong</em> menuju <em>Weak</em>.</p>

```
                    Strong
                       |
                 Linearizable
                       |
                   Sequential
                       |
                     Causal
                       |
                Read-your-writes
                       |
                 Monotonic read
                       |
                    Eventual
                       |
                     Weak
```

<p align="justify">Semakin ke atas, pengguna semakin yakin bahwa yang dibacanya adalah data terbaru, tetapi ongkos koordinasinya semakin besar. Semakin ke bawah, sistem semakin cepat dan semakin jarang menolak permintaan, tetapi pengguna harus menerima kemungkinan melihat data yang belum mutakhir. Enam model pada tangga itu, ditambah satu kategori besar di puncaknya, dibahas satu per satu berikut ini.</p>

---

## 5. Tujuh model konsistensi

### 5.1 Strong consistency

<p align="justify">Ini model yang paling mudah dijelaskan dan paling sulit diwujudkan. Setelah data diperbarui, semua node langsung melihat versi terbaru. Setiap pembacaan yang terjadi setelah penulisan pasti mendapatkan nilai yang sama, tidak peduli node mana yang menjawab.</p>

<p align="justify">Untuk mencapainya, sistem biasanya memakai protokol seperti <em>Two-Phase Commit</em> atau Paxos, yang pada intinya membuat semua pihak menyepakati satu nilai sebelum nilai itu dianggap sah. Contoh yang paling jelas adalah sistem perbankan: ketika saldo rekening berubah, semua server harus segera tahu nilai terbarunya. Tidak ada toleransi untuk saldo yang berbeda-beda tergantung cabang.</p>

### 5.2 Eventual consistency

<p align="justify">Model ini mengambil posisi sebaliknya. Data tidak dijamin langsung sama di semua node, tetapi <em>akhirnya</em> akan sama jika tidak ada pembaruan baru dalam jangka waktu tertentu. Tidak ada janji tentang kapan tepatnya, hanya janji bahwa ketidaksamaan itu bersifat sementara.</p>

<p align="justify">Pendekatan ini cocok untuk sistem yang mengutamakan ketersediaan dan kecepatan, misalnya Amazon DynamoDB dan Apache Cassandra. Pada kedua sistem itu, pembaruan mungkin terlambat menyebar ke replika lain, tetapi setelah beberapa waktu semuanya akan konvergen ke nilai yang sama. Model ini dikenal dengan istilah <strong>BASE</strong>.</p>

### 5.3 BASE dan hubungannya dengan ACID

<p align="justify">BASE adalah akronim dari <em>Basically Available</em>, <em>Soft State</em>, dan <em>Eventual Consistency</em>. Istilah ini sengaja diposisikan sebagai lawan dari ACID yang biasa dipakai basis data relasional.</p>

<p align="center">Tabel 2: Tiga komponen BASE</p>

<table align="center">
  <thead>
    <tr>
      <th>Komponen</th>
      <th>Arti</th>
      <th>Implikasi praktis</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><em>Basically Available</em></td>
      <td>Memastikan ketersediaan, data tetap dapat dibaca dan ditulis</td>
      <td>Tetap melayani bahkan saat terjadi kegagalan sebagian atau pemisahan jaringan</td>
    </tr>
    <tr>
      <td><em>Soft State</em></td>
      <td>Replika tidak harus konsisten sepanjang waktu</td>
      <td>Keadaan sistem boleh berubah seiring waktu, bahkan tanpa masukan baru, karena proses sinkronisasi berjalan di latar belakang</td>
    </tr>
    <tr>
      <td><em>Eventual Consistency</em></td>
      <td>Data akan konsisten di semua node jika tidak ada pembaruan setelah sekian waktu</td>
      <td>Pengguna mungkin melihat data lama untuk sementara, tetapi sistem akan mengejar ketertinggalan</td>
    </tr>
  </tbody>
</table>

<p align="justify">Cara membacanya: ACID menuntut semua pihak sepakat sebelum transaksi dianggap selesai, sedangkan BASE menerima keadaan antara. Kalau diibaratkan, ACID seperti rapat yang menunggu semua peserta hadir sebelum keputusan diambil, sementara BASE seperti grup percakapan yang tetap berjalan walau ada anggota yang belum membaca pesan.</p>

### 5.4 Causal consistency

<p align="justify">Model ini berfokus pada hubungan sebab akibat, bukan pada waktu. Jika satu operasi bergantung pada operasi lain, urutan keduanya harus dijaga. Operasi yang tidak saling berhubungan boleh dilihat dengan urutan berbeda oleh node yang berbeda.</p>

<p align="justify">Contohnya media sosial. Seseorang membuat posting, lalu orang lain memberi komentar. Komentar itu secara logis bergantung pada posting, jadi komentar tidak boleh muncul sebelum posting. Sebaliknya, dua komentar dari dua pengguna berbeda yang sama-sama menanggapi posting tersebut tidak saling bergantung, sehingga urutannya boleh berbeda di tiap perangkat.</p>

### 5.5 Read-your-writes consistency

<p align="justify">Model ini memberi jaminan yang sempit tetapi sangat terasa bagi pengguna: setelah seseorang menulis data, pembacaan berikutnya oleh orang yang sama akan melihat hasil tulisannya sendiri. Model ini tidak menjamin orang lain melihat hasil yang sama pada saat yang sama.</p>

<p align="justify">Kasusnya mudah ditemukan di aplikasi messaging. Saat pengguna mengirim pesan, pesan itu langsung muncul di layar pengirim meskipun penerima belum tentu sudah menerimanya. Pengirim tidak perlu menunggu sistem menyelesaikan replikasi ke semua server; dia hanya perlu diyakinkan bahwa tulisannya tidak hilang.</p>

### 5.6 Monotonic read consistency

<p align="justify">Jaminannya sederhana: kalau pengguna membaca data beberapa kali, dia tidak akan pernah melihat versi yang lebih lama dari versi yang sudah pernah dilihatnya. Waktu boleh bergerak maju, tidak boleh mundur.</p>

<p align="justify">Contohnya <em>timeline</em> media sosial. Setelah pengguna me-refresh dan melihat posting terbaru, refresh berikutnya tidak boleh menampilkan kondisi lama yang membuat posting tersebut hilang. Kalau itu terjadi, pengguna akan merasa ada yang salah, padahal penyebabnya mungkin dia sedang diarahkan ke replika yang tertinggal.</p>

### 5.7 Sequential consistency

<p align="justify">Pada model ini, semua operasi di sistem tampak dieksekusi dalam urutan yang sama untuk semua node, meskipun urutan itu tidak harus sesuai waktu nyata. Urutan penulisan penting, tetapi tidak perlu langsung sinkron.</p>

<p align="justify">Contohnya kembali ke media sosial. Semua pengguna melihat urutan posting yang sama, walaupun ada sedikit jeda antara posting dibuat dan saat posting itu terlihat. Yang dijaga adalah kesepakatan urutan, bukan ketepatan waktu.</p>

### 5.8 Linearizability

<p align="justify">Ini varian paling kuat dari <em>strong consistency</em>. Operasi tampak seolah-olah dilakukan secara atomik dan langsung, dengan urutan yang sama bagi semua node. Setelah sebuah penulisan selesai, semua pembacaan berikutnya oleh klien mana pun mencerminkan penulisan tersebut, atau penulisan sesudahnya. Efeknya, sistem terdistribusi tampak seperti satu node atomik tunggal yang menyediakan pengurutan operasi global secara <em>real-time</em>.</p>

<p align="justify">Model ini dipakai di sistem yang tidak boleh salah soal urutan, seperti etcd, ZooKeeper, dan Spanner. Contoh konkretnya adalah pengecekan keunikan ID: dua pengguna tidak boleh berhasil mendaftarkan <em>username</em> yang sama secara bersamaan. Tanpa pengurutan global, dua permintaan yang datang hampir bersamaan bisa sama-sama lolos pengecekan.</p>

---

## 6. Trade-off latensi dengan konsistensi

<p align="justify">Sampai di sini muncul pertanyaan yang wajar: kalau konsistensi kuat itu paling aman, mengapa tidak semua sistem memakainya? Jawabannya ada pada latensi. Ini kompromi mendasar dalam sistem terdistribusi: semakin tinggi tingkat konsistensi yang ingin dijamin, biasanya semakin besar latensi yang harus ditanggung pengguna.</p>

- Konsistensi naik, latensi naik
- Konsistensi turun, latensi turun

<p align="justify">Penjelasannya ada di apa yang ditunggu sistem sebelum menjawab. Pada konsistensi tinggi, sistem memastikan semua node memiliki data yang sama sebelum memberi respons. Artinya sistem menunggu sinkronisasi antar server selesai lebih dulu, dan itu berarti komunikasi lintas jaringan yang waktunya tidak bisa dihilangkan.</p>

<p align="justify">Pada konsistensi lemah, sistem langsung memberi respons tanpa menunggu semua node sinkron. Data bisa belum sama di semua tempat untuk sementara waktu, tetapi pengguna tidak perlu menunggu. Latensinya rendah, dengan risiko data yang dibaca belum tentu yang terbaru.</p>

<p align="center">Tabel 3: Perbandingan dampak strong dan eventual consistency</p>

<table align="center">
  <thead>
    <tr>
      <th>Aspek</th>
      <th><em>Strong consistency</em></th>
      <th><em>Eventual consistency</em></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Waktu memberi respons</td>
      <td>Setelah semua node sinkron</td>
      <td>Segera, tanpa menunggu sinkronisasi</td>
    </tr>
    <tr>
      <td>Latensi</td>
      <td>Tinggi, karena ada komunikasi lintas jaringan</td>
      <td>Rendah</td>
    </tr>
    <tr>
      <td>Risiko data</td>
      <td>Hampir tidak ada pembacaan data basi</td>
      <td>Ada kemungkinan membaca data yang belum terbaru</td>
    </tr>
    <tr>
      <td>Cocok untuk</td>
      <td>Transaksi keuangan, pengecekan keunikan, koordinasi klaster</td>
      <td><em>Feed</em>, penghitung akses, katalog produk, keranjang belanja</td>
    </tr>
  </tbody>
</table>

<p align="justify">Slide memberi contoh dua pusat data: Jakarta dan Yogyakarta. Seorang pengguna mengubah data di Jakarta, lalu ada permintaan pembacaan. Jika sistem memilih <em>strong consistency</em>, ia menunggu sampai Jakarta dan Yogyakarta sama persis sebelum menjawab. Datanya konsisten, tetapi responsnya lambat. Jika memilih <em>eventual consistency</em>, ia menjawab langsung dari Jakarta atau Yogyakarta, lalu mengirim pembaruan ke Yogyakarta belakangan. Responsnya cepat, tetapi data bisa berbeda untuk sementara.</p>

<p align="justify"><strong><em>Pilihannya bukan soal mana yang lebih baik, melainkan seberapa besar kerugian akibat data basi yang masih bisa diterima dibandingkan kerugian akibat pengguna menunggu terlalu lama.</em></strong></p>

---

## 7. Replikasi data

<p align="justify">Konsistensi tidak akan menjadi masalah kalau data hanya ada di satu tempat. Masalah itu muncul justru karena ada replikasi. <strong>Replikasi data adalah proses membuat dan memelihara salinan data yang sama di beberapa node atau server dalam sistem terdistribusi.</strong></p>

<p align="justify">Tujuannya agar sistem tetap tersedia, andal, dan cepat, meskipun ada kegagalan pada salah satu node. Contohnya, ada basis data utama di Jakarta. Untuk keperluan <em>high availability</em>, ditambahkan salinan basis data di Yogyakarta dan Denpasar. Proses menjaga agar semua salinan itu tetap sama adalah inti dari replikasi data.</p>

<p align="justify">Pola yang paling umum digambarkan di slide adalah satu <em>source</em> dengan beberapa replika. Pengguna menulis ke <em>source</em>, sementara pembacaan diarahkan ke replika. Di belakang layar, <em>source</em> mengirimkan perubahan ke semua replika lewat jalur replikasi. Pola ini yang kemudian dikenal sebagai <em>master–slave</em> atau <em>primary–secondary</em>.</p>

<p align="center">Tabel 4: Tujuan replikasi data</p>

<table align="center">
  <thead>
    <tr>
      <th>Tujuan</th>
      <th>Penjelasan</th>
      <th>Contoh manfaat</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Ketersediaan (<em>availability</em>)</td>
      <td>Jika satu server gagal, server lain masih punya salinan data</td>
      <td>Layanan tidak ikut mati saat satu pusat data bermasalah</td>
    </tr>
    <tr>
      <td>Kinerja (<em>performance</em>)</td>
      <td>Pengguna bisa membaca dari server terdekat</td>
      <td>Waktu respons lebih singkat karena jarak jaringan lebih pendek</td>
    </tr>
    <tr>
      <td>Reliabilitas (<em>reliability</em>)</td>
      <td>Risiko kehilangan data menurun karena ada cadangan di banyak tempat</td>
      <td>Data tetap ada walau satu disk rusak permanen</td>
    </tr>
    <tr>
      <td>Skalabilitas</td>
      <td>Beban baca (<em>read load</em>) bisa dibagi ke beberapa server</td>
      <td>Replika bisa ditambah saat jumlah pembaca meningkat</td>
    </tr>
  </tbody>
</table>

---

## 8. Jenis replikasi

<p align="justify">Istilah jenis replikasi di slide bercampur antara pembagian berdasarkan peran node dan pembagian berdasarkan waktu. Keduanya memang sering dibicarakan bersamaan, jadi perlu dipisahkan dengan jelas.</p>

<p align="center">Tabel 5: Jenis replikasi dan karakteristiknya</p>

<table align="center">
  <thead>
    <tr>
      <th>Jenis</th>
      <th>Cara kerja</th>
      <th>Kelebihan</th>
      <th>Kekurangan</th>
      <th>Contoh</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><em>Master–slave</em> (<em>primary–secondary</em>)</td>
      <td>Satu master menulis data, slave hanya membaca</td>
      <td>Konsistensi kuat dan alur data sederhana</td>
      <td>Bottleneck ada di master</td>
      <td>PostgreSQL <em>streaming replication</em></td>
    </tr>
    <tr>
      <td><em>Multi-master</em> (<em>peer-to-peer</em>)</td>
      <td>Semua node bisa menulis dan membaca</td>
      <td>Fleksibel, tidak ada satu titik yang harus selalu hidup</td>
      <td>Rawan konflik data</td>
      <td>PostgreSQL <em>logical replication</em>, Apache Cassandra, MongoDB, Apache Ignite</td>
    </tr>
    <tr>
      <td><em>Synchronous replication</em></td>
      <td>Pembaruan ditulis ke semua node sekaligus sebelum dianggap selesai</td>
      <td>Konsisten</td>
      <td>Lambat</td>
      <td>Google Spanner</td>
    </tr>
    <tr>
      <td><em>Asynchronous replication</em></td>
      <td>Pembaruan dikirim ke node lain setelah transaksi selesai di master</td>
      <td>Cepat</td>
      <td>Bisa terjadi jeda data (<em>data delay</em>)</td>
      <td>MySQL mode <em>async</em>, DynamoDB</td>
    </tr>
  </tbody>
</table>

<p align="justify">Perhatikan bahwa dua baris pertama menjawab pertanyaan "siapa yang boleh menulis", sedangkan dua baris terakhir menjawab pertanyaan "kapan penulisan dianggap selesai". Sebuah sistem bisa berada di persilangan keduanya, misalnya <em>master–slave</em> yang memakai replikasi <em>asynchronous</em> seperti konfigurasi PostgreSQL yang paling umum.</p>

---

## 9. Strategi sinkronisasi replika

<p align="justify">Kalau jenis replikasi hanya memberi dua sumbu pembeda, slide menyusun strategi sinkronisasi dalam empat dimensi. Keempatnya sering dipakai bersamaan, dan pilihan pada satu dimensi memengaruhi pilihan pada dimensi lain.</p>

<p align="center">Tabel 6: Empat dimensi strategi sinkronisasi replika</p>

<table align="center">
  <thead>
    <tr>
      <th>Dimensi</th>
      <th>Pilihan</th>
      <th>Pertanyaan yang dijawab</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Waktu sinkronisasi</td>
      <td><em>Synchronous</em>, <em>asynchronous</em>, <em>semi-synchronous</em></td>
      <td>Kapan sumber boleh menganggap penulisan selesai?</td>
    </tr>
    <tr>
      <td>Arah replikasi</td>
      <td><em>Master–slave</em>, <em>master–master</em> simetris, <em>master–master</em> asimetris</td>
      <td>Siapa yang boleh menulis dan siapa yang boleh membaca?</td>
    </tr>
    <tr>
      <td>Cara sinkronisasi</td>
      <td><em>Pull-based</em>, <em>push-based</em>, <em>hybrid</em></td>
      <td>Siapa yang mengambil inisiatif mengirim perubahan?</td>
    </tr>
    <tr>
      <td>Penanganan konflik</td>
      <td>LWW, <em>vector clock</em>, CRDT, OT</td>
      <td>Bagaimana kalau ada dua pembaruan yang bertabrakan?</td>
    </tr>
  </tbody>
</table>

<p align="justify">Pada dimensi arah, <em>master–master</em> dibedakan lagi menjadi dua. Yang simetris berarti semua node punya peran, sumber daya, dan kemampuan yang sama. Yang asimetris berarti peran dan kapasitasnya berbeda, misalnya satu node lebih kuat dan lebih sering menulis sementara node lain menjadi pendamping.</p>

### 9.1 Berdasarkan waktu: asynchronous, semi-synchronous, synchronous

<p align="justify">Perbedaan ketiganya terletak pada berapa banyak konfirmasi yang ditunggu sebelum sumber menyatakan transaksi selesai.</p>

- <strong>Asynchronous.</strong> Sumber melakukan <em>commit</em> tanpa menunggu replika. Ini yang tercepat, tetapi juga yang paling berisiko kehilangan data: kalau sumber mati sebelum perubahan terkirim, perubahan itu hilang.
- <strong>Semi-synchronous.</strong> Sumber menunggu satu replika menerima data. Ini kompromi yang cukup seimbang antara konsistensi dan performa, dan inilah yang dipakai MySQL.
- <strong>Synchronous.</strong> Sumber menunggu semua replika melakukan <em>commit</em>. Konsistensinya tertinggi, tetapi performanya paling rendah karena satu replika yang lambat bisa menahan seluruh transaksi.

<p align="justify">Cara membacanya sebagai spektrum: di kiri ada kecepatan dengan risiko, di kanan ada keamanan dengan ongkos. Semi-synchronous ada di tengah, dan karena itu paling sering jadi pilihan default di dunia nyata.</p>

### 9.2 Berdasarkan cara: pull, push, dan hybrid

<p align="justify">Dimensi ini soal inisiatif. Pada <strong>pull-based</strong>, replika yang aktif memeriksa pembaruan secara periodik ke sumber. Jika ada pembaruan, replika mengambilnya; jika tidak, dibiarkan saja. Kelemahannya adalah jeda sampai pemeriksaan berikutnya.</p>

<p align="justify">Pada <strong>push-based</strong>, sumber yang aktif mengirim pembaruan ke replika saat terjadi perubahan. Implementasinya biasanya memakai CDC (<em>Change Data Capture</em>) di sisi sumber, lalu pengiriman dilakukan lewat WebSockets atau SSE (<em>Server-Sent Events</em>). Hasilnya lebih dekat ke <em>real-time</em>, tetapi sumber harus tahu ke mana perubahan harus dikirim.</p>

<p align="justify"><strong>Hybrid</strong> menggabungkan keduanya, misalnya push untuk perubahan yang mendesak dan pull untuk rekonsiliasi berkala.</p>

### 9.3 Berdasarkan penanganan konflik

<p align="justify">Konflik hanya muncul kalau ada lebih dari satu penulis, sehingga bagian ini paling relevan untuk <em>multi-master</em>. Ada empat pendekatan yang disebut di slide.</p>

<ul>
  <li><strong>LWW (<em>Last Write Wins</em>).</strong> Pembaruan dengan <em>timestamp</em> terbaru yang dipakai, yang lain diabaikan. Sederhana, tetapi bisa membuang pembaruan yang sebenarnya sah.</li>
  <li><strong><em>Vector clock</em>.</strong> Pengurutan ditentukan dengan <em>vector clock</em>, seperti yang dibahas pada kuliah minggu sebelumnya. Pendekatan ini bisa membedakan mana yang lebih baru dan mana yang benar-benar bersamaan.</li>
  <li><strong>CRDT (<em>Conflict-free Replicated Data Type</em>).</strong> Setiap node memakai struktur data lokal, bukan bergantung pada struktur data di server pusat. Contohnya Yjs (https://yjs.dev/) yang dipakai antara lain untuk <em>collaborative editor</em> seperti Google Docs.</li>
  <li><strong>OT (<em>Operational Transformation</em>).</strong> Perubahan ditransformasikan berdasarkan informasi lokal, sehingga dua operasi yang bersamaan bisa diterapkan tanpa saling merusak.</li>
</ul>

---

## 10. Dua contoh konkret: CRDT dan OT

<p align="justify">Dua pendekatan terakhir paling mudah dipahami lewat contoh, dan slide memang menyediakan keduanya secara lengkap. Keduanya memakai pasangan tokoh yang sama, Agnes Monica dan Bambang, dengan masalah yang berbeda.</p>

### 10.1 CRDT: menghitung jumlah akses

<p align="justify">Agnes Monica dan Bambang akan menghitung jumlah akses ke sebuah website. Keduanya memulai dengan peta lokal yang isinya nol untuk masing-masing orang.</p>

```
Kondisi awal (di kedua perangkat)
{ "Agnes Monica": 0, "Bambang": 0 }
```

<p align="justify">Agnes Monica mencatat 5 pengunjung di perangkatnya, sedangkan Bambang mencatat 3 pengunjung di perangkatnya. Karena keduanya offline atau belum tersinkron, masing-masing hanya tahu angkanya sendiri.</p>

```
Setelah Agnes Monica mencatat 5 pengunjung
{ "Agnes Monica": 5, "Bambang": 0 }

Setelah Bambang mencatat 3 pengunjung
{ "Agnes Monica": 0, "Bambang": 3 }
```

<p align="justify">Saat kedua perangkat tersinkronisasi, CRDT memakai aturan <em>max</em> untuk setiap orang. Nilai Agnes Monica menjadi max(5, 0) = 5, dan nilai Bambang menjadi max(0, 3) = 3.</p>

```
Hasil akhir setelah sinkronisasi
{ "Agnes Monica": 5, "Bambang": 3 }

Total akses = 5 + 3 = 8
```

<p align="justify"><strong><em>Perhatikan bahwa tidak ada koordinasi terpusat di sini. Setiap perangkat cukup menggabungkan nilai dengan aturan yang sama, dan hasilnya identik. Itulah sifat yang membuat CRDT disebut <em>conflict-free</em>: konflik tidak diselesaikan setelah terjadi, tetapi dicegah dengan memilih operasi yang selalu bisa digabung.</em></strong></p>

### 10.2 OT: dua orang mengedit dokumen yang sama

<p align="justify">Kasus OT sedikit lebih rumit karena menyangkut posisi karakter. Agnes Monica dan Bambang mengedit dokumen yang berisi teks "abc".</p>

- Agnes Monica menyisipkan "x" di awal, pada posisi indeks 0, sehingga catatan lokalnya menjadi "xabc"
- Bambang menghapus karakter "c" di indeks 2, sehingga catatan lokalnya menjadi "ab"

<p align="justify">Kalau masing-masing langsung menerapkan operasi milik yang lain tanpa penyesuaian, hasilnya berbeda. Agnes Monica menerima perintah "hapus indeks 2" dan menerapkannya pada "xabc", sehingga yang terhapus justru "b" dan hasilnya "xac". Bambang menerima perintah "sisipkan x di indeks 0" dan menerapkannya pada "ab", sehingga hasilnya "xab". Kedua dokumen sekarang tidak sama.</p>

<p align="center">Tabel 7: Konflik posisi pada OT dan penyelesaiannya</p>

<table align="center">
  <thead>
    <tr>
      <th>Sudut pandang</th>
      <th>Operasi yang diterima</th>
      <th>Hasil tanpa transformasi</th>
      <th>Transformasi yang dilakukan</th>
      <th>Hasil akhir</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Agnes Monica</td>
      <td>"Hapus indeks 2" dari Bambang</td>
      <td>"xac" (yang terhapus "b")</td>
      <td>Posisi digeser karena ada sisipan di depannya: "hapus indeks 2" menjadi "hapus indeks 3"</td>
      <td>"xab"</td>
    </tr>
    <tr>
      <td>Bambang</td>
      <td>"Sisipkan x di indeks 0" dari Agnes Monica</td>
      <td>"xab"</td>
      <td>Karena sisipan terjadi sebelum perubahan lokalnya, indeks penghapusan "c" disesuaikan dari 2 menjadi 3</td>
      <td>"xab"</td>
    </tr>
  </tbody>
</table>

<p align="justify"><strong><em>OT bekerja dengan mengubah satu operasi agar tetap bermakna setelah operasi lain diterapkan. Hasil akhirnya, kedua pengguna mendapatkan dokumen yang sama: "xab".</em></strong></p>

---

## 11. Protokol replikasi

<p align="justify">Setelah strategi, ada tingkat berikutnya: protokol. Kalau strategi menjelaskan kebijakan, protokol menjelaskan mekanismenya. Slide menyebut lima protokol yang perlu dikenal.</p>

1. <strong><em>Primary-based</em> (<em>master–slave</em>).</strong> Satu node menjadi tempat menulis, node lain melayani pembacaan
2. <strong><em>Quorum-based</em>.</strong> Replikasi dilakukan untuk sejumlah node tertentu sesuai kesepakatan
3. <strong><em>State machine replication</em>.</strong> Semua replika mengeksekusi perintah yang sama dalam urutan yang sama
4. <strong><em>Gossip protocol</em>.</strong> Status disebarkan dari node ke node secara acak dan berantai
5. <strong><em>Chain replication</em>.</strong> Node disusun berantai, penulisan masuk dari kepala dan pembacaan diambil dari ekor

### 11.1 Quorum-based

<p align="justify">Protokol ini mengubah cara pandang terhadap keberhasilan operasi. Sebuah tulis atau baca dianggap berhasil bukan karena semua node menjawab, tetapi karena sejumlah node tertentu sudah mengonfirmasi. Syaratnya dirumuskan sebagai berikut.</p>

```
W + R > N
```

<p align="justify">W adalah jumlah node minimal yang harus mengonfirmasi operasi tulis, R adalah jumlah node minimal yang harus mengonfirmasi operasi baca, dan N adalah total jumlah node. Selama W + R lebih besar dari N, himpunan node yang menjawab pembacaan pasti beririsan dengan himpunan node yang menerima penulisan. Irisan itulah yang menjamin data terbaru selalu ditemukan.</p>

<p align="justify">Contohnya basis data terdistribusi dengan tiga node: Node A, Node B, dan Node C. Konfigurasi kuorumnya N = 3, W = 2, dan R = 2. Cek syaratnya: 2 + 2 = 4, dan 4 lebih besar dari 3, jadi konfigurasi ini valid.</p>

<p align="justify"><strong>Skenario tulis.</strong> Klien mengirim perintah menulis x = 100 ke klaster. Permintaan dikirim ke Node A, B, dan C secara paralel. Node A sukses menyimpan dan membalas ACK. Node B juga sukses dan membalas ACK. Node C sedang lambat atau <em>timeout</em>. Karena dua ACK sudah terkumpul dan itu memenuhi W = 2, operasi tulis dinyatakan berhasil, meskipun Node C belum selesai memproses datanya.</p>

<p align="justify"><strong>Skenario baca.</strong> Klien ingin membaca data x. Sistem meminta data dari minimal dua node karena R = 2. Misalnya klien membaca dari Node A dan Node C. Node A memberikan versi terbaru, yaitu x = 100. Node C mungkin masih menyimpan versi lama atau bahkan belum menerima datanya. Tetapi karena penulisan sebelumnya melibatkan Node A, data terbaru pasti terbaca dari irisan tersebut.</p>

<p align="justify"><strong><em>Di sinilah keindahan quorum: sistem tidak perlu menunggu semua node, tetapi tetap bisa menjamin pembacaan yang benar. Yang dikorbankan hanyalah kemampuan menoleransi lebih dari N dikurangi W kegagalan sekaligus.</em></strong></p>

### 11.2 State machine replication

<p align="justify">Protokol ini mengejar <em>high availability</em> dengan cara memiliki beberapa server atau replika yang mengeksekusi perintah yang sama dalam urutan yang sama. Server utama menerima permintaan klien, menambahkannya ke log, dan mengurutkannya, sementara replika menerapkan input yang sudah diurutkan tersebut.</p>

<p align="justify">Alurnya bisa diikuti lewat satu contoh. Keadaan awal, replika A, B, dan C semuanya memiliki x = 10. Klien mengirimkan perintah SET x = 20 ke <em>leader</em>, yaitu Replika A.</p>

- Replika A menetapkan nomor urut, menambahkan perintah ke log-nya, lalu mengirimkannya ke B dan C
- A menerima konfirmasi dari B dan C bahwa perintah tersebut sudah ada di log mereka
- Dengan begitu A menganggap perintah itu "dikomit"
- Semua replika, A, B, dan C, mengeksekusi SET x = 20 ke penyimpanan data lokal mereka, mengubah status dari x = 10 menjadi x = 20
- <em>Leader</em> mengirimkan hasil akhir ke klien

<p align="justify">Kunci desainnya adalah pemisahan antara "mencatat" dan "mengeksekusi". Perintah diurutkan dulu di log, baru dijalankan. Karena semua replika menjalankan urutan yang sama dari keadaan awal yang sama, hasil akhirnya juga sama. Inilah dasar dari sistem seperti Raft dan Paxos yang sudah disinggung pada materi sinkronisasi.</p>

### 11.3 Gossip protocol

<p align="justify">Protokol ini dipakai pada banyak <em>distributed database</em> seperti Apache Cassandra, antara lain untuk memeriksa keberadaan node dan mereplikasi data. Idenya meniru cara gosip menyebar: tidak ada pengumuman resmi, cukup satu orang bercerita ke beberapa orang, lalu mereka bercerita lagi.</p>

- Node A mendeteksi perubahan, misalnya memperbarui entri data atau mengetahui tetangganya sedang mati
- Setiap detik, Node A memilih satu sampai tiga <em>peer</em> secara acak, misalnya Node B, lalu membagikan statusnya saat ini termasuk pembaruan baru
- Node B menerima pembaruan, memperbarui statusnya sendiri, lalu secara acak memilih Node C untuk membagikan hal yang sama
- Melalui beberapa putaran, semua node dalam klaster dengan cepat mempelajari status atau keadaan node yang baru

<p align="justify">Kelebihannya adalah ketahanan: tidak ada satu node yang wajib hidup agar informasi menyebar. Kekurangannya adalah penyebaran bersifat probabilistik, sehingga tidak ada jaminan waktu pasti sampai semua node tahu.</p>

### 11.4 Chain replication

<p align="justify">Protokol ini menyusun replika seperti rantai, bukan seperti bintang. Ada satu <em>head</em> di ujung depan, beberapa node di tengah, dan satu <em>tail</em> di ujung belakang. Penulisan selalu masuk lewat <em>head</em>, sedangkan pembacaan selalu diambil dari <em>tail</em>.</p>

```
Posisi node    : [head] --> [A] --> [B] --> [C] --> [tail]

Alur penulisan : Klien --write--> [head] --> [A] --> [B] --> [C] --> [tail]
Alur konfirmasi: [tail] --> [C] --> [B] --> [A] --> [head] --ack--> Klien
Alur pembacaan : Klien <--read-- [tail]
```

<p align="justify">Alurnya begini. Klien mengirim penulisan ke <em>head</em>. <em>Head</em> meneruskannya ke node berikutnya, node itu meneruskan lagi, sampai perintah mencapai <em>tail</em>. Setelah <em>tail</em> menyimpan perubahan, konfirmasi dikirim balik menyusuri rantai sampai kembali ke <em>head</em>, lalu <em>head</em> memberitahu klien bahwa penulisan berhasil.</p>

<p align="justify">Pembacaan dilayani oleh <em>tail</em>. Karena <em>tail</em> adalah node terakhir yang menerima pembaruan, membaca dari <em>tail</em> menjamin data yang dibaca sudah melewati seluruh rantai. Hasilnya konsistensi kuat tanpa perlu kuorum, dengan catatan jalur penulisan menjadi lebih panjang dan kegagalan di tengah rantai harus ditangani dengan mengeluarkan node tersebut dari rantai. Pola ini dipakai antara lain pada lapisan penyimpanan Azure.</p>

---

## 12. Studi kasus di industri

<p align="justify">Slide menutup materi dengan dua studi kasus nyata, yaitu PostgreSQL 18 <em>Streaming Replication</em> dan Apache Ignite 3 <em>Master–Master</em>. Keduanya sengaja dipilih karena mewakili dua kutub yang berbeda: satu sangat bergantung pada peran <em>primary</em>, satu membagi peran ke semua node.</p>

<p align="center">Tabel 8: Perbandingan dua studi kasus</p>

<table align="center">
  <thead>
    <tr>
      <th>Aspek</th>
      <th>PostgreSQL 18 <em>streaming replication</em></th>
      <th>Apache Ignite 3 <em>master–master</em></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Pola replikasi</td>
      <td><em>Master–slave</em>: satu <em>primary</em>, satu atau lebih <em>standby</em></td>
      <td><em>Multi-master</em>: semua node dapat menerima penulisan</td>
    </tr>
    <tr>
      <td>Arah penulisan</td>
      <td>Hanya ke <em>primary</em></td>
      <td>Ke node mana pun</td>
    </tr>
    <tr>
      <td>Arah pembacaan</td>
      <td><em>Primary</em> atau <em>standby</em>, tergantung kebutuhan konsistensi</td>
      <td>Ke node mana pun, termasuk node yang menulis</td>
    </tr>
    <tr>
      <td>Mekanisme</td>
      <td>Pengiriman aliran WAL dari <em>primary</em> ke <em>standby</em></td>
      <td>Replikasi data antar node dengan pembagian data dan konsistensi terkelola</td>
    </tr>
    <tr>
      <td>Isu utama</td>
      <td>Jeda replikasi dan proses <em>failover</em></td>
      <td>Konflik penulisan dan kebutuhan mekanisme penyelesaian</td>
    </tr>
  </tbody>
</table>

<p align="justify">Pada PostgreSQL, <em>streaming replication</em> bekerja dengan mengalirkan WAL (<em>Write-Ahead Log</em>) dari <em>primary</em> ke <em>standby</em>. <em>Standby</em> menerapkan ulang catatan log itu sehingga isinya mengikuti <em>primary</em>. Karena hanya ada satu arah penulisan, konflik penulisan tidak terjadi. Yang harus dipikirkan justru sebaliknya: seberapa jauh <em>standby</em> tertinggal, dan apa yang terjadi kalau <em>primary</em> mati.</p>

<p align="justify">Pada Apache Ignite 3, semua node boleh menulis, sehingga tidak ada satu titik yang menjadi <em>bottleneck</em>. Konsekuensinya kembali ke pembahasan sebelumnya: ketika dua node menulis data yang sama secara bersamaan, harus ada aturan penyelesaian konflik. Di sinilah strategi seperti LWW, <em>vector clock</em>, atau CRDT menjadi relevan.</p>

<p align="justify">Slide menyebut bahwa detail kedua studi kasus ini dibahas pada <em>lecture notes</em> dan <em>source code</em> yang menyertai materi. Karena itu yang dicatat di sini adalah kerangka pemahamannya, bukan konfigurasi teknis langkah demi langkah.</p>

---

## 13. Diskusi

<p align="justify">Slide menutup dengan dua pertanyaan diskusi. Keduanya bagus karena memaksa kita memilih, bukan menghafal.</p>

<p align="justify"><strong>Pertanyaan 1: apa kelebihan dan kekurangan <em>strong</em> versus <em>eventual consistency</em>?</strong></p>

<p align="justify">Kelebihan <em>strong consistency</em> adalah kepastian. Setiap pembacaan mengembalikan data terbaru, sehingga kesalahan logika bisnis seperti saldo minus atau stok terjual ganda bisa dicegah. Kekurangannya ada pada latensi dan ketersediaan: sistem harus menunggu koordinasi antar node, dan saat terjadi partisi jaringan ia cenderung menolak permintaan demi menjaga konsistensi.</p>

<p align="justify">Kelebihan <em>eventual consistency</em> adalah respons cepat dan layanan yang tetap hidup meskipun sebagian node bermasalah. Kekurangannya adalah jendela waktu di mana data bisa berbeda, sehingga aplikasi harus dirancang untuk menerima itu, misalnya dengan menggabungkan data saat konflik atau menampilkan status sementara kepada pengguna.</p>

<p align="justify"><strong>Pertanyaan 2: bagaimana jika replikasi gagal di satu node?</strong></p>

<p align="justify">Jawabannya bergantung pada pola yang dipakai. Pada <em>master–slave</em>, kegagalan di <em>standby</em> biasanya hanya mengurangi kapasitas baca; <em>primary</em> tetap melayani penulisan, dan <em>standby</em> bisa dibangun ulang dari salinan penuh. Yang berbahaya adalah kegagalan di <em>primary</em>: dibutuhkan proses <em>failover</em> untuk menaikkan salah satu <em>standby</em> menjadi <em>primary</em>, dan pada replikasi <em>asynchronous</em> selalu ada kemungkinan sebagian transaksi terakhir belum sampai sehingga hilang.</p>

<p align="justify">Pada <em>multi-master</em> atau rantai, kegagalan satu node menuntut mekanisme deteksi dan pengeluaran node dari kelompok, misalnya lewat <em>gossip</em> untuk mendeteksi node mati atau dengan menyambung ulang rantai pada <em>chain replication</em>. Risiko yang harus dihindari adalah <em>split-brain</em>, yaitu dua bagian sistem sama-sama merasa berhak menulis sehingga data bercabang. Karena itu pemilihan koordinator dan kuorum kembali menjadi penting.</p>

---

## Ringkasan

<p align="justify"><strong>Konsistensi adalah janji tentang kapan perubahan data terlihat oleh node lain, dan replikasi adalah alasan janji itu perlu dibuat.</strong> Tanpa konsistensi, transfer bank bisa membuat uang seolah menghilang untuk sementara; tanpa replikasi, sistem kehilangan ketersediaan dan ketahanan terhadap kegagalan. Keduanya tidak bisa dipisahkan.</p>

<p align="justify">Teorema CAP menjelaskan mengapa kita tidak bisa memiliki semuanya. Karena partisi jaringan pasti terjadi, pilihan nyata ada di antara tetap melayani dengan data yang mungkin belum sinkron, atau menolak sementara demi data yang konsisten. Model konsistensi kemudian menyediakan tangga kompromi, dari linearizability yang paling ketat sampai eventual consistency yang paling longgar, dengan model di antaranya seperti sequential, causal, read-your-writes, dan monotonic read yang masing-masing menyelesaikan masalah tertentu.</p>

<p align="justify">Replikasi dijalankan lewat kombinasi jenis, strategi, dan protokol. Jenis menentukan siapa yang boleh menulis dan kapan penulisan dianggap selesai. Strategi menentukan waktu sinkronisasi, arah, cara pengiriman, dan penanganan konflik. Protokol menentukan mekanismenya, mulai dari <em>primary-based</em>, quorum dengan syarat W + R > N, <em>state machine replication</em>, <em>gossip</em>, sampai <em>chain replication</em>. Ketika konflik tak terhindarkan, LWW, <em>vector clock</em>, CRDT, dan OT menyediakan cara menyelesaikannya, seperti yang terlihat pada contoh penghitung akses dan penyuntingan dokumen bersama.</p>

<p align="justify"><strong><em>Inti minggu ini adalah bahwa konsistensi bukan harga mati, melainkan keputusan desain. Sistem yang baik memilih tingkat konsistensi sesuai nilai data yang dijaganya, lalu memilih strategi dan protokol replikasi yang paling murah untuk mencapai tingkat itu.</em></strong></p>

---

## Referensi

- Slide perkuliahan Pertemuan 4, "Konsistensi dan Replikasi pada Sistem Terdistribusi", Dr. Bambang Purnomosidi D. P., FTI UTDI.
- Slide perkuliahan Pertemuan 3, "Sinkronisasi dan Koordinasi pada Sistem Terdistribusi" (untuk bagian <em>vector clock</em>), Dr. Bambang Purnomosidi D. P., FTI UTDI.
- Tanenbaum, A. S., dan van Steen, M. (2007). _Distributed Systems: Principles and Paradigms_.
- Kleppmann, M. (2017). _Designing Data-Intensive Applications_.
- Fox, A., dan Brewer, E. (1999). _Harvest, Yield, and Scalable Tolerant Systems_.
- Gilbert, S., dan Lynch, N. (2002). _Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services_.
- Gifford, D. K. (1979). _Weighted Voting for Replicated Data_.
- Shapiro, M., Preguiça, N., Baquero, C., dan Zawirski, M. (2011). _Conflict-free Replicated Data Types_.
- Ellis, C. A., dan Gibbs, S. J. (1989). _Concurrency Control in Groupware Systems_.
- van Renesse, R., dan Schneider, F. B. (2004). _Chain Replication for Supporting High Throughput and Availability_.
- DeCandia, G., dkk. (2007). _Dynamo: Amazon's Highly Available Key-value Store_.
- Lakshman, A., dan Malik, P. (2010). _Cassandra: A Decentralized Structured Storage System_.
- PostgreSQL Global Development Group. _Log-Shipping Standby Servers_. https://www.postgresql.org/docs/current/warm-standby.html
- Apache Software Foundation. _Apache Ignite Documentation_. https://ignite.apache.org/docs/latest/
- Medium. _An Overview of MySQL Replication_. https://medium.com/codex/an-overview-of-mysql-replication-9e5635cc3a8a
- Medium. _Chain Replication: How Does Azure Storage Handle Replication Under the Hood_. https://medium.com/@m.hassan.def/chain-replication-how-does-azure-storage-handle-replication-under-the-hood-79acbd3ac441
- Yjs. _Yjs: A CRDT Framework_. https://yjs.dev/
