# Ringkasan Materi Minggu ke-3: Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

Mata kuliah: Sistem Terdistribusi dan Terdesentralisasi<br>
Topik: Sinkronisasi dan Koordinasi pada Sistem Terdistribusi<br>
Dosen: Dr. Bambang Purnomosidi D. P.<br>
Kode: FIF25002T | SKS: 3 | Prodi: Informatika, FTI UTDI

---

## Tujuan pembelajaran

- Mampu menjelaskan mengapa sinkronisasi diperlukan untuk koordinasi pada sistem terdistribusi
- Mampu menjelaskan berbagai tantangan sinkronisasi pada sistem terdistribusi
- Mampu menjelaskan sinkronisasi waktu fisik beserta algoritma dan protokolnya
- Mampu menjelaskan sinkronisasi waktu logis beserta algoritma terkait
- Mampu menjelaskan masalah mutual exclusion dan berbagai algoritma penyelesaiannya
- Mampu menjelaskan mengapa pemilihan koordinator diperlukan dan bagaimana masalah itu dipecahkan
- Mampu memberikan contoh studi kasus nyata di industri

---

## 1. Apa itu sinkronisasi

<p align="justify"><strong>Sinkronisasi adalah koordinasi proses, data, dan sumber daya di berbagai <em>node</em> agar operasi berjalan konsisten dan andal.</strong> Sistem terdistribusi terdiri atas banyak mesin yang masing-masing menjalankan prosesnya sendiri, memiliki memori sendiri, dan memiliki jam sendiri. Tanpa mekanisme koordinasi, node-node itu bisa saling menginjak pekerjaan yang sama.</p>

<p align="justify">Sinkronisasi mengatasi tiga hal sekaligus, yaitu menjaga konsistensi data, mengelola proses yang berjalan bersamaan, dan mencapai perilaku sistem yang koheren di seluruh jaringan. Dengan mekanisme yang efektif, sistem dapat beroperasi lancar, mencegah konflik data (<em>data race</em>), serta menyediakan layanan yang andal dan efisien.</p>

<p align="justify">Contoh yang dipakai di kelas adalah aplikasi e-commerce. Produk X memiliki stok 10, lalu dua pengguna membeli satu unit pada saat yang hampir bersamaan.</p>

- User A membaca stok produk X, hasilnya 10, lalu dianggap aman dan proses dilanjutkan
- User B membaca stok produk X, hasilnya juga 10, lalu juga dianggap aman
- Proses A membeli X dan mengurangi stok menjadi 9
- Proses B membeli X dan mengurangi stok menjadi 9

<p align="justify">Kenyataannya ada dua pembelian, sehingga stok seharusnya 8. Yang tercatat di perangkat lunak tetap 9. <strong><em>Satu pembaruan hilang karena dua proses membaca nilai yang sama sebelum salah satunya menulis.</em></strong> Inilah bentuk paling sederhana dari masalah yang diselesaikan oleh sinkronisasi.</p>

---

## 2. Data race dan race condition

<p align="justify">Dua istilah ini sering dipakai bergantian, padahal tingkatnya berbeda. <strong>Data race</strong> berada di level rendah, yaitu akses memori bersamaan oleh dua thread atau lebih. <strong>Race condition</strong> berada di level lebih tinggi, yaitu kebenaran hasil bergantung pada urutan eksekusi yang tidak dapat diprediksi.</p>

<p align="center">Tabel 1: Perbandingan data race dan race condition</p>

<table align="center">
  <thead>
    <tr>
      <th>Fitur / parameter</th>
      <th>Data race</th>
      <th>Race condition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Fokus utama</td>
      <td>Akses memori secara bersamaan (<em>concurrent memory access</em>)</td>
      <td>Urutan dan waktu eksekusi instruksi (<em>timing &amp; execution sequence</em>)</td>
    </tr>
    <tr>
      <td>Level</td>
      <td><em>Low-level</em> (instrumen CPU / akses memori fisik)</td>
      <td><em>High-level</em> (alur logika aplikasi / <em>business logic</em>)</td>
    </tr>
    <tr>
      <td>Syarat terjadi</td>
      <td>Minimal 2 thread mengakses memori yang sama; minimal 1 akses berupa operasi <em>write</em>; tidak ada sinkronisasi (tanpa mutex/lock)</td>
      <td>Kebenaran program bergantung pada urutan eksekusi yang tidak bisa diprediksi</td>
    </tr>
    <tr>
      <td>Dampak utama</td>
      <td><em>Undefined behavior</em>, korupsi memori, data tidak konsisten</td>
      <td>Output salah, bug logika bisnis (saldo minus, eksekusi ganda)</td>
    </tr>
    <tr>
      <td>Solusi</td>
      <td>Mutex / lock atau tipe data <em>atomic</em></td>
      <td>Desain ulang alur kontrol (misalnya <em>atomic check-and-act</em>, <em>database transaction lock</em>)</td>
    </tr>
  </tbody>
</table>

<p align="justify">Ilustrasi data race pada slide memakai satu variabel dengan nilai awal 10. Thread 1 membaca 10 dan menghitung 11, Thread 2 juga membaca 10 dan menghitung 11, lalu keduanya menulis 11. Hasil akhirnya 11, padahal seharusnya 12 karena ada dua kenaikan.</p>

<p align="justify">Ilustrasi race condition memakai rekening bank dengan saldo $100. Thread A mengecek saldo, memutuskan penarikan $80 valid, lalu mengurangi saldo. Thread B melakukan hal yang sama sebelum pengurangan dari A terlihat. Keduanya lolos pengecekan, sehingga saldo akhirnya −$60. Inilah <em>double spending</em> yang tidak akan terjadi jika pengecekan dan pengurangan dilakukan sebagai satu operasi atomik.</p>

<p align="justify">Untuk menunjukkan sifat konkuren, slide menjalankan contoh Python berikut, yang membuat lima thread dan menunggu semuanya selesai dengan <code>join</code>.</p>

