# Ringkasan Materi Minggu ke-1: Pengenalan Sistem Terdistribusi dan Terdesentralisasi

Mata Kuliah: Sistem Terdistribusi dan Terdesentralisasi

Topik: Pengenalan Sistem Terdistribusi dan Terdesentralisasi

Dosen: Dr. Bambang Purnomosidi D. P.

Kode: FIF25002T | SKS: 3 | Prodi: Informatika, FTI UTDI

---

## Tujuan pembelajaran

- Mampu menjelaskan konsep sistem terdistribusi
- Mampu menjelaskan konsep sistem terdesentralisasi
- Mampu menjelaskan perbedaan sistem terdistribusi dengan sistem terdesentralisasi
- Mampu menyebutkan berbagai contoh penerapan nyata dari sistem terdistribusi dan terdesentralisasi
- Mampu menyebutkan berbagai tantangan dan tren terbaru di dunia sistem terdistribusi dan terdesentralisasi

---

## 1. Arti penting

Bayangkan ketika sedang membuka Instagram. Gambar-gambar muncul dalam sepersekian detik, tidak peduli ada di Yogyakarta atau di Wamena. Tidak ada satu komputer pun di dunia yang bisa melayani ratusan juta orang seperti itu sendirian. Di balik layar ada ribuan mesin yang bekerja bersama, dan kamu sebagai pengguna tidak pernah tahu mesin mana yang menjawab permintaan.

Itulah gambaran sederhana dari sistem terdistribusi. Hampir semua layanan yang dipakai sehari-hari berdiri di atasnya. Data dan pengguna tumbuh jauh lebih cepat daripada kemampuan satu server untuk menanganinya. Layanan modern juga dituntut tetap hidup meski ada komponen yang rusak, dan tersedia terus tanpa jeda. Tanpa konsep ini, Google, Netflix, dan Blockchain tidak mungkin ada.

Dua istilah teknis yang sering muncul di sini perlu dipahami dulu. Skalabilitas berarti kemampuan sistem menangani kenaikan beban, entah itu pengguna, data, atau permintaan, tanpa kinerjanya jatuh. Sistem yang fault-tolerant berarti sistem itu tetap berjalan normal walaupun sebagian komponennya, baik hardware maupun software, mengalami kegagalan. Kalau dua sifat ini digabung, kita bisa membayangkan layanan yang tetap melayani jutaan orang sambil sesekali kehilangan beberapa mesin di belakangnya.

---

## 2. Pengertian sistem terdistribusi

Definisi yang dipakai di slide berasal dari Tanenbaum dan van Steen tahun 2007 yaitu kumpulan komputer independen yang tampak bagi penggunanya sebagai satu sistem yang koheren. Ada tiga hal yang perlu digarisbawahi dari kalimat tersebut.

Pertama, komputernya independen. Masing-masing punya memori dan prosesor sendiri, tidak berbagi satu otak. Kedua, mereka berkomunikasi lewat jaringan, jadi selalu ada risiko pesan terlambat atau hilang. Ketiga, hasil akhirnya harus terasa seperti satu sistem. Inilah yang disebut transparansi.

Transparansi punya beberapa bentuk. Transparansi lokasi berarti pengguna tidak perlu tahu data ada di server mana. Transparansi akses berarti cara memanggil layanan tetap sama walau di belakangnya berbeda. Transparansi replikasi berarti pengguna tidak sadar bahwa data disimpan di beberapa tempat. Yang paling sulit adalah transparansi kegagalan, yaitu kondisi ketika satu node mati dan pengguna sama sekali tidak merasakannya. Bagian terakhir inilah yang paling banyak menghabiskan usaha para perancang sistem.

---

## 3. Arsitektur sistem dasar

Ada tiga pola dasar yang biasa dipakai.

Client-server adalah pola paling mudah dibayangkan. Browser mengirim permintaan, server menjawab. Semua aturan main ada di server, jadi mudah dikembangkan dan diamankan. Kelemahannya, server menjadi titik tumpu. Kalau server itu penuh atau mati, seluruh layanan ikut terdampak.

