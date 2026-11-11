# Ringkasan Materi Minggu ke-1: Pengenalan Sistem Terdistribusi dan Terdesentralisasi

Mata kuliah: Sistem Terdistribusi dan Terdesentralisasi<br>
Topik: Pengenalan Sistem Terdistribusi dan Terdesentralisasi<br>
Dosen: Dr. Bambang Purnomosidi D. P.<br>
Kode: FIF25002T | SKS: 3 | Prodi: Informatika, FTI UTDI

<p align="justify">Dokumen ini merangkum materi pertemuan pertama, mulai dari alasan mempelajari sistem terdistribusi sampai tren yang sedang berkembang. Urutan pembahasannya mengikuti agenda di slide, sehingga mudah dicocokkan saat belajar ulang.</p>

## Tujuan pembelajaran

- Mampu menjelaskan konsep sistem terdistribusi
- Mampu menjelaskan konsep sistem terdesentralisasi
- Mampu menjelaskan perbedaan sistem terdistribusi dengan sistem terdesentralisasi
- Mampu menyebutkan berbagai contoh penerapan nyata dari sistem terdistribusi dan terdesentralisasi
- Mampu menyebutkan berbagai tantangan dan tren terbaru di dunia sistem terdistribusi dan terdesentralisasi

## 1. Arti penting

<p align="justify">Bayangkan ketika sedang membuka Instagram. Gambar-gambar muncul dalam sepersekian detik, tidak peduli kita ada di Yogyakarta atau di Wamena. Tidak ada satu komputer pun di dunia yang bisa melayani ratusan juta orang seperti itu sendirian. Di balik layar ada ribuan mesin yang bekerja bersama, dan pengguna tidak pernah tahu mesin mana yang menjawab permintaan.</p>

<p align="justify">Itulah gambaran sederhana dari sistem terdistribusi. Hampir semua layanan yang dipakai sehari-hari berdiri di atasnya. Data dan pengguna tumbuh jauh lebih cepat daripada kemampuan satu server untuk menanganinya. Layanan modern juga dituntut tetap hidup meski ada komponen yang rusak, dan tersedia terus tanpa jeda.</p>

<p align="justify">Ada dua sifat yang perlu dipahami lebih dulu. <strong>Skalabilitas</strong> berarti kemampuan sistem menangani kenaikan beban, entah itu pengguna, data, atau permintaan, tanpa kinerjanya jatuh. <strong>Fault tolerance</strong> berarti sistem tetap berjalan normal walaupun sebagian komponennya, baik hardware maupun software, mengalami kegagalan. Kalau dua sifat ini digabung, kita bisa membayangkan layanan yang tetap melayani jutaan orang sambil sesekali kehilangan beberapa mesin di belakangnya.</p>

<p align="justify"><strong><em>Tanpa konsep ini, Google, Netflix, dan Blockchain tidak mungkin ada.</em></strong></p>

## 2. Pengertian sistem terdistribusi

<p align="justify">Definisi yang dipakai di slide berasal dari Tanenbaum dan van Steen tahun 2007, yaitu kumpulan komputer independen yang tampak bagi penggunanya sebagai satu sistem yang koheren. Ada tiga hal yang perlu digarisbawahi dari kalimat tersebut.</p>

<p align="justify">Pertama, komputernya independen. Masing-masing punya memori dan prosesor sendiri, tidak berbagi satu otak. Kedua, mereka berkomunikasi lewat jaringan, jadi selalu ada risiko pesan terlambat atau hilang. Ketiga, hasil akhirnya harus terasa seperti satu sistem. Inilah yang disebut <strong>transparansi</strong>.</p>

<p align="justify">Transparansi punya beberapa bentuk. Transparansi lokasi berarti pengguna tidak perlu tahu data ada di server mana. Transparansi akses berarti cara memanggil layanan tetap sama walau di belakangnya berbeda. Transparansi replikasi berarti pengguna tidak sadar bahwa data disimpan di beberapa tempat.</p>

<p align="justify"><strong><em>Yang paling sulit diwujudkan adalah transparansi kegagalan, yaitu kondisi ketika satu node mati dan pengguna sama sekali tidak merasakannya.</em></strong> Bagian inilah yang paling banyak menghabiskan usaha para perancang sistem.</p>

## 3. Arsitektur sistem dasar