```python
import threading
import time


def task(name, delay):
    print(f"Task {name} starting...")
    time.sleep(delay)  # Simulates an I/O-bound operation
    print(f"Task {name} finished after {delay}s.")


# 1. Create thread objects
t1 = threading.Thread(target=task, args=("A", 5))
t2 = threading.Thread(target=task, args=("B", 5))
t3 = threading.Thread(target=task, args=("C", 5))
t4 = threading.Thread(target=task, args=("D", 5))
t5 = threading.Thread(target=task, args=("E", 5))

# 2. Start the threads
t1.start()
t2.start()
t3.start()
t4.start()
t5.start()

# 3. Wait for threads to finish before continuing
t1.join()
t2.join()
t3.join()
t4.join()
t5.join()

print("All tasks completed.")
```

<p align="justify">Hasil di terminal memperlihatkan kelima task mulai pada waktu yang hampir sama, tetapi urutan selesainya berubah-ubah antar eksekusi, misalnya A, B, D, E, C pada satu percobaan dan A, B, E, C, D pada percobaan lain. <strong><em>Thread membuat program berjalan bersamaan, dan justru karena itu urutan kejadiannya tidak lagi bisa diandalkan tanpa mekanisme sinkronisasi.</em></strong></p>

---

## 3. Mengapa sinkronisasi penting

<p align="justify">Sinkronisasi bukan sekadar soal menghindari <em>bug</em>. Slide menyebut sejumlah alasan yang saling berkaitan.</p>

- **Integritas data.** Tanpa sinkronisasi, <em>data race</em> dan <em>race condition</em> membuat data rusak atau tidak konsisten.
- **Sinkronisasi state.** <em>State</em> aplikasi harus sama di semua node. Contohnya <em>online multiplayer game</em> yang mengirim posisi pemain, kesehatan, dan inventaris terbaru ke semua klien, atau aplikasi web yang menyamakan isi keranjang belanja antara peramban dan basis data.
- **Koordinasi task.** Pekerjaan yang dipecah ke banyak node perlu diatur agar tidak tumpang tindih atau saling menunggu tanpa akhir.
- **Pengelolaan resource.** Sumber daya bersama seperti printer, berkas, atau baris basis data hanya boleh dipakai satu proses pada satu waktu.
- **Mendukung recovery.** Mekanisme pemulihan membutuhkan <em>state</em> yang terjaga, sehingga setelah kegagalan sistem bisa melanjutkan dari titik yang benar.
- **Utilisasi efisien.** Sinkronisasi mencegah operasi redundan yang membuang waktu dan sumber daya.
- **Load balancing.** Beban dapat dibagi merata karena tiap node tahu pekerjaan mana yang sudah diambil node lain.
- **Mencegah deadlock.** Pengaturan urutan akses sumber daya menghindarkan proses saling menunggu selamanya.
- **Operasi yang skalabel.** Semakin banyak node, semakin besar kemungkinan konflik, sehingga koordinasi harus dirancang sejak awal.

---

## 4. Deadlock

<p align="justify">Deadlock adalah keadaan ketika sekumpulan proses saling menunggu sumber daya yang dipegang proses lain, sehingga tidak ada satu pun yang bisa maju. Pada sistem terdistribusi, deadlock bisa melintasi node dan sumber daya yang berbeda.</p>

<p align="justify">Diagram di slide menampilkan tiga proses dan tiga sumber daya dalam satu jaringan terdistribusi. Process 1 memegang R1 sambil menunggu R2, Process 2 memegang R2 sambil menunggu R3, dan Process 3 memegang R3 sambil menunggu R1. Garis <em>assigned to</em> dan <em>waiting for</em> itu membentuk siklus tertutup. <strong><em>Selama siklus itu ada, tidak ada proses yang bisa melepaskan sumber dayanya, dan sistem berhenti membuat kemajuan.</em></strong></p>

<p align="justify">Deadlock biasanya ditangani dengan pencegahan lewat aturan pengurutan sumber daya, penghindaran, deteksi melalui graf tunggu, atau pemulihan dengan membatalkan salah satu proses. Pada sistem terdistribusi, deteksi menjadi lebih sulit karena tidak ada satu pihak yang melihat seluruh keadaan jaringan secara langsung.</p>

---

## 5. Tantangan sinkronisasi

<p align="justify">Menyinkronkan banyak mesin jauh lebih sulit daripada menyinkronkan thread di satu komputer. Slide merangkum tantangan utamanya sebagai berikut.</p>

<p align="center">Tabel 2: Tantangan sinkronisasi pada sistem terdistribusi</p>

<table align="center">
  <thead>
    <tr>
      <th>Tantangan</th>
      <th>Penjelasan</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><em>Network latency</em> dan <em>partitioning</em></td>
      <td>Pesan butuh waktu, dan jaringan bisa terpecah sehingga sebagian node tidak bisa berkomunikasi dengan node lain</td>
    </tr>
    <tr>
      <td>Skalabilitas</td>
      <td>Biaya koordinasi tumbuh seiring jumlah node; algoritma yang baik di 3 node bisa mahal di 300 node</td>
    </tr>
    <tr>
      <td><em>Fault-tolerant system</em></td>
      <td>Node bisa mati kapan saja, sehingga keputusan tidak boleh bergantung pada satu pihak saja</td>
    </tr>
    <tr>
      <td><em>Concurrency control</em></td>
      <td>Banyak operasi berjalan bersamaan dan harus diurutkan atau dibatasi aksesnya</td>
    </tr>
    <tr>
      <td><em>Data consistency</em></td>
      <td>Replika di beberapa node harus tetap konsisten setelah pembaruan</td>
    </tr>
    <tr>
      <td>Sinkronisasi waktu</td>
      <td>Jam tiap mesin bergeser, sehingga urutan kejadian tidak bisa hanya bersandar pada waktu fisik</td>
    </tr>
  </tbody>
