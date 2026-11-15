# Ringkasan Materi Minggu ke-5: Fault Tolerance

Mata kuliah: Sistem Terdistribusi dan Terdesentralisasi<br>
Topik: Fault Tolerance<br>
Dosen: Dr. Bambang Purnomosidi D. P.<br>
Kode: FIF25002T | SKS: 3 | Prodi: Informatika, FTI UTDI

---

## Tujuan pembelajaran

- Memahami dan mampu menjelaskan mengapa _fault tolerance_ diperlukan pada sistem terdistribusi dan terdesentralisasi
- Memahami dan mampu menjelaskan pengertian dari _fault tolerance_ serta _fault tolerant systems_
- Memahami dan mampu menjelaskan berbagai jenis _fault_ dalam suatu sistem teknologi informasi
- Memahami dan mampu menjelaskan klasifikasi _fault_ berdasarkan sumbernya
- Memahami berbagai _design patterns_ untuk mencapai _fault tolerance_ serta bisa mengaplikasikannya menggunakan Python
- Memahami bagaimana berbagai bahasa pemrograman serta peranti pengembangan mengimplementasikan _fault tolerance_

---

## 1. Pengertian fault tolerance

<p align="justify"><strong><em>Fault tolerance adalah kemampuan suatu sistem untuk mempertahankan operasional sistem dengan tepat meskipun terjadi kegagalan atau kesalahan pada satu atau lebih komponennya.</em></strong></p>

<p align="justify">Ada dua bagian yang perlu dibaca dengan hati-hati. Pertama, sistem harus tetap beroperasi, artinya layanan tidak berhenti ketika satu komponen bermasalah. Kedua, operasional itu harus tetap tepat, artinya jawaban yang diberikan tetap benar. Sistem yang masih menerima permintaan tetapi mengembalikan data yang salah tidak disebut <em>fault tolerant</em>. Ia hanya gagal dengan cara yang lebih sulit terlihat.</p>

<p align="justify">Kemampuan ini penting untuk sistem dengan <strong><em>high availability</em></strong>, <strong><em>mission critical</em></strong>, atau <strong><em>life-critical system</em></strong>. Semakin besar kerugian yang muncul ketika layanan berhenti, semakin tinggi tuntutan terhadap kemampuan ini, dan semakin mahal pula ongkos untuk membangunnya.</p>

---

## 2. Arti penting pada sistem terdistribusi dan terdesentralisasi

<p align="justify">Alasan utama <strong><em>fault tolerance</em></strong> menjadi persoalan pokok karena sistem terdistribusi dan terdesentralisasi mempunyai banyak komponen.</p>

<p align="justify">Dengan banyak komponen, jumlah hal yang bisa salah juga bertambah. Koneksi antar komponen yang bisa menjadi masalah. Dua komponen bisa saja sama-sama sehat, tetapi hubungan di antara keduanya yang bermasalah, dan dari sudut pandang pengguna hasilnya tetap sama, yaitu layanan tidak bisa dipakai.</p>

<p align="justify">Di sisi lain, tuntutan pengguna tidak menurun. Semakin kritis suatu aplikasi, semakin tinggi tuntutan agar aplikasi tersebut tetap berfungsi dengan baik. Pertemuan antara dua kenyataan itu, komponen yang banyak dan tuntutan yang tinggi, menghasilkan kebutuhan akan <strong><em>high availability</em></strong> yang harus diantisipasi sejak awal. Bukan sesuatu yang ditambahkan setelah sistem terbukti sering tumbang.</p>

---

## 3. Jenis fault

<p align="justify"><strong><em>fault</em></strong> dibagi menjadi empat jenis. Pembagian ini berguna karena masing-masing jenis menuntut penanganan yang berbeda, terutama pada dua jenis terakhir yang tidak bisa diselesaikan dengan cara yang sama seperti dua jenis pertama.</p>