<p align="justify">Ada tiga pola dasar yang biasa dipakai. Masing-masing punya cara kerja dan <em>trade-off</em> sendiri. <strong><em>Tidak ada satu pola yang selalu unggul untuk semua kebutuhan.</em></strong></p>

<p align="center">Tabel 1: Perbandingan arsitektur sistem dasar</p>

<table align="center">
  <thead>
    <tr>
      <th>Pola</th>
      <th>Cara kerja</th>
      <th>Kelebihan</th>
      <th>Kekurangan</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Client-server</td>
      <td>Server menyimpan data dan menjawab permintaan dari klien</td>
      <td>Aturan terpusat, mudah dikembangkan dan diamankan</td>
      <td>Server menjadi titik tumpu; jika penuh atau mati, layanan ikut terganggu</td>
    </tr>
    <tr>
      <td>Peer-to-peer</td>
      <td>Setiap node bisa menjadi pengirim sekaligus penerima</td>
      <td>Tidak bergantung pada satu pusat sehingga lebih tahan gangguan</td>
      <td>Koordinasi lebih rumit dan keamanan lebih sulit dijaga</td>
    </tr>
    <tr>
      <td>Hybrid</td>
      <td>Server pusat menangani katalog atau daftar anggota, data tersebar di banyak node</td>
      <td>Menggabungkan kepastian dari pusat dengan ketahanan P2P</td>
      <td>Desain dan operasionalnya lebih kompleks</td>
    </tr>
  </tbody>
</table>

<p align="justify">Pola client-server cocok ketika aturan main harus jelas dan data perlu dijaga ketat. Peer-to-peer lebih menarik ketika ketahanan terhadap gangguan lebih penting daripada kemudahan pengelolaan. Hybrid menjadi jalan tengah yang paling sering dipakai sistem nyata, karena murni P2P sering terlalu sulit dioperasikan.</p>

## 4. Karakteristik sistem terdistribusi

<p align="justify">Karakteristik sistem terdistribusi bisa dilihat sebagai daftar kemampuan yang harus dipenuhi sekaligus. <strong>Heterogenitas</strong> menuntut adanya protokol dan lapisan <em>middleware</em> yang menyembunyikan perbedaan antar komponen, sehingga mesin dengan sistem operasi atau versi perangkat lunak berbeda tetap bisa bekerja bersama.</p>

<p align="center">Tabel 2: Karakteristik sistem terdistribusi</p>

<table align="center">
  <thead>
    <tr>
      <th>Karakteristik</th>
      <th>Penjelasan</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Transparansi lokasi</td>
      <td>Pengguna tidak perlu tahu data berada di server mana</td>
    </tr>
    <tr>
      <td>Skalabilitas</td>
      <td>Sistem mampu menangani kenaikan beban tanpa kinerjanya jatuh</td>
    </tr>
    <tr>
      <td>Fault tolerance</td>
      <td>Sistem tetap berjalan normal walau sebagian komponen gagal</td>
    </tr>
    <tr>
      <td>Kinerja tinggi</td>
      <td>Permintaan diproses cepat meski jumlah pengguna besar</td>
    </tr>
    <tr>
      <td>Heterogenitas</td>
      <td>Komponen dengan sistem operasi, versi, atau perangkat keras berbeda tetap bisa bekerja bersama</td>
    </tr>
  </tbody>
</table>

## 5. Pengertian sistem terdesentralisasi

<p align="justify">Kalau sistem terdistribusi berbicara tentang banyak mesin, sistem terdesentralisasi berbicara tentang banyak pihak yang memegang kendali.</p>

<p align="justify"><strong>Sentralisasi</strong> berarti ada satu titik kontrol. Satu pihak memutuskan, yang lain mengikuti. <strong>Desentralisasi</strong> membagi kontrol itu ke banyak pihak, sehingga tidak ada satu titik yang kegagalannya bisa melumpuhkan semuanya.</p>

<p align="justify"><strong><em>Karena tidak ada pusat, keputusan diambil lewat konsensus, yaitu kesepakatan bersama antar peserta jaringan.</em></strong> Contoh yang paling jelas adalah DNS. Nama domain di seluruh dunia tersebar di banyak server yang saling merujuk, dan tidak ada satu komputer yang menyimpan semuanya. BitTorrent juga begitu, karena file dipecah dan diambil dari banyak pengguna lain secara bersamaan. Blockchain melangkah lebih jauh dengan menjadikan konsensus sebagai aturan utama untuk mencatat transaksi.</p>