</table>

---

## 6. Sinkronisasi waktu fisik

<p align="justify"><strong>Sinkronisasi waktu fisik adalah proses penyelarasan <em>real-time clock</em> atau jam nyata dari sistem terdistribusi dengan standar umum</strong>, agar semua node memiliki acuan waktu yang konsisten. Sinkronisasi ini penting untuk mengorelasikan log dari banyak mesin, menjadwalkan <em>event</em>, dan menjaga konsistensi sistem secara keseluruhan.</p>

<p align="justify">Pelaksanaannya bergantung pada protokol dan pada sumber acuan berbasis perangkat keras, yaitu <em>atomic clock</em> dan/atau GPS yang disinkronkan dengan <em>atomic clock</em>. Jam kuarsa biasa pada komputer tidak cukup stabil untuk menjadi acuan, karena frekuensinya bergeser mengikuti suhu dan usia perangkat.</p>

<p align="center">Tabel 3: Standar acuan waktu</p>

<table align="center">
  <thead>
    <tr>
      <th>Standar</th>
      <th>Keterangan</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>GMT (<em>Greenwich Mean Time</em>)</td>
      <td>Acuan lama berbasis posisi matahari; sudah tidak digunakan sebagai standar waktu</td>
    </tr>
    <tr>
      <td>TAI (<em>Temps Atomique International</em>)</td>
      <td>Waktu atom internasional, dihitung dari ratusan <em>atomic clock</em> di seluruh dunia; menjadi dasar UTC</td>
    </tr>
    <tr>
      <td>UTC (<em>Universal Coordinated Time</em>)</td>
      <td>Standar waktu sipil yang didefinisikan ITU dan dipakai sejak 1963; diturunkan dari TAI dengan penyesuaian detik kabisat</td>
    </tr>
    <tr>
      <td>LTC (<em>Coordinated Lunar Time</em>)</td>
      <td>Acuan waktu untuk aktivitas di bulan; masih dalam proses pengembangan</td>
    </tr>
  </tbody>
</table>

<p align="justify">UTC adalah standar yang dipakai sehari-hari, sedangkan TAI adalah hitungan detik atomik yang berjalan tanpa penyesuaian. Perbedaan keduanya saat ini sekitar 37 detik karena detik kabisat ditambahkan untuk menyesuaikan dengan rotasi bumi.</p>

---

## 7. Rantai sinkronisasi dari atomic clock sampai client

<p align="justify">Tidak ada komputer biasa yang menyimpan <em>atomic clock</em> sendiri. Waktu yang dipakai perangkat sehari-hari adalah hasil rantai sinkronisasi yang panjang.</p>

- <em>Master atomic clock</em> menjadi acuan utama
- <em>Atomic clock</em> di satelit GPS disinkronkan dengan acuan tersebut
- Satelit GPS memancarkan sinyal waktu ke bumi
- GPS dongle menerima sinyal itu dan menyediakannya ke komputer, biasanya lewat perangkat serial seperti <code>/dev/ttyACM0</code>
- Server NTP atau PTP membaca sumber waktu tersebut dan menyebarkannya ke jaringan
- Client, baik Linux, Windows, macOS, maupun telepon seluler, menyinkronkan jamnya ke server itu

<p align="justify">Sumber <em>atomic clock</em> yang dipakai saat ini antara lain sebagai berikut.</p>

- **Satelit GPS.** Kurang lebih 31 satelit GPS di orbit <em>Medium Earth Orbit</em> (MEO), dioperasikan U.S. Space Force, masing-masing membawa beberapa <em>atomic clock</em>
- **DSAC (<em>Deep Space Atomic Clock</em>).** Diluncurkan NASA pada 2019 di wahana <em>Orbital Test Bed</em> pada orbit rendah bumi untuk menguji stabilitas jam atom di antariksa
- **ISS.** Misi <em>Atomic Clock Ensemble in Space</em> (ACES) menempatkan <em>cold-atom clock</em> di orbit rendah bumi
- **Ground control.** Pengelolaan <em>master clock</em> dilakukan di darat, antara lain di U.S. Naval Observatory, Washington, D.C., dan Schriever Space Force Base, Colorado

<p align="justify">Di sisi perangkat, GPS dongle murah seperti U-blox VK-172 sudah cukup untuk menerima sinyal waktu. Di Linux, perangkat itu muncul sebagai <code>/dev/ttyACM0</code> dan dibaca oleh <em>daemon</em> seperti <code>gpsd</code>. <strong><em>Rantai ini menjelaskan mengapa waktu yang "benar" bukan berasal dari komputer itu sendiri, melainkan hasil disiplin dari acuan atomik yang disebarkan lewat GPS dan protokol jaringan.</em></strong></p>

---

## 8. NTP

<p align="justify"><strong>NTP (<em>Network Time Protocol</em>)</strong> adalah protokol sinkronisasi waktu yang paling luas dipakai. Standarnya RFC 5905, dikembangkan oleh David L. Mills bersama Harlan Stenn dan Network Time Foundation, dan diperkenalkan pada 1985. NTP bekerja dalam hierarki <em>stratum</em>: stratum 0 adalah acuan fisik seperti <em>atomic clock</em> atau GPS, stratum 1 adalah server yang terhubung langsung ke acuan, stratum 2 mengambil waktu dari stratum 1, dan seterusnya.</p>

<p align="justify">Akurasi NTP bergantung pada media. Lewat internet, selisih antar mesin biasanya dalam hitungan milidetik. Di LAN, akurasinya biasanya dalam hitungan mikrodetik. Dengan <em>hardware timestamping</em> atau <em>reference clock</em> perangkat keras, akurasi sub-mikrodetik bisa dicapai.</p>