Peer-to-peer membalik logikanya. Setiap komputer bisa menjadi pengirim sekaligus penerima. Tidak ada pusat yang harus selalu hidup, sehingga sistem lebih tahan terhadap gangguan. Harga yang dibayar adalah koordinasi yang lebih rumit, karena tidak ada satu pihak yang bisa memutuskan segalanya, dan keamanan menjadi lebih sulit dijaga ketika siapa saja boleh masuk.

Hybrid menggabungkan keduanya. Biasanya ada server pusat untuk hal-hal yang butuh kepastian, seperti daftar anggota atau katalog, sementara data sebenarnya tersebar di banyak node. Banyak sistem nyata memilih jalan tengah ini karena murni P2P sering terlalu sulit dikelola.

---

## 4. Karakteristik sistem terdistribusi

Dari penjelasan di atas, karakteristiknya bisa dirangkum sebagai berikut. Sistem harus transparan secara lokasi, mampu berkembang mengikuti beban, tahan terhadap kegagalan sebagian, punya kinerja yang tinggi, dan sanggup menangani heterogenitas. Heterogenitas artinya komponen yang dipakai tidak seragam. Bisa jadi ada mesin dengan sistem operasi berbeda, versi perangkat lunak berbeda, atau perangkat keras dari generasi berbeda. Sistem yang baik tetap bisa menyatukan semuanya, biasanya lewat protokol dan lapisan middleware yang menyembunyikan perbedaan itu.

---

## 5. Pengertian sistem terdesentralisasi

Kalau sistem terdistribusi berbicara tentang banyak mesin, sistem terdesentralisasi berbicara tentang banyak pihak yang memegang kendali.

Sentralisasi berarti ada satu titik kontrol. Satu pihak memutuskan, yang lain mengikuti. Desentralisasi membagi kontrol itu ke banyak pihak, sehingga tidak ada satu titik yang kegagalannya bisa melumpuhkan semuanya. Karena tidak ada pusat, keputusan diambil lewat konsensus, yaitu kesepakatan bersama antar peserta jaringan.

Contoh yang paling jelas adalah DNS. Nama domain di seluruh dunia tersebar di banyak server yang saling merujuk, dan tidak ada satu komputer yang menyimpan semuanya. BitTorrent juga begitu, karena file dipecah dan diambil dari banyak pengguna lain secara bersamaan. Blockchain melangkah lebih jauh dengan menjadikan konsensus sebagai aturan utama untuk mencatat transaksi.

---

## 6. Sistem terdistribusi vs sistem terdesentralisasi

Orang sering menyamakan dua istilah ini, padahal titik beratnya berbeda. Sistem terdistribusi berfokus pada pembagian beban kerja. Sistem terdesentralisasi berfokus pada pembagian kontrol.

Supaya lebih konkret, lihat Google File System dan HDFS. Datanya dipecah dan disimpan di banyak mesin, jadi jelas terdistribusi. Namun ada satu NameNode atau master yang mengatur letak setiap potongan data. Kendalinya tetap terpusat. Sebaliknya, blockchain mendistribusikan beban sekaligus kontrol, karena tidak ada satu pihak yang boleh memutuskan isi ledger sendirian.

| Aspek           | Terdistribusi           | Terdesentralisasi           |
| --------------- | ----------------------- | --------------------------- |
| Yang dibagi     | Beban kerja             | Kontrol                     |
| Titik keputusan | Bisa tetap terpusat     | Tersebar di banyak pihak    |
| Contoh          | GFS, HDFS, Kafka, MinIO | Blockchain, DNS, BitTorrent |

Keduanya bisa bertemu dalam satu sistem, dan sering memang begitu. Satu sistem bisa terdistribusi tanpa terdesentralisasi.

---

## 7. Studi kasus sistem terdistribusi

Google File System dan HDFS dipakai untuk menyimpan data berukuran sangat besar. File dipecah menjadi potongan kecil, disebar ke banyak mesin, lalu direplikasi agar aman ketika ada mesin yang mati. Pendekatan ini memungkinkan penyimpanan data sebesar petabyte dengan perangkat keras biasa.