<p align="center">Tabel 1: Jenis fault</p>

<table align="center">
  <thead>
    <tr>
      <th>Jenis</th>
      <th>Penjelasan</th>
      <th>Contoh</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong><em>Transient fault</em></strong></td>
      <td>Bersifat sementara, muncul sebentar lalu hilang dengan sendirinya</td>
      <td>Petir</td>
    </tr>
    <tr>
      <td><strong><em>Intermittent fault</em></strong></td>
      <td>Muncul dan hilang secara berulang</td>
      <td><em>Overheat</em> pada perangkat keras</td>
    </tr>
    <tr>
      <td><strong><em>Permanent fault</em></strong></td>
      <td>Bersifat tetap, tidak hilang sampai komponen diperbaiki atau diganti</td>
      <td>Komponen yang rusak menetap</td>
    </tr>
    <tr>
      <td><strong><em>Byzantine fault</em></strong></td>
      <td>Komponen memberikan hasil acak atau tidak konsisten</td>
      <td>Node yang tetap menjawab tetapi jawabannya berbeda-beda</td>
    </tr>
  </tbody>
</table>

<p align="justify"><em>Transient fault</em> paling mudah ditangani karena sifatnya sementara. Gangguan yang hilang sendiri biasanya cukup ditangani dengan mengulang operasi pada waktu berikutnya, tanpa perlu mengganti apa pun.</p>

<p align="justify"><em>Intermittent fault</em> lebih merepotkan daripada yang terlihat. Karena muncul dan hilang berulang, pemeriksaan yang dilakukan pada saat komponen sedang sehat tidak menemukan apa pun. Contoh <em>overheat</em> pada perangkat keras menggambarkan polanya dengan jelas. Perangkat bekerja normal, lalu melambat atau berhenti ketika suhunya melewati batas, kemudian normal kembali setelah dingin, dan kejadian yang sama terulang di lain waktu.</p>

<p align="justify"><em>Permanent fault</em> menuntut pendekatan yang berbeda sama sekali. Mengulang percobaan tidak akan menolong karena penyebabnya tidak hilang. Yang dibutuhkan adalah pengalihan ke komponen lain yang masih berfungsi, atau penggantian komponen yang rusak.</p>

<p align="justify"><em>Byzantine fault</em> adalah yang paling sulit. Komponennya tidak diam dan tidak melaporkan kesalahan. Ia tetap menjawab, tetapi jawabannya acak atau tidak konsisten dengan komponen lain. Karena tidak ada laporan kesalahan yang bisa dipegang, sistem harus bisa menyimpulkan sendiri mana jawaban yang layak dipercaya. Slide menautkan bagian ini ke tulisan pengantar mengenai <em>Byzantine Fault Tolerance</em> sebagai bahan bacaan lanjutan.</p>

---

## 4. Klasifikasi fault berdasarkan sumber

<p align="justify">Kalau bagian sebelumnya melihat bagaimana <strong><em>fault</strong></em> berperilaku, bagian ini melihat dari mana ia datang. klasifikasi fault dibagi menjadi tiga sumber.</p>

<p align="center">Tabel 2: Klasifikasi fault berdasarkan sumber</p>

<table align="center">
  <thead>
    <tr>
      <th>Sumber</th>
      <th>Contoh</th>
      <th>Yang perlu dipahami</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong><em>Hardware fault</em></strong></td>
      <td><em>Disk crash</em>, <em>power failure</em></td>
      <td>Berasal dari perangkat fisik, sifatnya konkret dan umumnya terlihat jelas</td>
    </tr>
    <tr>
      <td><strong><em>Software fault</em></strong></td>
      <td><em>Bug</em>, <em>memory leak</em>, <em>race condition</em></td>
      <td>Berasal dari program, sering baru muncul pada kondisi atau beban tertentu</td>
    </tr>
    <tr>
      <td><strong><em>Communication fault</em></strong></td>
      <td><em>Packet loss</em>, <em>network partition</em></td>
      <td>Berasal dari jalur penyampaian pesan antar komponen</td>
    </tr>
  </tbody>