## 6. Sistem terdistribusi vs sistem terdesentralisasi

<p align="justify">Orang sering menyamakan dua istilah ini, padahal titik beratnya berbeda. Sistem terdistribusi berfokus pada pembagian beban kerja, sedangkan sistem terdesentralisasi berfokus pada pembagian kontrol.</p>

<p align="center">Tabel 3: Perbandingan sistem terdistribusi dan terdesentralisasi</p>

<table align="center">
  <thead>
    <tr>
      <th>Aspek</th>
      <th>Terdistribusi</th>
      <th>Terdesentralisasi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Yang dibagi</td>
      <td>Beban kerja</td>
      <td>Kontrol</td>
    </tr>
    <tr>
      <td>Titik keputusan</td>
      <td>Bisa tetap terpusat</td>
      <td>Tersebar di banyak pihak</td>
    </tr>
    <tr>
      <td>Contoh</td>
      <td>GFS, HDFS, Kafka, MinIO</td>
      <td>Blockchain, DNS, BitTorrent</td>
    </tr>
  </tbody>
</table>

<p align="justify">Contoh yang memperjelas perbedaan ini adalah Google File System dan HDFS. Datanya dipecah dan disimpan di banyak mesin, jadi jelas terdistribusi. Namun ada satu <code>NameNode</code> atau master yang mengatur letak setiap potongan data, sehingga kendalinya tetap terpusat. Sebaliknya, blockchain mendistribusikan beban sekaligus kontrol, karena tidak ada satu pihak yang boleh memutuskan isi <em>ledger</em> sendirian.</p>

<p align="justify"><strong><em>Satu sistem bisa terdistribusi tanpa terdesentralisasi.</em></strong></p>

## 7. Studi kasus sistem terdistribusi

<p align="justify">Google File System dan HDFS dipakai untuk menyimpan data berukuran sangat besar. <strong>File dipecah menjadi potongan kecil, disebar ke banyak mesin, lalu direplikasi agar aman ketika ada mesin yang mati.</strong> Pendekatan ini memungkinkan penyimpanan data sebesar <em>petabyte</em> dengan perangkat keras biasa.</p>

<p align="justify">Apache Kafka dan Apache Pulsar dipakai untuk <em>messaging</em> dan <em>streaming</em>. Keduanya menjadi jalur data yang menyalurkan peristiwa dari banyak sumber ke banyak tujuan secara berurutan dan tahan gangguan. Banyak perusahaan memakainya untuk mencatat aktivitas pengguna, transaksi, atau log sistem.</p>

<p align="justify">Netflix, Twitch, dan YouTube menghadapi masalah yang mirip, yaitu mengirim video ke penonton di berbagai belahan dunia tanpa membuat video tersendat. Solusinya biasanya berupa jaringan pengiriman konten yang menaruh salinan video di banyak lokasi, sehingga penonton mengambil dari server yang paling dekat.</p>

<p align="justify">Instagram, Threads, Facebook, dan TikTok memakai pendekatan serupa untuk feed dan penyimpanan foto. Yang menarik, feed media sosial biasanya rela menampilkan data yang belum sepenuhnya terbaru, asalkan halaman tetap cepat terbuka. Ini berkaitan dengan pembahasan konsistensi nanti.</p>

## 8. Studi kasus sistem terdesentralisasi

<p align="justify">Blockchain seperti Bitcoin, Ethereum, Algorand, Sui, Near, dan Aptos menyimpan catatan transaksi yang sama di banyak node. <strong><em>Tidak ada satu pihak yang bisa mengubah catatan itu sendirian karena perubahan harus disetujui mayoritas jaringan.</em></strong> Harga yang dibayar adalah kecepatan dan biaya komputasi, karena setiap keputusan butuh konsensus.</p>

<p align="justify">DNS mendesentralisasi penamaan domain. Tidak ada satu server tunggal yang menyimpan seluruh daftar nama domain dunia. Setiap pengelola zona bertanggung jawab atas bagiannya sendiri, dan sistemnya saling merujuk.</p>

<p align="justify">BitTorrent mendesentralisasi distribusi file. File dipecah menjadi bagian-bagian kecil, dan setiap pengunduh sekaligus menjadi pengirim bagi pengguna lain. Semakin banyak pengunduh, semakin cepat distribusinya, kebalikan dari model client-server biasa.</p>

## 9. CAP theorem