Apache Kafka dan Apache Pulsar dipakai untuk messaging dan streaming. Keduanya menjadi jalur data yang menyalurkan peristiwa dari banyak sumber ke banyak tujuan secara berurutan dan tahan gangguan. Banyak perusahaan memakainya untuk mencatat aktivitas pengguna, transaksi, atau log sistem.

Netflix, Twitch, dan YouTube menghadapi masalah yang mirip, yaitu mengirim video ke penonton di berbagai belahan dunia tanpa membuat video tersendat. Solusinya biasanya berupa jaringan pengiriman konten yang menaruh salinan video di banyak lokasi, sehingga penonton mengambil dari server yang paling dekat.

Instagram, Threads, Facebook, dan TikTok memakai pendekatan serupa untuk feed dan penyimpanan foto. Yang menarik, feed media sosial biasanya rela menampilkan data yang belum sepenuhnya terbaru, asalkan halaman tetap cepat terbuka. Ini berkaitan dengan pembahasan konsistensi nanti.

---

## 8. Studi kasus sistem terdesentralisasi

Blockchain seperti Bitcoin, Ethereum, Algorand, Sui, Near, dan Aptos menyimpan catatan transaksi yang sama di banyak node. Tidak ada satu pihak yang bisa mengubah catatan itu sendirian karena perubahan harus disetujui mayoritas jaringan. Harga yang dibayar adalah kecepatan dan biaya komputasi, karena setiap keputusan butuh konsensus.

DNS mendesentralisasi penamaan domain. Tidak ada satu server tunggal yang menyimpan seluruh daftar nama domain dunia. Setiap pengelola zona bertanggung jawab atas bagiannya sendiri, dan sistemnya saling merujuk.

BitTorrent mendesentralisasi distribusi file. File dipecah menjadi bagian-bagian kecil, dan setiap pengunduh sekaligus menjadi pengirim bagi pengguna lain. Semakin banyak pengunduh, semakin cepat distribusinya, kebalikan dari model client-server biasa.

---

## 9. CAP theorem

Teorema CAP diperkenalkan Eric Brewer pada tahun 2000 dan sering disebut Brewer's Theorem. Isinya tentang tiga sifat yang tidak bisa dimaksimalkan bersamaan pada penyimpanan data terdistribusi.

Consistency berarti setiap operasi baca mengembalikan penulisan terbaru, atau kalau tidak bisa, mengembalikan error. Availability berarti setiap permintaan ke node yang masih sehat akan mendapat jawaban tanpa error, walaupun data yang dikembalikan mungkin bukan yang paling baru. Partition tolerance berarti sistem tetap beroperasi ketika komunikasi antar node terputus atau jaringan terpecah.

Dalam praktiknya, partisi jaringan bukan pilihan. Kabel bisa putus, jaringan bisa terbelah, dan sistem harus mengantisipasinya. Jadi yang sebenarnya dipilih adalah sikap saat partisi terjadi, antara tetap melayani atau tetap konsisten.

Kalau memilih AP, permintaan data akan selalu dilayani, dengan risiko data antar node sempat tidak sinkron. Ini pilihan yang wajar untuk layanan yang tidak boleh berhenti, seperti feed media sosial atau katalog produk.

Kalau memilih CP, permintaan baca maupun tulis akan ditolak saat tidak bisa dipastikan datanya konsisten. Ini pilihan untuk hal yang kesalahannya mahal, seperti saldo atau pencatatan transaksi. Setelah partisi selesai dan sistem pulih, biasanya ada proses auto healing yang menyamakan data kembali. Namun selama partisi berlangsung, keputusan desainnya tetap harus diambil lebih dulu.

---

## 10. Konsistensi data

Strong consistency berarti hasil baca selalu sama dengan kondisi terakhir yang ditulis. Kalau kamu transfer uang lalu langsung mengecek saldo, angka yang muncul harus sudah memperhitungkan transfer itu. Untuk perbankan, ini wajib, karena selisih angka berarti masalah nyata.