</table>

<p align="justify"><em>Hardware fault</em> adalah yang paling mudah dibayangkan. Disk yang mati atau catu daya yang putus membuat sebagian sistem kehilangan komponennya secara nyata. Penanganannya juga paling jelas, yaitu menyediakan komponen pengganti dan memastikan data tidak hanya tersimpan di satu tempat.</p>

<p align="justify"><em>Software fault</em> punya watak yang berbeda karena kesalahannya ada di dalam logika program. <em>Bug</em> membuat sebagian jalur kode berperilaku keliru. <em>Memory leak</em> membuat pemakaian memori naik terus sampai sistem kehabisan sumber daya. <em>Race condition</em> membuat hasil program bergantung pada urutan kejadian yang tidak pasti, sehingga kesalahannya hanya muncul kalau waktunya kebetulan bertabrakan.</p>

<p align="justify"><em>Communication fault</em> menyangkut pesan yang berpindah antar komponen. <em>Packet loss</em> membuat sebagian pesan tidak sampai. <em>Network partition</em> membuat jaringan terbelah sehingga dua kelompok komponen tidak bisa saling menghubungi, padahal masing-masing kelompok masih hidup. Jenis ini penting karena gejalanya mirip dengan komponen yang mati, sehingga sistem harus bisa membedakan atau setidaknya menyiapkan penanganan untuk kedua kemungkinan itu.</p>

---

## 5. Teknik untuk mencapai fault tolerance

<p align="justify">Teknik-teknik yang dipakai untuk mencapai <strong><em>fault tolerance</em></strong>, dengan sumber dari <strong><em>Design Gurus</em></strong>. Teknik-teknik itulah yang kemudian dibahas satu per satu pada slide-slide berikutnya.</p>

<p align="justify">Urutan pembahasannya sekaligus menunjukkan dua lapisan yang berbeda. Sebagian teknik bekerja pada tingkat arsitektur, yaitu mengatur bagaimana komponen disusun dan bagaimana beban dibagi. Sebagian lainnya bekerja pada tingkat operasional, yaitu mengatur apa yang dilakukan ketika ada komponen yang bermasalah atau ketika sebuah panggilan gagal.</p>

<p align="justify">Redundansi dan replikasi, <strong><em>load balancing</em></strong>, <strong><em>checkpointing</em></strong> dan <strong><em>rollback recovery</em></strong>, <strong><em>heartbeat</em></strong> dan <strong><em>failure detection</em></strong>, pengelolaan <strong><em>retry</em></strong> dengan pustaka tenacity, <strong><em>circuit breaker</em></strong>, <strong><em>consensus</em></strong> dan <strong><em>coordination</em></strong>, serta <strong><em>actor model</em></strong> digunakan untuk supervisi proses.</p>

---

## 6. Redundansi dan replikasi

<p align="justify">Prinsipnya sederhana. <em>Resource</em> tidak hanya satu, tetapi direplikasi ke berbagai node, dan dengan begitu terjadi redundansi. Tujuan akhirnya adalah supaya tidak terjadi <strong><em>single point of failure</em></strong>, yaitu satu komponen yang ketika ia mati seluruh sistem ikut mati.</p>

<p align="justify">Materi menutup bagian ini dengan rujukan ke materi minggu sebelumnya, yaitu bagaimana menjaga salinan-salinan tetap memiliki data yang sama. Menambah jumlah salinan memang mengurangi risiko kehilangan layanan, tetapi sekaligus menambah persoalan konsistensi antar salinan.</p>

---

## 7. Load balancing

<p align="justify"><strong>Load balancing</strong> sebagai proses untuk melakukan <em>scaling</em> aplikasi menjadi lebih dari satu <em>instance</em>, dengan catatan bahwa satu <em>instance</em> dalam konteks ini berarti satu proses aplikasi penuh yang aktif.</p>