<p align="justify"><strong>CAP theorem</strong> diperkenalkan Eric Brewer pada tahun 2000 dan sering disebut Brewer's Theorem. Isinya tentang tiga sifat yang tidak bisa dimaksimalkan bersamaan pada penyimpanan data terdistribusi.</p>

<p align="justify"><strong>Consistency</strong> berarti setiap operasi baca mengembalikan penulisan terbaru, atau kalau tidak bisa, mengembalikan error. <strong>Availability</strong> berarti setiap permintaan ke node yang masih sehat akan mendapat jawaban tanpa error, walaupun data yang dikembalikan mungkin bukan yang paling baru. <strong>Partition tolerance</strong> berarti sistem tetap beroperasi ketika komunikasi antar node terputus atau jaringan terpecah.</p>

<p align="center">Tabel 4: Tiga sifat dalam CAP theorem</p>

<table align="center">
  <thead>
    <tr>
      <th>Sifat</th>
      <th>Arti</th>
      <th>Konsekuensi saat partisi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Consistency</td>
      <td>Setiap operasi baca mengembalikan penulisan terbaru atau error</td>
      <td>Data tetap akurat, tetapi sebagian permintaan bisa ditolak</td>
    </tr>
    <tr>
      <td>Availability</td>
      <td>Setiap permintaan ke node sehat mendapat jawaban tanpa error</td>
      <td>Layanan tetap melayani, tetapi data bisa belum sinkron</td>
    </tr>
    <tr>
      <td>Partition tolerance</td>
      <td>Sistem tetap beroperasi saat komunikasi antar node terputus</td>
      <td>Wajib dimiliki karena jaringan bisa terbelah kapan saja</td>
    </tr>
  </tbody>
</table>

<p align="justify">Dalam praktiknya, partisi jaringan bukan pilihan. Kabel bisa putus, jaringan bisa terbelah, dan sistem harus mengantisipasinya. <strong><em>Saat partisi terjadi, perancang harus memilih antara tetap melayani atau tetap konsisten.</em></strong></p>

<p align="justify">Kalau memilih <code>AP</code>, permintaan data akan selalu dilayani, dengan risiko data antar node sempat tidak sinkron. Ini pilihan yang wajar untuk layanan yang tidak boleh berhenti, seperti feed media sosial atau katalog produk.</p>

<p align="justify">Kalau memilih <code>CP</code>, permintaan baca maupun tulis akan ditolak saat tidak bisa dipastikan datanya konsisten. Ini pilihan untuk hal yang kesalahannya mahal, seperti saldo atau pencatatan transaksi. Setelah partisi selesai dan sistem pulih, biasanya ada proses <em>auto healing</em> yang menyamakan data kembali. Namun selama partisi berlangsung, keputusan desainnya tetap harus diambil lebih dulu.</p>

## 10. Konsistensi data

<p align="justify"><strong>Strong consistency</strong> berarti hasil baca selalu sama dengan kondisi terakhir yang ditulis. Kalau kita transfer uang lalu langsung mengecek saldo, angka yang muncul harus sudah memperhitungkan transfer itu. Untuk perbankan, ini wajib, karena selisih angka berarti masalah nyata.</p>

<p align="justify"><strong>Eventual consistency</strong> berarti sistem mungkin sempat menampilkan data lama, tetapi pada akhirnya semua node akan menyamakan diri. Jumlah suka di sebuah unggahan bisa terlambat beberapa detik, dan hampir tidak ada yang peduli. Media sosial memilih model ini karena kecepatan dan ketersediaan lebih penting daripada ketepatan detik per detik.</p>

<p align="center">Tabel 5: Perbandingan strong dan eventual consistency</p>

<table align="center">
  <thead>
    <tr>
      <th>Model</th>
      <th>Arti</th>
      <th>Cocok untuk</th>
      <th>Contoh</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Strong consistency</td>
      <td>Hasil baca selalu sama dengan kondisi terakhir yang ditulis</td>
      <td>Data yang selisihnya mahal</td>
      <td>Saldo bank, pencatatan transaksi</td>
    </tr>
    <tr>
      <td>Eventual consistency</td>
      <td>Data lama bisa muncul sementara, tetapi semua node menyamakan diri pada akhirnya</td>
      <td>Layanan yang mengutamakan kecepatan</td>
      <td>Jumlah suka, feed media sosial</td>
    </tr>
  </tbody>
</table>

<p align="justify"><strong><em>Pilihan di antara keduanya bergantung pada seberapa mahal sebuah ketidaksinkronan bagi pengguna.</em></strong></p>