<p align="justify">Implementasi yang dipakai pada slide adalah <strong>chrony</strong>, yang terdiri atas dua program. <code>chronyd</code> adalah <em>daemon</em> yang berjalan saat <em>boot</em>, sedangkan <code>chronyc</code> adalah antarmuka baris perintah untuk memantau dan mengubah parameter saat <em>daemon</em> berjalan. chrony juga bisa berperan sebagai server NTPv4 (RFC 5905) bagi komputer lain.</p>

<p align="justify">Pada Arch Linux, pemasangannya sederhana.</p>

```bash
sudo pacman -S chrony chrony-dinit
```

<p align="justify">Slide kemudian memperlihatkan berkas layanan untuk init system dinit, yaitu <code>/etc/dinit.d/chronyd</code> yang menjalankan <code>/usr/lib/dinit/chronyd</code>, dan <code>/etc/dinit.d/chrony</code> yang menjalankan <code>chronyc</code> dengan opsi <code>waitsync</code> agar jam sudah tersinkron sebelum layanan lain yang bergantung padanya berjalan. Setelah itu layanan diaktifkan.</p>

```bash
sudo dinitctl enable chrony
# Service 'chrony' has been enabled.
# Service 'chrony' started.
```

<p align="justify">Pemeriksaan sumber waktu dilakukan dengan <code>chronyc -n sources</code>. Keluarannya menampilkan daftar server beserta <em>stratum</em>, jumlah <em>poll</em>, nilai <em>reach</em>, waktu penerimaan terakhir, dan <em>offset</em> dalam mikrodetik. Tanda <code>^*</code> menandai server yang sedang dipilih sebagai acuan.</p>

```text
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^* 240b:400b:21:cc00:25e5:c8e2:87ae:9142   2   6   17   45  +929us[-1670us] +/-  108ms
^+ 2603:1040:1801:8::14                    2   6   17   45  +935us[-1663us] +/-  102ms
^* 240b:400b:21:cc00:25e5:c8e2:87ae:9141   2   6   17   45 -2146us[-4744us] +/-   31ms
^* 240b:400b:1a:a002:ffff:4136:a295:9261   2   6   17   44 +1519us[-1079us] +/-   63ms
```

<p align="justify">Status keseluruhan dilihat dengan <code>chronyc -n tracking</code>. Contoh di slide menunjukkan sistem berjalan pada <em>stratum</em> 3 dengan <em>reference ID</em> dari salah satu server di atas, selisih waktu sistem hanya sekitar 2,75 mikrodetik dari waktu NTP, <em>RMS offset</em> 2,6 milidetik, <em>root delay</em> 122 milidetik, dan interval pembaruan 2 detik.</p>

---

## 9. PTP

<p align="justify"><strong>PTP (<em>Precision Time Protocol</em>)</strong> adalah protokol sinkronisasi waktu yang jauh lebih presisi daripada NTP, tetapi menuntut dukungan perangkat keras. PTP distandarkan oleh IEEE sebagai IEEE 1588. Versi 2002 adalah standar pertama, versi 2008 dikenal sebagai PTP versi 2 dan tidak kompatibel mundur dengan versi 2002, sedangkan versi 2019 memuat perbaikan yang tetap kompatibel dengan standar 2008.</p>

<p align="justify">Standar 2008 memperkenalkan konsep <em>profile</em>, yaitu penyesuaian PTP untuk domain tertentu. Salah satu contohnya adalah IEEE 802.1AS, adaptasi PTP yang disebut gPTP untuk <em>Audio Video Bridging</em> (AVB) dan <em>Time-Sensitive Networking</em> (TSN).</p>

<p align="justify">Implementasi yang dipakai di Linux adalah <strong>linuxptp</strong>. Proyek ini menyediakan dua program utama.</p>

- <code>ptp4l</code> menjalankan PTP sebagai <em>boundary</em>, <em>ordinary</em>, atau <em>transparent clock</em>. Opsinya antara lain <code>-i</code> untuk memilih antarmuka, <code>-E</code> untuk mekanisme <em>delay request-response</em> (E2E), <code>-P</code> untuk <em>peer delay</em> (P2P), <code>-2</code> untuk transport IEEE 802.3, dan <code>-4</code> untuk UDP IPv4
- <code>phc2sys</code> menyinkronkan <em>clock</em> sistem dengan <em>clock</em> perangkat keras PTP (<em>Physical Hardware Clock</em>). Opsi <code>-a</code> membaca <em>clock</em> dari <code>ptp4l</code> yang sedang berjalan dan menyesuaikan arah sinkronisasi secara otomatis, sedangkan <code>-r</code> menyesuaikan jam sistem dengan <code>phc2sys</code> bukan dengan <code>ptp4l</code>

<p align="justify">Pada slide, linuxptp dipasang lewat AUR Arch Linux karena belum tersedia di repositori resmi. Berkas <code>PKGBUILD</code> mengunduh sumber dari <code>https://www.nwtime.org/downloads/linuxptp/</code>, membangun dengan <code>make</code>, lalu memasang <em>binary</em> ke <code>/usr/sbin</code>. Hasil akhirnya adalah paket <code>linuxptp 4.4-1</code>.</p>

<p align="justify">Syarat penting pemakaian PTP adalah <strong>NIC harus mendukung <em>hardware timestamping</em></strong>. Kemampuan itu diperiksa dengan <code>ethtool -T</code>.</p>

```bash
sudo ethtool -T eth0
# Time stamping parameters for eth0:
# Capabilities:
#         software-transmit
#         software-receive
#         software-system-clock
# PTP Hardware Clock: none
# Hardware Transmit Timestamp Modes: none
# Hardware Receive Filter Modes: none
```

<p align="justify">Keluaran di atas menunjukkan NIC hanya mendukung <em>timestamp</em> perangkat lunak, tanpa <em>PTP Hardware Clock</em>. Pada kondisi ini PTP tetap bisa dijalankan, tetapi akurasi sub-mikrodetik tidak akan tercapai karena penandaan waktu tetap terjadi di lapisan sistem operasi, bukan di perangkat keras jaringan. <strong><em>Presisi PTP bergantung pada seberapa dekat titik penandaan waktu ke kabel, sehingga dukungan NIC sama pentingnya dengan protokolnya.</em></strong></p>