Eventual consistency berarti sistem mungkin sempat menampilkan data lama, tetapi pada akhirnya semua node akan menyamakan diri. Jumlah suka di sebuah unggahan bisa terlambat beberapa detik, dan hampir tidak ada yang peduli. Media sosial memilih model ini karena kecepatan dan ketersediaan lebih penting daripada ketepatan detik per detik.

Pilihan di antara keduanya bergantung pada seberapa mahal sebuah ketidaksinkronan bagi pengguna.

---

## 11. Tantangan

Latency dan throughput adalah dua hal yang sering tertukar. Latency berbicara tentang berapa lama satu permintaan diproses. Throughput berbicara tentang berapa banyak permintaan yang bisa diselesaikan dalam satuan waktu. Sistem bisa punya throughput besar tetapi latency tinggi kalau antreannya panjang.

Keamanan menjadi lebih rumit karena permukaan serangannya bertambah. DDoS membanjiri layanan dengan permintaan palsu sampai kewalahan. Pada sistem terdesentralisasi ada pula Sybil attack, yaitu serangan yang membuat banyak identitas palsu sekaligus untuk mendominasi pengaruh, suara, atau reputasi jaringan. Nama Sybil sendiri diambil dari buku tahun 1973 tentang seorang perempuan dengan gangguan identitas.

Manajemen node juga tidak sederhana. Semakin banyak mesin, semakin banyak konfigurasi, pemantauan, dan pemulihan yang harus diurus. Pada sistem terdesentralisasi muncul persoalan tambahan berupa governance, yaitu siapa yang berhak mengubah aturan main. Dalam blockchain, perdebatan ini bisa berujung pada fork, ketika sebagian peserta memilih aturan yang berbeda dan jaringan terbelah.

---

## 12. Tren masa depan

Cloud-native dengan Kubernetes dan Docker sudah menjadi cara standar menjalankan aplikasi terdistribusi. Edge dan fog computing memindahkan sebagian pemrosesan lebih dekat ke pengguna, sehingga latency turun dan penggunaan bandwidth jaringan lebih hemat.

Web3 dan DeFi melanjutkan gagasan desentralisasi ke ranah keuangan dan kepemilikan digital, meski tantangan tata kelola dan keamanannya masih terbuka.

Federated learning membawa ide distribusi ke dunia kecerdasan buatan. Model dilatih di banyak perangkat, dan yang dikirim ke server hanya pembaruan modelnya, sementara data aslinya tetap tinggal di perangkat pengguna. Pendekatan ini menarik untuk data sensitif seperti rekam medis atau foto pribadi.

---

## Ringkasan

Sistem terdistribusi membagi beban kerja ke banyak mesin agar mampu menangani skala besar dan tetap hidup saat ada komponen yang gagal. Sistem terdesentralisasi membagi kontrol ke banyak pihak agar tidak ada satu titik yang bisa mengendalikan atau melumpuhkan semuanya. Keduanya saling terkait dan menjadi fondasi teknologi modern, tetapi fokusnya berbeda dan sebaiknya tidak dicampur.

---

## Catatan

Ada dua contoh di slide yang perlu dibaca dengan hati-hati. Apache Cassandra umumnya dikategorikan sebagai AP dengan konsistensi yang bisa diatur, bukan CP. Contoh CP yang lebih tidak diperdebatkan adalah HBase, ZooKeeper, atau etcd. MongoDB juga sering disebut AP di slide, padahal klasifikasinya bergantung pada konfigurasi replica set dan read concern yang dipakai.

Untuk ujian, sebaiknya ikuti penjelasan dosen. Namun pahami juga bahwa pengelompokan ini punya nuansa, karena banyak basis data modern menyediakan konsistensi yang bisa diatur per operasi.

---

## Referensi

- Slide perkuliahan Pertemuan 1, "Pengenalan Sistem Terdistribusi dan Terdesentralisasi", Dr. Bambang Purnomosidi D. P., FTI UTDI.
- Tanenbaum, A. S., dan van Steen, M. (2007). Distributed Systems: Principles and Paradigms.
- Brewer, E. (2000). Towards Robust Distributed Systems.