<p align="justify">Prosesnya dilakukan dengan menggunakan <em>load balancer</em> seperti HAProxy atau nginx, yang membagi permintaan ke beberapa <strong><em>instance</em></strong> dari aplikasi. Ketika ada lebih dari satu <strong><em>instance</em></strong> yang melayani, matinya satu <strong><em>instance</em></strong> tidak langsung berarti layanan mati, karena permintaan berikutnya bisa diarahkan ke <strong><em>instance</em></strong> lain yang masih aktif.</p>

<p align="justify">Materi juga menautkan pembahasan rinci mengenai penyiapan <strong><em>load balancing</em></strong> pada berkas terpisah, yaitu `05-web-app-py-blacksheep-load-balancing-nginx.pdf`. Berkas itulah yang dipakai untuk melihat penerapannya secara teknis, sementara slide hanya menempatkan <strong><em>load balancing</em></strong> sebagai salah satu bagian dari upaya menjaga layanan tetap tersedia.</p>

---

## 8. Checkpointing dan rollback recovery

<p align="justify"><strong><em>Checkpointing</em></strong> adalah proses menyimpan status sistem secara berkala ke media penyimpan. <strong><em>Rollback recovery</em></strong> adalah proses mengembalikan sistem ke <strong><em>checkpoint</em></strong> yang tercatat sebelumnya setelah terjadi kegagalan.</p>

<p align="justify">Manfaat gabungan keduanya yaitu memungkinkan sistem pulih dari kesalahan dengan memulai ulang dari status yang tersimpan, bukan dari awal. Tujuannya meminimalkan kehilangan data dan waktu pemrosesan.</p>

---

## 9. Heartbeat dan failure detection

<p align="justify"><strong><em>Heartbeat</em></strong> sebagai sinyal periodik yang dikirim antar komponen dalam suatu sistem untuk menunjukkan bahwa komponen tersebut masih aktif. <strong><em>Failure detection</em></strong> kemudian dijelaskan sebagai proses penggunaan <strong><em>heartbeat</em></strong> itu untuk menentukan apakah suatu komponen telah gagal.</p>

<p align="justify">Cara kerjanya jika komponen pemantau tidak menerima <strong><em>heartbeat</em></strong> dari komponen lain dalam jangka waktu tertentu, komponen tersebut diasumsikan telah gagal dan tidak responsif. Dengan begitu, sistem tidak perlu menunggu pengguna melapor, karena tanda-tanda awal sudah terbaca dari sinyal yang berhenti datang.</p>

<p align="justify">Pola ini krusial untuk membangun sistem terdistribusi yang andal, karena memungkinkannya deteksi dan penanganan kegagalan secara tepat waktu. Deteksi yang cepat membuat tindakan lanjutan, seperti pengalihan beban atau penggantian komponen, bisa dilakukan sebelum dampaknya melebar.</p>

<p align="justify">Untuk penerapan sederhana, slide merujuk pada skrip `check-server.py` yang dipakai untuk pengecekan komponen. Catatan penting bahwa untuk diagnostik lebih lanjut diperlukan program yang lebih kompleks, sehingga skrip sederhana itu lebih tepat dipahami sebagai titik awal, bukan solusi lengkap.</p>

---

## 10. Mengelola retry dengan pustaka tenacity