<p align="center">Tabel 4: Perbandingan ringkas NTP dan PTP</p>

<table align="center">
  <thead>
    <tr>
      <th>Aspek</th>
      <th>NTP</th>
      <th>PTP</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Standar</td>
      <td>RFC 5905 (IETF)</td>
      <td>IEEE 1588 (2002, 2008, 2019)</td>
    </tr>
    <tr>
      <td>Akurasi khas</td>
      <td>Milidetik di internet, mikrodetik di LAN</td>
      <td>Sub-mikrodetik hingga nanodetik pada jaringan lokal</td>
    </tr>
    <tr>
      <td>Perangkat keras</td>
      <td>Tidak wajib</td>
      <td>Wajib mendukung <em>hardware timestamping</em> untuk hasil terbaik</td>
    </tr>
    <tr>
      <td>Implementasi Linux</td>
      <td>chrony, ntpd</td>
      <td>linuxptp (<code>ptp4l</code>, <code>phc2sys</code>)</td>
    </tr>
    <tr>
      <td>Pemakaian umum</td>
      <td>Sinkronisasi jam server dan perangkat umum</td>
      <td>Jaringan industri, AVB/TSN, sistem kendali <em>real-time</em></td>
    </tr>
  </tbody>
</table>

---

## 10. Sinkronisasi waktu logis

<p align="justify">Waktu fisik selalu menyisakan selisih, sekalipun sudah disinkronkan. Karena itu sistem terdistribusi memakai cara lain untuk menentukan urutan kejadian, yaitu <strong>waktu logis</strong>. Waktu logis adalah metode untuk menetapkan <em>causal order of events</em>, yaitu urutan sebab akibat, dan tidak terkait dengan waktu nyata.</p>

<p align="justify">Dasar pemikirannya adalah relasi <em>happens-before</em> yang bersifat transitif. Jika A terjadi sebelum B dan B terjadi sebelum C, maka A terjadi sebelum C. Relasi inilah yang ingin ditangkap, bukan jam berapa masing-masing kejadian berlangsung.</p>

<p align="justify">Gagasan ini diperkenalkan oleh Leslie Lamport dan sudah dibahas pada materi minggu ke-2. Lamport clock memakai satu angka yang bertambah pada setiap kejadian dan disertakan pada setiap pesan. Keterbatasannya, satu angka tidak bisa membedakan kejadian yang benar-benar berhubungan dengan kejadian yang hanya kebetulan berurutan. Karena itu muncul <strong>vector clocks</strong>.</p>

---

## 11. Vector clocks

<p align="justify">Vector clock memberi setiap proses satu slot angka, sehingga ukurannya mengikuti jumlah proses. Slide membedakan <em>vector</em> yang berukuran dinamis dengan <em>array</em> yang berukuran tetap. Dengan tiga proses A, B, dan C, posisi slotnya adalah A di indeks 0, B di indeks 1, dan C di indeks 2.</p>

<p align="justify">Keadaan awal ketiganya sama, yaitu <code>[0, 0, 0]</code>. Ada dua jenis kejadian yang perlu diperhatikan, yaitu <em>internal event</em> dan <em>send/receive message</em>. Aturannya sederhana.</p>

1. Setiap kali proses mengerjakan sesuatu, angka pada slotnya sendiri bertambah satu
2. Sebelum mengirim pesan, proses menambah slotnya sendiri lalu menyertakan seluruh vektor pada pesan
3. Saat menerima pesan, proses mengambil nilai maksimum per elemen antara vektornya sendiri dan vektor pada pesan (<em>element-wise max</em>), lalu menambah slotnya sendiri karena penerimaan pesan juga sebuah kejadian

<p align="justify">Langkah pada slide berjalan sebagai berikut.</p>

<p align="center">Tabel 5: Contoh langkah vector clock pada tiga proses</p>

<table align="center">
  <thead>
    <tr>
      <th>Langkah</th>
      <th>Kejadian</th>
      <th>A</th>
      <th>B</th>
      <th>C</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Keadaan awal</td>
      <td><code>[0,0,0]</code></td>
      <td><code>[0,0,0]</code></td>
      <td><code>[0,0,0]</code></td>
    </tr>
    <tr>
      <td>1</td>
      <td>A melakukan <em>internal event</em></td>
      <td><code>[1,0,0]</code></td>
      <td><code>[0,0,0]</code></td>
      <td><code>[0,0,0]</code></td>
    </tr>
    <tr>
      <td>2</td>
      <td>A menambah slotnya lalu mengirim pesan ke B dengan vektor <code>[2,0,0]</code></td>
      <td><code>[2,0,0]</code></td>
      <td><code>[0,0,0]</code></td>
      <td><code>[0,0,0]</code></td>
    </tr>
    <tr>
      <td>3</td>
      <td>B menerima, mengambil <em>element-wise max</em> dari <code>[0,0,0]</code> dan <code>[2,0,0]</code></td>
      <td><code>[2,0,0]</code></td>
      <td><code>[2,0,0]</code></td>
      <td><code>[0,0,0]</code></td>
    </tr>
    <tr>
      <td>4</td>
      <td>B menambah slotnya lalu mengirim pesan ke C dengan vektor <code>[2,1,0]</code></td>
      <td><code>[2,0,0]</code></td>
      <td><code>[2,1,0]</code></td>
      <td><code>[0,0,0]</code></td>
    </tr>
    <tr>
      <td>5</td>
      <td>C menerima, mengambil maksimum <code>[2,1,0]</code>, lalu menambah slotnya karena bereaksi dengan <em>internal event</em></td>
      <td><code>[2,0,0]</code></td>
      <td><code>[2,1,0]</code></td>
      <td><code>[2,1,1]</code></td>
    </tr>
    <tr>
      <td>6</td>
      <td>C melakukan <em>internal event</em> lagi</td>
      <td><code>[2,0,0]</code></td>
      <td><code>[2,1,0]</code></td>
      <td><code>[2,1,2]</code></td>
    </tr>
  </tbody>