## 11. Tantangan

<p align="justify"><strong>Latency</strong> dan <strong>throughput</strong> adalah dua hal yang sering tertukar. Latency berbicara tentang berapa lama satu permintaan diproses. Throughput berbicara tentang berapa banyak permintaan yang bisa diselesaikan dalam satuan waktu. Sistem bisa punya throughput besar tetapi latency tinggi kalau antreannya panjang.</p>

<p align="justify">Keamanan menjadi lebih rumit karena permukaan serangannya bertambah. DDoS membanjiri layanan dengan permintaan palsu sampai kewalahan. Pada sistem terdesentralisasi ada pula <strong>Sybil attack</strong>, yaitu serangan yang membuat banyak identitas palsu sekaligus untuk mendominasi pengaruh, suara, atau reputasi jaringan. Nama Sybil sendiri diambil dari buku tahun 1973 tentang seorang perempuan dengan gangguan identitas.</p>

<p align="justify"><strong><em>Semakin banyak mesin, semakin besar beban pengelolaannya.</em></strong> Konfigurasi, pemantauan, dan pemulihan harus diurus satu per satu. Pada sistem terdesentralisasi muncul persoalan tambahan berupa <strong>governance</strong>, yaitu siapa yang berhak mengubah aturan main. Dalam blockchain, perdebatan ini bisa berujung pada <em>fork</em>, ketika sebagian peserta memilih aturan yang berbeda dan jaringan terbelah.</p>

## 12. Tren masa depan

<p align="justify">Cloud-native dengan Kubernetes dan Docker sudah menjadi cara standar menjalankan aplikasi terdistribusi. <strong>Edge computing</strong> dan fog computing memindahkan sebagian pemrosesan lebih dekat ke pengguna, sehingga latency turun dan penggunaan <em>bandwidth</em> jaringan lebih hemat.</p>

<p align="justify">Web3 dan DeFi melanjutkan gagasan desentralisasi ke ranah keuangan dan kepemilikan digital, meski tantangan tata kelola dan keamanannya masih terbuka.</p>

<p align="justify"><strong>Federated learning</strong> membawa ide distribusi ke dunia kecerdasan buatan. <strong><em>Model dilatih di banyak perangkat, dan yang dikirim ke server hanya pembaruan modelnya, sementara data aslinya tetap tinggal di perangkat pengguna.</em></strong> Pendekatan ini menarik untuk data sensitif seperti rekam medis atau foto pribadi.</p>

## Ringkasan

<p align="justify"><strong>Sistem terdistribusi membagi beban kerja ke banyak mesin agar mampu menangani skala besar dan tetap hidup saat ada komponen yang gagal. Sistem terdesentralisasi membagi kontrol ke banyak pihak agar tidak ada satu titik yang bisa mengendalikan atau melumpuhkan semuanya.</strong> Keduanya saling terkait dan menjadi fondasi teknologi modern, tetapi fokusnya berbeda dan sebaiknya tidak dicampur.</p>

## Catatan

<p align="justify">Ada dua contoh di slide yang perlu dibaca dengan hati-hati. <strong>Apache Cassandra</strong> umumnya dikategorikan sebagai <code>AP</code> dengan konsistensi yang bisa diatur, bukan <code>CP</code>. Contoh <code>CP</code> yang lebih tidak diperdebatkan adalah HBase, ZooKeeper, atau etcd. <strong>MongoDB</strong> juga sering disebut <code>AP</code> di slide, padahal klasifikasinya bergantung pada konfigurasi <code>replica set</code> dan <code>read concern</code> yang dipakai.</p>

<p align="justify"><strong><em>Untuk ujian, sebaiknya ikuti penjelasan dosen, tetapi pahami juga bahwa pengelompokan ini punya nuansa.</em></strong> Banyak basis data modern menyediakan konsistensi yang bisa diatur per operasi, sehingga label AP atau CP tidak selalu berlaku untuk seluruh sistem.</p>

## Referensi

- Slide perkuliahan Pertemuan 1, "Pengenalan Sistem Terdistribusi dan Terdesentralisasi", Dr. Bambang Purnomosidi D. P., FTI UTDI.
- Tanenbaum, A. S., dan van Steen, M. (2007). *Distributed Systems: Principles and Paradigms*.
- Brewer, E. (2000). *Towards Robust Distributed Systems*.