<p align="justify"><strong>Tenacity</strong> (https://github.com/jd/tenacity) sebagai alat untuk mengelola proses yang memerlukan keterkaitan dengan komponen di luar aplikasi. Alasannya yaitu karena selalu ada kemungkinan kegagalan untuk koneksi dengan komponen di luar aplikasi.</p>

<p align="justify">Komponen di luar aplikasi bisa berupa basis data, layanan lain, atau sistem milik pihak ketiga. Semuanya berada di luar kendali penuh aplikasi, sehingga gangguan kecil pada koneksi bukan hal yang bisa dihindari sepenuhnya. Di titik itulah pengelolaan percobaan ulang menjadi penting, supaya gangguan yang bersifat sementara tidak langsung terlihat sebagai kegagalan oleh pengguna.</p>

---

## 11. Circuit breaker

<p align="justify"><em>Circuit breaker</em> sebagai pola desain yang digunakan dalam <em>microservices</em> untuk mencegah kegagalan berantai dengan memblokir sementara panggilan ke layanan yang gagal. Pola ini bertindak seperti mesin keadaan dengan tiga status, yaitu <em>closed</em>, <em>open</em>, dan <em>half-open</em>.</p>

<p align="center">Tabel 3: Tiga status circuit breaker</p>

<table align="center">
  <thead>
    <tr>
      <th>Status</th>
      <th>Perilaku</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong><em>Closed</em></strong></td>
      <td>Permintaan diizinkan dan dipantau untuk kegagalan</td>
    </tr>
    <tr>
      <td><strong><em>Open</em></strong></td>
      <td>Permintaan selanjutnya segera ditolak, dan layanan diberi waktu untuk pulih</td>
    </tr>
    <tr>
      <td><strong><em>Half-open</em></strong></td>
      <td>Sejumlah permintaan terbatas diizinkan untuk menguji apakah layanan telah pulih</td>
    </tr>
  </tbody>
</table>

<p align="justify">Dalam status <em>closed</em>, permintaan diizinkan dan dipantau untuk kegagalan. Jika ambang batas kegagalan terpenuhi, <em>circuit breaker</em> beralih ke status <em>open</em>, segera menolak permintaan selanjutnya, dan memberi layanan waktu untuk pulih. Setelah batas waktu, <em>circuit breaker</em> memasuki status <em>half-open</em>, memungkinkan sejumlah permintaan terbatas untuk menguji apakah layanan telah pulih. Jika permintaan pengujian berhasil, sirkuit kembali ke status <em>closed</em>. Jika gagal, <em>circuit breaker</em> kembali ke status <em>open</em>.</p>

---

## 12. Consensus dan coordination

<p align="justify"><strong><em>Consensus</em></strong> adalah suatu proses dan pada proses tersebut di beberapa node menyepakati satu nilai, status, atau urutan operasi, bahkan ketika terjadi kegagalan dan penundaan jaringan. <strong><em>Coordination</em></strong> adalah praktik yang lebih luas dalam mengelola interaksi antar node untuk memastikan mereka bekerja sama secara efektif dan konsisten.</p>

<p align="justify">Hubungan keduanya yaitu bahwa <em>coordination</em> seringkali melibatkan <em>consensus</em> sebagai komponen kunci. <em>Consensus</em> dapat dipahami sebagai mekanisme pengambilan keputusan, sedangkan <em>coordination</em> mencakup pengaturan yang lebih luas di sekitarnya.</p>

<p align="justify">Tiga contoh yang memperlihatkan penerapannya di sistem nyata.</p>

<p align="justify">Contoh pertama adalah <em>database leader election</em>. Dalam kluster basis data dengan replikasi, pemimpin harus dipilih untuk menangani operasi tulis. Algoritma <em>consensus</em> seperti Raft dapat digunakan untuk keperluan pemilihan itu.</p>

<p align="justify">Contoh kedua adalah pembaruan berkas di penyimpanan awan. Ketika mengubah berkas dalam layanan seperti Google Drive, sistem menggunakan <em>consensus</em> untuk memastikan perubahan di-<em>commit</em> ke mayoritas server. Begitu mayoritas setuju, pembaruan dianggap final dan kemudian direplikasi ke semua server.</p>

<p align="justify">Contoh ketiga adalah mata uang <em>kripto</em> dan <em>blockchain</em>. Teknologi <em>blockchain</em> menggunakan <em>consensus</em> untuk validasi dan persetujuan transaksi.</p>

---

## 13. Actor model untuk supervisi proses

<p align="justify">Bagian ini ditampilkan materi dalam dua gambar, yang keduanya merujuk pada tulisan pengantar mengenai <em>actor model</em>. Gambar pertama memperlihatkan gagasan dasarnya, sedangkan gambar kedua memperlihatkan bagaimana pola itu dipakai untuk supervisi.</p>

<p align="justify">Gagasan dasarnya adalah menyusun program dari sejumlah aktor yang masing-masing memiliki keadaan sendiri dan berkomunikasi lewat pesan, bukan lewat memori bersama. Karena setiap aktor terpisah dari yang lain, kesalahan pada satu aktor tidak dengan sendirinya merusak keadaan aktor lain. Pemisahan inilah yang membuat kegagalan bisa dikurung pada satu bagian.</p>

<p align="justify">Di atas pemisahan itu dipasang mekanisme supervisi. Sebagian aktor bertugas mengawasi aktor lain, sehingga ketika ada aktor yang gagal, ada pihak yang mengambil keputusan tentang apa yang harus dilakukan terhadapnya. Dengan begitu penanganan kegagalan tidak dilakukan di sembarang tempat, tetapi pada tingkat yang memang disiapkan untuk itu.</p>

<p align="justify">Dua gambar pada slide bersumber dari tulisan berjudul <em>Introduction to Actor Model</em> di Medium dan dari <em>System Design Newsletter</em> yang juga membahas <em>actor model</em>. Keduanya dipakai untuk menunjukkan susunan aktor beserta hubungan pengawasan di antara mereka.</p>

---

## 14. Implementasi pada berbagai bahasa dan peranti

<p align="justify"><strong>Fault tolerance</strong> di beberapa ekosistem. Ada dua jalur yang terlihat jelas, yaitu bahasa yang menyediakan mekanismenya sejak awal dan bahasa yang mengandalkan pustaka pihak ketiga.</p>

<p align="justify"><strong>Erlang dan Elixir</strong> mempunyai mekanisme <em>builtin</em> untuk <em>actor model</em> dan <em>Supervisor-Worker</em>. Keduanya tidak perlu pustaka tambahan untuk memperoleh pola pengawasan, karena mekanisme itu sudah menjadi bagian dari bahasa dan ekosistemnya.</p>

<p align="justify"><strong>Pony</strong>Bahasa pemrograman yang memang mengkhususkan diri pada <em>actor model</em>, dengan tautan ke https://www.ponylang.io. Berbeda dari dua bahasa sebelumnya, Pony menempatkan <em>actor model</em> sebagai inti dari cara bahasa itu dirancang.</p>

<p align="justify">Bahasa pemrograman lain biasanya menggunakan pustaka pihak ketiga. Untuk <strong>Python</strong>, contohnya Xoscar (https://xoscar.dev), tenacity, <em>circuit breaker</em>, dan lain-lain. Untuk <strong>Java</strong>, slide menyebut Apache Pekko untuk <em>actor model</em> dan <em>Supervisor-Worker</em>, serta Resilience4j (https://github.com/resilience4j/resilience4j) untuk <em>fault tolerance</em>, dan lain-lain.</p>

<p align="justify">Untuk mencari berbagai pustaka <em>fault tolerance</em> di berbagai bahasa pemrograman, misalnya lewat npm untuk JavaScript dan crates.io untuk Rust.</p>

---

## Ringkasan

<p align="justify"><strong>Fault tolerance adalah kemampuan sistem mempertahankan operasional yang tepat meskipun terjadi kegagalan pada satu atau lebih komponennya.</strong> Kemampuan ini dibutuhkan oleh sistem dengan <em>high availability</em>, <em>mission critical</em>, dan <em>life-critical system</em>, dan menjadi makin penting pada sistem terdistribusi karena jumlah komponennya banyak serta koneksi antar komponen bisa menjadi masalah.</p>

<p align="justify"><strong>Fault</strong> yang harus dihadapi bermacam-macam. Ada yang sementara seperti gangguan akibat petir, ada yang muncul dan hilang berulang seperti <em>overheat</em> pada perangkat keras, ada yang menetap sampai komponennya diganti, dan ada yang paling sulit karena komponennya tetap menjawab tetapi dengan hasil yang acak atau tidak konsisten. Sumbernya pun berbeda, yaitu perangkat keras, perangkat lunak, dan jalur komunikasi.</p>

<p align="justify">Untuk menghadapinya, tersedia sejumlah teknik. Redundansi dan replikasi menghindarkan <em>single point of failure</em>. <em>Load balancing</em> menjalankan aplikasi pada lebih dari satu <em>instance</em>. <em>Checkpointing</em> dan <em>rollback recovery</em> memungkinkan pemulihan dari status tersimpan, bukan dari awal. <em>Heartbeat</em> dan <em>failure detection</em> membuat kegagalan bisa diketahui lebih cepat. Pustaka seperti tenacity membantu mengelola percobaan ulang pada koneksi ke komponen luar. <em>Circuit breaker</em> mencegah kegagalan berantai dengan memblokir sementara panggilan ke layanan yang gagal. <em>Consensus</em> dan <em>coordination</em> menjaga banyak node tetap mengambil keputusan yang sama, seperti pada pemilihan pemimpin basis data, pembaruan berkas di penyimpanan awan, dan validasi transaksi <em>blockchain</em>. Sementara <em>actor model</em> dengan supervisinya menyusun program agar kegagalan terkurung pada satu bagian.</p>

<p align="justify"><strong><em>Inti materi ini adalah bahwa kegagalan bukan hal yang diharapkan tidak terjadi, melainkan hal yang penanganannya sudah disiapkan.</em></strong> Sistem yang baik tidak berjanji bebas dari kegagalan, tetapi memastikan bahwa ketika kegagalan datang, dampaknya terbatas dan pemulihannya sudah punya jalan.</p>

---

## Referensi

- Slide perkuliahan Pertemuan 5, "Fault Tolerance", Dr. Bambang Purnomosidi D. P., FTI UTDI.
- Slide perkuliahan Pertemuan 4, "Konsistensi dan Replikasi pada Sistem Terdistribusi", untuk bagian redundansi dan replikasi, Dr. Bambang Purnomosidi D. P., FTI UTDI.
- Berkas `05-web-app-py-blacksheep-load-balancing-nginx.pdf`, untuk pembahasan rinci _load balancing_.
- Skrip `check-server.py`, untuk contoh pengecekan komponen.
- Skrip `check-retry.py`, untuk contoh pengelolaan _retry_.
- Design Gurus. _Design Patterns: 5 Expert Techniques for Boosting Fault Tolerance in Distributed Systems_. https://www.designgurus.io/kb/design-patterns-5-expert-techniques-for-boosting-fault-tolerance-in-distributed-systems
- Medium. _Byzantine Fault Tolerance_. https://medium.com/coinmonks/byzantine-fault-tolerance-222a8134be7
- Medium. _Introduction to Actor Model_. https://medium.com/@nguyenthanhquang.cse/introduction-to-actor-model-2c82d25d1b83
- System Design Newsletter. _Actor Model_. https://newsletter.systemdesign.one/p/actor-model
- Tenacity. _Retrying library for Python_. https://github.com/jd/tenacity
- Pony. _Pony programming language_. https://www.ponylang.io
- Xoscar. _Xoscar actor framework_. https://xoscar.dev
- Resilience4j. _Fault tolerance library for Java_. https://github.com/resilience4j/resilience4j
- Tanenbaum, A. S., dan van Steen, M. (2007). _Distributed Systems: Principles and Paradigms_.