</table>

<p align="justify">Posisi akhirnya adalah A <code>[2,0,0]</code>, B <code>[2,1,0]</code>, dan C <code>[2,1,2]</code>. Perhatikan bahwa slot milik B pada vektor C bernilai 1, karena C mengetahui satu kejadian B, dan slot milik A bernilai 2, karena C mengetahui dua kejadian A yang mendahului pengiriman B.</p>

<p align="justify">Untuk membaca hasilnya dipakai aturan berikut.</p>

- A terjadi sebelum B jika <strong>semua</strong> angka pada vektor A lebih kecil atau sama dengan angka pada vektor B, dan <strong>minimal satu</strong> angka benar-benar lebih kecil
- Keduanya <strong>concurrent</strong> atau independen jika tidak semua komponen A ≤ komponen B, dan sebaliknya

<p align="justify">Dengan aturan itu, beberapa pertanyaan bisa dijawab.</p>

<p align="center">Tabel 6: Membaca relasi antar kejadian</p>

<table align="center">
  <thead>
    <tr>
      <th>Pertanyaan</th>
      <th>Perbandingan</th>
      <th>Jawaban</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Apakah <em>event</em> pertama A terjadi sebelum B?</td>
      <td>A <code>[1,0,0]</code> vs B <code>[0,0,0]</code></td>
      <td>Tidak. B <code>[0,0,0]</code> terjadi lebih dulu, karena <code>[0,0,0]</code> ≤ <code>[1,0,0]</code></td>
    </tr>
    <tr>
      <td>Bagaimana A setelah mengirim pesan dibanding reaksi B?</td>
      <td>A <code>[2,0,0]</code> vs B <code>[2,1,0]</code></td>
      <td>A lebih dulu, karena <code>[2,0,0]</code> ≤ <code>[2,1,0]</code> dan ada komponen yang lebih kecil</td>
    </tr>
    <tr>
      <td>Apakah kejadian C yang terakhir terjadi sebelum A?</td>
      <td>C <code>[2,1,2]</code> vs A <code>[2,0,0]</code></td>
      <td>Tidak. C tidak mendahului A; arah relasinya justru A → C, karena <code>[2,0,0]</code> ≤ <code>[2,1,2]</code></td>
    </tr>
    <tr>
      <td>Apakah dua kejadian pada proses berbeda bisa independen?</td>
      <td>Misalnya A <code>[1,0,0]</code> vs C <code>[0,0,1]</code></td>
      <td>Ya. Tidak ada yang ≤ yang lain, sehingga keduanya <em>concurrent</em>: tidak saling mengetahui</td>
    </tr>
  </tbody>
</table>

<p align="justify">Inti implementasinya kira-kira seperti berikut.</p>

```python
# internal event
clock[i] += 1

# send message
clock[i] += 1
kirim(pesan, clock)

# receive message
clock = [max(lokal, masuk) for lokal, masuk in zip(clock, clock_pesan)]
clock[i] += 1
```

<p align="justify">Implementasi lengkapnya ada pada berkas <code>vclocks.py</code> yang disertakan di kelas. <strong><em>Vector clock menjawab pertanyaan yang tidak bisa dijawab Lamport clock, yaitu apakah dua kejadian saling menyebabkan atau berjalan bersamaan tanpa saling tahu.</em></strong> Harganya adalah ukuran vektor yang tumbuh sebanding jumlah proses.</p>

<p align="center">Tabel 7: Posisi akhir vector clock dan maknanya</p>

<table align="center">
  <thead>
    <tr>
      <th>Proses</th>
      <th>Vektor akhir</th>
      <th>Makna</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>A</td>
      <td><code>[2,0,0]</code></td>
      <td>Dua kejadian internal, belum mengetahui kejadian B dan C</td>
    </tr>
    <tr>
      <td>B</td>
      <td><code>[2,1,0]</code></td>
      <td>Mengetahui dua kejadian A dan satu kejadian sendiri, belum tahu C</td>
    </tr>
    <tr>
      <td>C</td>
      <td><code>[2,1,2]</code></td>
      <td>Mengetahui dua kejadian A, satu kejadian B, dan dua kejadian sendiri</td>
    </tr>
  </tbody>
</table>

---

## 12. Mutual exclusion

<p align="justify"><strong>Mutual exclusion</strong> adalah prinsip dalam ilmu komputer yang memastikan hanya satu proses atau thread yang dapat mengakses sumber daya bersama pada satu waktu, sehingga konflik tidak terjadi dan integritas data terjaga. Banyak proses mungkin memerlukan akses eksklusif ke sumber daya seperti printer, berkas, atau basis data.</p>

<p align="justify">Bagian kode yang mengakses sumber daya bersama disebut <strong>critical section</strong> (CS). Tujuan mutex adalah memastikan tidak ada eksekusi CS yang tumpang tindih. Jika lebih dari satu proses masuk ke CS secara bersamaan, inkonsistensi atau kerusakan data bisa terjadi.</p>

<p align="justify">Ada empat prinsip yang harus dipenuhi oleh solusi mutual exclusion.</p>

- **Mutual exclusion.** Hanya satu proses yang boleh bekerja pada sumber daya bersama pada satu waktu
- **Progress.** Jika tidak ada proses yang sedang berada di CS, maka permintaan untuk masuk harus bisa diputuskan tanpa penundaan yang tidak perlu
- **Bounded waiting.** Tidak ada proses yang menunggu selamanya; ada batas jumlah proses lain yang boleh mendahuluinya
- **Fairness.** Permintaan dilayani dalam urutan yang wajar, biasanya berdasarkan waktu kedatangan

<p align="justify">Pada sistem terdistribusi, masalah ini menjadi lebih sulit karena tidak ada memori bersama dan tidak ada satu tempat untuk menyimpan status "sedang dipakai". Karena itu muncul beberapa pendekatan algoritmik.</p>

---

## 13. Algoritma mutual exclusion terdistribusi

<p align="justify">Slide membahas tiga pendekatan, masing-masing dengan trade-off yang berbeda antara kesederhanaan, ketahanan terhadap kegagalan, dan biaya pesan.</p>

### 13.1 Centralized algorithm

<p align="justify">Satu node bertindak sebagai koordinator. Proses yang ingin masuk CS meminta izin kepada koordinator. Koordinator memberikan akses jika CS sedang bebas, dan jika sedang dipakai, permintaan antre. Saat pemegang CS melepaskannya, koordinator mengizinkan proses berikutnya.</p>

<p align="justify">Kelebihannya sederhana dan biayanya rendah, hanya tiga pesan per pemakaian CS, yaitu <em>request</em>, <em>grant</em>, dan <em>release</em>. Kekurangannya, koordinator menjadi <em>single point of failure</em> dan bisa menjadi <em>bottleneck</em> ketika permintaan banyak.</p>

### 13.2 Ricart–Agrawala

<p align="justify">Algoritma ini sepenuhnya terdistribusi, tanpa koordinator. Setiap proses yang ingin masuk CS mengirim pesan permintaan ke semua proses lain, dan pesan itu diberi stempel waktu. Proses penerima membalas OK jika ia tidak tertarik masuk CS atau jika stempel waktu permintaan yang masuk lebih kecil daripada stempel waktu permintaannya sendiri. Jika ia juga ingin masuk CS dengan stempel waktu lebih kecil, ia menunda balasan sampai selesai.</p>

<p align="justify">Suatu proses boleh masuk CS setelah semua proses lain menjawab OK. Setiap pemakaian CS memerlukan 2 × (N − 1) pesan, terdiri atas N − 1 pesan permintaan dan minimal N − 1 pesan balasan. Kelebihannya tahan terhadap kegagalan satu node, karena tidak ada koordinator tunggal. Kekurangannya, <em>overhead</em> pesan tinggi dan jumlahnya tumbuh cepat seiring jumlah node. Jika ada node yang mati, mekanisme tambahan diperlukan agar balasan yang tidak pernah datang tidak membuat proses menunggu selamanya.</p>

### 13.3 Token-based algorithm

<p align="justify">Sebuah token unik beredar di antara proses. Proses hanya boleh masuk CS jika sedang memegang token. Setelah selesai, token diteruskan ke proses berikutnya dalam lingkar logis.</p>

<p align="justify">Kelebihannya sangat hemat, hanya satu pesan per pemakaian CS. Kekurangannya, kehilangan token sulit dideteksi dan memerlukan mekanisme pemulihan, misalnya dengan membangkitkan token baru setelah waktu tertentu.</p>

<p align="center">Tabel 8: Perbandingan algoritma mutual exclusion</p>

<table align="center">
  <thead>
    <tr>
      <th>Algoritma</th>
      <th>Cara kerja</th>
      <th>Biaya pesan per CS</th>
      <th>Kelebihan</th>
      <th>Kekurangan</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Centralized</td>
      <td>Satu koordinator memberi izin berdasarkan antrean</td>
      <td>3 pesan</td>
      <td>Sederhana, mudah dipahami, biaya rendah</td>
      <td><em>Single point of failure</em>, rawan <em>bottleneck</em></td>
    </tr>
    <tr>
      <td>Ricart–Agrawala</td>
      <td>Permintaan berstempel waktu dikirim ke semua proses, masuk CS setelah semua menjawab OK</td>
      <td>2 × (N − 1) pesan</td>
      <td>Terdistribusi penuh, tahan kegagalan satu node</td>
      <td><em>Overhead</em> pesan tinggi, perlu penanganan node mati</td>
    </tr>
    <tr>
      <td><em>Token-based</em></td>
      <td>Token tunggal berpindah antar proses; hanya pemegang token boleh masuk CS</td>
      <td>1 pesan</td>
      <td>Paling hemat pesan</td>
      <td>Kehilangan token sulit dideteksi, perlu pemulihan</td>
    </tr>
  </tbody>
</table>

<p align="justify"><strong><em>Tidak ada algoritma yang unggul di semua aspek. Centralized menang pada kesederhanaan, Ricart–Agrawala menang pada ketiadaan koordinator tunggal, dan token-based menang pada efisiensi pesan.</em></strong> Pilihan bergantung pada skala sistem dan seberapa besar toleransi terhadap kegagalan yang dibutuhkan.</p>

---

## 14. Pemilihan koordinator

<p align="justify">Dalam sistem terdistribusi, beberapa node kadang memerlukan satu koordinator untuk mengelola sumber daya bersama, menyinkronkan operasi, dan membuat keputusan global. Ketika koordinator gagal atau node baru bergabung, sistem harus bisa memilih pemimpin baru secara otomatis tanpa intervensi manual.</p>

<p align="justify">Proses ini disebut <em>leader election</em> atau pemilihan koordinator. Beberapa algoritma yang bisa dipakai adalah sebagai berikut.</p>

- **Bully algorithm.** Setiap proses memiliki nomor identitas. Proses yang mendeteksi koordinator mati mengirim pesan pemilihan ke semua proses dengan nomor lebih tinggi. Yang tertinggi akan menjawab dan mengambil alih, lalu mengumumkan dirinya sebagai koordinator. Namanya "bully" karena proses bernomor terbesar memaksa yang lain mengalah
- **Ring algorithm.** Proses disusun dalam lingkaran logis. Pesan pemilihan berputar searah lingkaran sambil mengumpulkan nomor identitas, dan nomor tertinggi yang terkumpul menjadi koordinator baru
- **Raft dan Paxos.** Keduanya berbasis konsensus. Raft memilih pemimpin melalui pemungutan suara dengan istilah (<em>term</em>) yang meningkat, sedangkan Paxos menyepakati nilai melalui dua fase, yaitu <em>prepare</em> dan <em>accept</em>. Keduanya lebih rumit tetapi memberikan jaminan yang lebih kuat ketika jaringan terpecah

<p align="center">Tabel 9: Algoritma pemilihan koordinator</p>

<table align="center">
  <thead>
    <tr>
      <th>Algoritma</th>
      <th>Ide utama</th>
      <th>Karakter</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Bully</td>
      <td>Nomor identitas tertinggi menang</td>
      <td>Sederhana, komunikasi O(N²), mengasumsikan semua proses saling mengenal</td>
    </tr>
    <tr>
      <td>Ring</td>
      <td>Pesan pemilihan berputar searah lingkaran, nomor tertinggi dipilih</td>
      <td>Komunikasi O(N), tetapi bergantung pada topologi cincin</td>
    </tr>
    <tr>
      <td>Raft / Paxos</td>
      <td>Konsensus mayoritas dengan istilah dan log</td>
      <td>Kompleks, tetapi aman saat ada partisi jaringan dan node gagal</td>
    </tr>
  </tbody>
</table>

<p align="justify">Di industri, pemilihan koordinator jarang ditulis sendiri. Apache ZooKeeper menyediakannya melalui protokol <strong>Zab</strong> (<em>ZooKeeper Atomic Broadcast</em>) yang mirip Paxos, sedangkan Kubernetes memakai <strong>Raft</strong> lewat <strong>etcd</strong>. Layanan lain seperti Consul juga memakai Raft untuk menjaga konsistensi data konfigurasi dan status klaster.</p>

<p align="justify"><strong><em>Pemilihan koordinator adalah bentuk konkret dari konsensus: banyak node harus menyepakati satu pemimpin yang sama walaupun sebagian pesan terlambat atau sebagian node tidak menjawab.</em></strong></p>

---

## Ringkasan

<p align="justify"><strong>Sinkronisasi adalah fondasi koordinasi pada sistem terdistribusi.</strong> Tanpa sinkronisasi, dua pembelian pada stok yang sama dapat menghasilkan satu pembaruan hilang, dan dua penarikan saldo dapat membuat rekening minus. Data race terjadi di level memori, sedangkan race condition terjadi di level logika bisnis, dan keduanya berakar pada eksekusi bersamaan yang tidak diatur.</p>

<p align="justify">Sinkronisasi waktu fisik berusaha menyamakan jam seluruh node dengan acuan atomik yang disebarkan lewat GPS, lalu diteruskan oleh NTP atau PTP. NTP dengan chrony memberi akurasi milidetik sampai mikrodetik dan cukup untuk sebagian besar kebutuhan. PTP dengan linuxptp bisa mencapai presisi jauh lebih tinggi, tetapi menuntut NIC yang mendukung <em>hardware timestamping</em>. Bagaimanapun, jam fisik tetap menyisakan selisih dan tidak bisa dijadikan dasar tunggal untuk mengurutkan kejadian.</p>

<p align="justify">Karena itu dipakai waktu logis. Lamport clock mengurutkan kejadian dengan satu angka, sedangkan vector clocks menyimpan satu angka per proses sehingga bisa membedakan hubungan sebab akibat dari kejadian yang berjalan bersamaan. Untuk mengakses sumber daya bersama, tersedia algoritma centralized, Ricart–Agrawala, dan token-based, dengan trade-off masing-masing pada biaya pesan dan ketahanan terhadap kegagalan. Ketika koordinator dibutuhkan, bully, ring, dan protokol konsensus seperti Raft dan Paxos menyediakan cara memilihnya secara otomatis.</p>

<p align="justify"><strong><em>Inti minggu ini adalah bahwa sistem terdistribusi tidak memiliki satu memori bersama dan tidak memiliki satu jam yang sempurna. Koordinasi karena itu dibangun di atas urutan logis, protokol yang disepakati, dan mekanisme yang tetap bekerja ketika sebagian node gagal.</em></strong></p>

---

## Referensi

- Slide perkuliahan Pertemuan 3, "Sinkronisasi dan Koordinasi pada Sistem Terdistribusi", Dr. Bambang Purnomosidi D. P., FTI UTDI.
- Tanenbaum, A. S., dan van Steen, M. (2007). _Distributed Systems: Principles and Paradigms_.
- Lamport, L. (1978). _Time, Clocks, and the Ordering of Events in a Distributed System_.
- Fidge, C. (1988). _Timestamps in Message-Passing Systems That Preserve the Partial Ordering_.
- Mattern, F. (1988). _Virtual Time and Global States of Distributed Systems_.
- Mills, D. L., dkk. (2010). _Network Time Protocol Version 4: Protocol and Algorithms Specification_ (RFC 5905).
- IEEE. _1588-2019: Standard for a Precision Clock Synchronization Protocol for Networked Measurement and Control Systems_.
- chrony. _Introduction_. https://chrony-project.org/
- LinuxPTP Project. https://linuxptp.nwtime.org/
- Wikipedia. _Coordinated Universal Time_. https://en.wikipedia.org/wiki/Coordinated_Universal_Time
- NIST. _How UTC(NIST) is Related to Coordinated Universal Time (UTC)_. https://www.nist.gov/pml/time-and-frequency-division/how-utcnist-related-coordinated-universal-time-utc-international
- GeeksforGeeks. _Types of Distributed Deadlock_. https://www.geeksforgeeks.org/operating-systems/distributed-system-types-of-distributed-deadlock/
- Hunt, P., dkk. (2010). _ZooKeeper: Wait-free coordination for Internet-scale systems_.
- Ongaro, D., dan Ousterhout, J. (2014). _In Search of an Understandable Consensus Algorithm (Raft)_.
