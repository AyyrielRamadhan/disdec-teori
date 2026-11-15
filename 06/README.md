# Ringkasan Materi Minggu ke-6: Distributed File System

Mata kuliah: Sistem Terdistribusi dan Terdesentralisasi<br>
Topik: Distributed File System<br>
Dosen: Dr. Bambang Purnomosidi D. P.<br>
Kode: FIF25002T | SKS: 3 | Prodi: Informatika, FTI UTDI

---

## Tujuan pembelajaran

- Memahami dan mampu menjelaskan pengertian dari _file system_
- Mampu menyebutkan berbagai jenis _file system_
- Mampu menjelaskan cara kerja _file system_ secara umum
- Memahami dan mampu menjelaskan pengertian _distributed file system_ (DFS)
- Mampu menyebutkan berbagai jenis DFS
- Mampu menjelaskan cara kerja DFS secara umum
- Mampu melakukan instalasi salah satu DFS sehingga mampu memahami cara kerjanya
- Mampu memahami dan menjelaskan _cloud storage_ serta hubungannya dengan DFS
- Mampu melakukan instalasi salah satu _object storage_ sebagai _cloud storage_

---

## 1. Pengertian file system

<p align="justify"><strong><em>File system adalah metode yang digunakan sistem operasi untuk mengelola dan mengatur file pada media penyimpan (harddisk, USB flashdisk, SSD, dan sebagainya).</em></strong></p>

<p align="justify">Istilah ini sering ditulis <em>file system</em> atau <em>filesystem</em>, dan disingkat FS atau <em>fs</em>. Contoh yang paling mudah dikenali adalah ext4, XFS, btrfs, dan ReiserFS di Linux; FAT 16, FAT 32, dan NTFS di DOS/Windows; serta APFS (<em>Apple File System</em>) di Mac.</p>

<p align="justify">Suatu FS diakses melalui <em>driver file system</em>. Driver tersebut dikelola kernel, yang menyediakan <strong><em>virtual file system</em></strong> untuk diakses oleh aplikasi di <em>user space</em>. Karena lapisan virtual inilah aplikasi tidak perlu tahu FS apa yang sebenarnya dipakai di bawahnya.</p>

<p align="justify">Satu hal yang perlu dibedakan dengan tegas adalah <strong>FS berbeda dengan hirarki FS</strong>. Hirarki pada FS adalah pengaturan <em>file</em> dan direktori pada suatu sistem operasi. Pengguna hanya melihat hirarki FS tanpa mempedulikan FS apa yang bekerja di baliknya.</p>

---

## 2. Berbagai jenis file system dan cara kerjanya

<p align="justify">Jenis FS yang umum dijumpai dapat dipetakan berdasarkan sistem operasinya.</p>

<p align="center">Tabel 1: Berbagai jenis file system</p>

<table align="center">
  <thead>
    <tr>
      <th>Sistem operasi</th>
      <th>File system</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Linux</strong></td>
      <td>ext4, XFS, btrfs, ReiserFS, …</td>
    </tr>
    <tr>
      <td><strong>DOS/Windows</strong></td>
      <td>FAT 16, FAT 32, NTFS, …</td>
    </tr>
    <tr>
      <td><strong>Mac</strong></td>
      <td>APFS (<em>Apple File System</em>)</td>
    </tr>
  </tbody>
</table>

<p align="justify">Secara umum, cara kerja FS dapat dibaca sebagai satu jalur dari aplikasi sampai ke perangkat keras, yaitu <strong>aplikasi → <em>System Call Interface</em> → <em>virtual FS</em> → <em>driver</em> kernel → <em>hardware</em> (media penyimpan)</strong>.</p>

<p align="justify">Dalam arsitektur kernel Linux, aplikasi berada di <em>user space</em> bersama <em>system utilities</em>, <em>shell</em>/CLI, dan aplikasi GUI. Melalui <em>System Call Interface</em> (misalnya <code>read()</code> dan <code>write()</code>), permintaan masuk ke <em>kernel space</em> yang berisi <em>process management</em>, <em>memory management</em> (MMU), IPC, <em>file systems</em> (ext4, XFS, …), <em>network stack</em> (TCP/IP), dan <em>device drivers</em>. Lapisan paling bawah adalah <em>hardware</em>, yaitu CPU, RAM, <em>disk storage</em>, dan <em>network interface</em>.</p>

<p align="justify"><strong><em>Filesystem Hierarchy Standard</em> (FHS)</strong> adalah standar penataan <em>file</em> dan direktori pada sistem operasi mirip UNIX. FHS mengatur persyaratan dan pedoman penempatan <em>file</em> agar aplikasi, alat administrasi sistem, dan skrip tetap dapat bekerja secara konsisten. Isi akar sistem berkas pada contoh di materi terlihat sebagai berikut.</p>

```text
$ tree -D -L 1 /
├── bin -> usr/bin
├── boot
├── dev
├── etc
├── home
├── lib -> usr/lib
├── lib64 -> usr/lib
├── lost+found
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin -> usr/bin
├── srv
├── sys
├── tmp
├── usr
└── var

20 directories, 0 files
```

---

## 3. Pengertian distributed file system (DFS)

<p align="justify"><strong><em>DFS bukan sekadar “FS yang tersebar di lebih dari satu mesin”. Secara konseptual, DFS adalah sistem abstraksi terkoordinasi dari sekumpulan media penyimpan yang menutupi heterogenitas, kegagalan, dan konkurensi, sehingga menghasilkan jaringan penyimpanan yang terintegrasi, andal, dan berkinerja tinggi.</em></strong></p>

<p align="justify">Seperti halnya sistem terdistribusi lain, komponen media penyimpan pada DFS terdiri atas lebih dari satu media penyimpan, tetapi dari sisi pengguna tampak sebagai satu media penyimpan. Di situlah letak inti abstraksinya. Banyak perangkat, banyak lokasi, dan banyak kemungkinan gagal, tetapi yang dilihat pengguna hanya satu tempat menyimpan berkas.</p>

<p align="justify">Karena itu muncul pertanyaan pembanding yang penting, yaitu apa beda antara <strong>DAS</strong> (<em>Direct-Attached Storage</em>), <strong>DFS</strong>, <strong>NAS</strong> (<em>Network-Attached Storage</em>), dan <strong>SAN</strong> (<em>Storage Area Network</em>)?</p>

---

## 4. DAS, NAS, SAN, dan DFS

<p align="justify"><strong>DAS</strong> adalah penyimpanan yang menempel langsung pada satu mesin. Pada contoh di materi, hal itu terlihat dari keluaran <code>df -h</code> yang seluruhnya berupa perangkat lokal dan sistem berkas sementara milik mesin itu sendiri.</p>

```text
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
dev              32G     0   32G   0% /dev
run              32G  1.7M   32G   1% /run
efivarfs        192K  124K   64K  67% /sys/firmware/efi/efivars
/dev/nvme0n1p2   98G   39G   55G  42% /
shm              32G  179M   32G   1% /dev/shm
/dev/nvme0n1p1 1022M  328K 1022M   1% /boot/efi
/dev/nvme0n1p3  373G  249G  125G  67% /home
tmpfs            32G   35M   32G   1% /tmp
tmpfs           6.3G   16K  6.3G   1% /run/user/1000
```

<p align="justify"><strong>NAS</strong> adalah <em>shared storage</em> melalui jaringan bersama. Yang dibagikan adalah <em>file system</em>, sehingga pengelolaannya lebih mudah. Di sisi lain, <strong>SAN</strong> adalah <em>shared storage</em> melalui jaringan khusus, yang menyajikan <em>block storage</em>. SAN cepat tetapi mahal, dan biasanya dibangun dengan <em>SAN switch</em> serta kumpulan <em>disk</em> tersendiri.</p>

<p align="center">Tabel 2: Perbandingan DAS, NAS, SAN, dan DFS</p>

<table align="center">
  <thead>
    <tr>
      <th>Aspek</th>
      <th>DAS</th>
      <th>NAS</th>
      <th>SAN</th>
      <th>DFS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Level penyajian</strong></td>
      <td><em>Disk</em> lokal terpasang langsung</td>
      <td><em>File system</em></td>
      <td><em>Block storage</em></td>
      <td>Abstraksi <em>software</em> atas banyak node</td>
    </tr>
    <tr>
      <td><strong>Media/jalur</strong></td>
      <td>Kabel langsung ke mesin</td>
      <td>Jaringan bersama (TCP/IP)</td>
      <td>Jaringan khusus (FC/iSCSI) via <em>SAN switch</em></td>
      <td>Jaringan antar node</td>
    </tr>
    <tr>
      <td><strong>Ciri utama</strong></td>
      <td>Sederhana, terikat satu mesin</td>
      <td>Mudah dikelola, berbagi berkas</td>
      <td>Cepat tetapi mahal</td>
      <td>Terintegrasi, andal, berkinerja tinggi</td>
    </tr>
  </tbody>
</table>

<p align="justify">DFS adalah abstraksi pada level <em>software</em> dan mengelola data ke dalam <em>file</em> serta direktori yang dapat didistribusikan ke banyak node. SAN menyajikan penyimpanan sebagai sekumpulan <em>block level devices</em> yang dapat dihubungkan langsung oleh server dan diformat dengan FS-nya sendiri.</p>

<p align="justify">Secara konseptual SAN bisa digunakan sebagai penyimpanan untuk DFS, meskipun hal itu tidak disarankan. Penggunaan umum SAN justru meliputi <em>backend</em> untuk basis data berskala besar, <em>backend</em> untuk <em>video</em>–<em>audio editing</em>, dan <em>backend</em> untuk virtualisasi berskala besar. DFS sendiri lebih mengarah pada DAS di setiap node.</p>

---

## 5. Berbagai DFS yang umum

<p align="justify">Beberapa DFS yang umum dijumpai disebutkan pada materi sebagai berikut.</p>

<p align="center">Tabel 3: Berbagai DFS yang umum</p>

<table align="center">
  <thead>
    <tr>
      <th>DFS</th>
      <th>Keterangan</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong><em>Google File System</em></strong></td>
      <td>DFS yang dikembangkan Google</td>
    </tr>
    <tr>
      <td><strong><em>Colossus</em></strong></td>
      <td>Pengganti <em>Google File System</em>; digunakan di YouTube, GMail, Google Docs, …</td>
    </tr>
    <tr>
      <td><strong><em>CephFS</em></strong></td>
      <td>https://ceph.io/en/</td>
    </tr>
    <tr>
      <td><strong><em>Gluster</em></strong></td>
      <td>https://www.gluster.org/</td>
    </tr>
    <tr>
      <td><strong><em>HDFS</em></strong></td>
      <td>Hadoop DFS — https://hadoop.apache.org/</td>
    </tr>
    <tr>
      <td><strong><em>Lustre</em></strong></td>
      <td>https://www.lustre.org/</td>
    </tr>
  </tbody>
</table>

---

## 6. Cara kerja DFS

<p align="justify">Cara kerja DFS dapat dijelaskan lewat susunan lapisan. Pengguna dan aplikasi mengakses data melalui sebuah <strong><em>global namespace</em></strong>. Lapisan <em>distributed file system</em> kemudian berdiri di atas beberapa <em>namespace</em> yang berbeda di bawahnya.</p>

<p align="justify">Pada contoh di materi, di bawah satu <em>global namespace</em> terdapat <em>Namespace 1</em> dan <em>Namespace 2</em> yang berupa NAS 1 dan NAS 2 di <em>data center</em>; <em>Namespace 3</em> dan <em>Namespace 4</em> yang berupa Linux NFS dan Windows SMB di <em>remote location</em>; serta <em>Namespace 5</em> yang berada di <em>public cloud</em>.</p>

<p align="justify">Susunan itu memperlihatkan inti pekerjaan DFS, yaitu menyatukan penyimpanan yang berbeda-beda menjadi satu <em>namespace</em> global, menutupi heterogenitas antar sistem, menutupi kegagalan komponen, dan menutupi konkurensi akses, sehingga hasilnya terintegrasi, andal, dan berkinerja tinggi.</p>

---

## 7. Studi kasus HDFS

<p align="justify"><strong><em>HDFS adalah file system yang bersifat open source, scalable, terdistribusi, dan fault-tolerant yang dirancang untuk mengelola dan menyimpan dataset besar di seluruh klaster commodity hardware.</em></strong></p>

<p align="justify"><strong><em>Commodity hardware</em></strong> berarti perangkat keras komputer yang diproduksi secara massal, murah, dan mudah dipertukarkan, serta tersedia secara luas dari berbagai vendor. HDFS merupakan komponen utama Apache Hadoop dan dipakai untuk pemrosesan data skala besar dengan membagi <em>file</em> menjadi blok-blok, mendistribusikannya ke beberapa server, dan mereplikasinya untuk mencapai <em>high availability</em>.</p>

<p align="justify">Pada sisi versi, Apache Hadoop <strong>3.5.0</strong> dikompilasi dengan <strong>JDK 17</strong> dan dapat dijalankan dengan Java 17 (untuk server) atau Java 17 dan Java 21 (klien).</p>

<p align="justify">Arsitektur HDFS bersifat <em>master–slave</em>. Pada node <em>master</em> terdapat <strong>NameNode</strong> yang menyimpan metadata dan <strong>ResourceManager</strong>. Pada tiap node <em>slave</em> terdapat <strong>DataNode</strong> yang menyimpan blok data, <strong>NodeManager</strong>, serta <strong>Map/Reduce</strong>.</p>

<p align="justify">Metadata yang dipegang NameNode mencakup nama <em>file</em>, jumlah replika, dan informasi lain, misalnya <code>/home/foo/data, 3</code>. Ketika klien membaca, ia melakukan <em>metadata ops</em> ke NameNode, lalu <strong>membaca data langsung dari DataNode</strong>. Ketika menulis, klien melakukan <em>block ops</em>, lalu menulis ke DataNode, dan blok itu direplikasi ke DataNode lain. Replika disebar lintas <em>rack</em> (Rack 1 dan Rack 2) agar kegagalan satu rak tidak menghabisi seluruh salinan.</p>

<p align="center">Tabel 4: Komponen arsitektur HDFS</p>

<table align="center">
  <thead>
    <tr>
      <th>Komponen</th>
      <th>Peran</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong><em>NameNode</em></strong></td>
      <td>Node <em>master</em> yang menyimpan metadata (nama <em>file</em>, replika, …)</td>
    </tr>
    <tr>
      <td><strong><em>DataNode</em></strong></td>
      <td>Node <em>slave</em> yang menyimpan blok-blok data</td>
    </tr>
    <tr>
      <td><strong><em>Client</em></strong></td>
      <td>Melakukan <em>metadata ops</em> ke NameNode dan baca/tulis blok langsung ke DataNode</td>
    </tr>
    <tr>
      <td><strong><em>ResourceManager</em></strong></td>
      <td>Berada di sisi <em>master</em> bersama NameNode</td>
    </tr>
    <tr>
      <td><strong><em>NodeManager</em></strong></td>
      <td>Berada di tiap <em>slave</em> bersama DataNode</td>
    </tr>
  </tbody>
</table>

<p align="justify">Untuk memahami cara kerja HDFS secara praktis, materi mengarahkan agar mahasiswa mempraktikkan penggunaan HDFS sesuai dokumen yang disertakan di EL-UTDI, yaitu berkas <code>06-menggunakan-hadoop-hdfs--linux.pdf</code>.</p>

---

## 8. Pengertian cloud storage

<p align="justify"><strong><em>Cloud storage adalah layanan online yang memungkinkan pengguna menyimpan dan mengakses data di server jarak jauh melalui internet, bukan di hard drive lokal atau perangkat fisik.</em></strong></p>

<p align="justify">Cloud storage juga merupakan model penyimpanan data di cloud yang dikelola secara <em>remote</em> dalam suatu <em>pool</em> (logis) dan dapat diakses pengguna melalui jaringan, biasanya internet. Penyimpanan fisiknya mencakup beberapa server, terkadang di beberapa lokasi, dan lingkungan fisik tersebut umumnya dimiliki serta dikelola oleh penyedia <em>cloud computing</em>.</p>

<p align="justify">Contoh yang disebutkan adalah <strong>Amazon S3</strong> (https://aws.amazon.com/s3/), <strong>Azure Blob Storage</strong>, dan <strong>GCS</strong> (<em>Google Cloud Storage</em>).</p>

---

## 9. Relasi cloud storage dengan DFS

<p align="justify"><strong><em>Cloud storage adalah tempat penyimpanan data, sedangkan pengorganisasiannya dilakukan oleh DFS.</em></strong> Pembagian peran inilah yang menjelaskan mengapa keduanya sering muncul bersamaan. Yang satu menyediakan ruang, yang lain menyediakan cara mengelola ruang itu sebagai <em>file</em> dan direktori.</p>

<p align="justify">Contohnya, S3 dapat digunakan oleh HDFS. S3 berperan sebagai <em>cloud storage</em>, sementara operasi FS dilakukan oleh HDFS. Hal itu dapat dilakukan dengan konfigurasi pada <code>core-site.xml</code>.</p>

```xml
<property>
  <name>fs.s3a.access.key</name>
  <value>YOUR_AWS_ACCESS_KEY_ID</value>
</property>
<property>
  <name>fs.s3a.secret.key</name>
  <value>YOUR_AWS_SECRET_ACCESS_KEY</value>
</property>
<property>
  <name>fs.s3a.endpoint</name>
  <value>s3.amazonaws.com</value> <!-- Or specific region endpoint -->
</property>
```

---

## 10. Tipe cloud storage

<p align="justify">Cloud storage dibedakan menjadi tiga tipe, yaitu <em>object storage</em>, <em>file storage</em>, dan <em>block storage</em>.</p>

<p align="center">Tabel 5: Tipe cloud storage</p>

<table align="center">
  <thead>
    <tr>
      <th>Aspek</th>
      <th><em>Block storage</em></th>
      <th><em>File storage</em></th>
      <th><em>Object storage</em></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong><em>Transport</em></strong></td>
      <td>FC atau iSCSI</td>
      <td>TCP/IP</td>
      <td>TCP/IP</td>
    </tr>
    <tr>
      <td><strong><em>Interface</em></strong></td>
      <td><em>Direct Attached</em> atau SAN</td>
      <td>NFS, SMB</td>
      <td>HTTP, REST</td>
    </tr>
    <tr>
      <td><strong><em>Use case</em></strong></td>
      <td><em>Low latency</em>, terbaik untuk data terstruktur</td>
      <td>Kinerja baik, berbagi berkas, <em>global file locking</em></td>
      <td>Mudah diskalakan tanpa batas, dapat diakses lintas LAN dan WAN</td>
    </tr>
  </tbody>
</table>

---

## 11. Studi kasus cloud storage

<p align="justify">Materi menampilkan beberapa contoh penerapan <em>cloud storage</em> yang semuanya berbasis <em>open source</em>. <strong>Ceph</strong> (https://ceph.io/en/) adalah <em>open-source distributed storage system</em> yang menawarkan penyimpanan terpadu. <strong>CubeFS</strong> (https://cubefs.io) adalah <em>cloud native unstructured data storage</em> yang mendukung protokol akses S3, HDFS, dan POSIX, serta dipakai untuk <em>big data</em>, AI/LLM, <em>container platform</em>, hingga pemisahan <em>storage</em> dan <em>compute</em>.</p>

<p align="justify"><strong>RustFS</strong> (https://rustfs.com) diposisikan sebagai pengganti MinIO dan S3 dengan <em>zero GC</em> serta <em>throughput</em> maksimal, dibangun untuk <em>multi-cloud storage</em>. <strong>PGSTY SILO</strong> (https://silo.pgsty.com) adalah <em>fork</em> MinIO yang menyediakan antarmuka S3, dengan S3 API pada port <code>:9000</code> dan <em>console</em> pada <code>:9001</code>, serta memakai <em>erasure set</em> pada <em>distributed disks</em>. <strong>Rook</strong> (https://rook.io) adalah <em>open-source, cloud-native storage for Kubernetes</em> yang mengelola penyimpanan <em>file</em>, <em>block</em>, dan <em>object</em>.</p>

<p align="justify">Arsitektur CubeFS memperlihatkan bagaimana komponen-komponen itu disusun. Sebuah <strong>Master</strong> menjadi pusat koordinasi. <strong>Client</strong> dapat berupa <em>container</em>, VM, atau server dengan antarmuka POSIX/HDFS. Di sisi penyimpanan terdapat tiga subsistem, yaitu <em>Object Subsystem</em> dengan <strong>Object Node</strong> (juga diakses melalui AWS S3 SDK), <em>Metadata Subsystem</em> dengan <strong>Meta Node</strong> yang memakai <em>meta partition</em> di memori, serta <em>Data Subsystem</em> dengan <strong>Data Node</strong> (<em>data partition</em>) dan <strong>Blob Node</strong> (<em>erasure code stripe</em>). Di antara semuanya terdapat <strong>Volume</strong> sebagai unit logis yang menghubungkan klien dengan subsistem penyimpanan.</p>

<p align="center">Tabel 6: Komponen arsitektur CubeFS</p>

<table align="center">
  <thead>
    <tr>
      <th>Komponen</th>
      <th>Peran</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong><em>Master</em></strong></td>
      <td>Pusat koordinasi seluruh klaster</td>
    </tr>
    <tr>
      <td><strong><em>Client</em></strong></td>
      <td>Mengakses data melalui antarmuka POSIX/HDFS (dari <em>container</em>, VM, atau server)</td>
    </tr>
    <tr>
      <td><strong><em>Object Node</em></strong></td>
      <td>Bagian dari <em>Object Subsystem</em>; dapat diakses lewat AWS S3 SDK</td>
    </tr>
    <tr>
      <td><strong><em>Meta Node</em></strong></td>
      <td>Bagian dari <em>Metadata Subsystem</em>; menyimpan <em>meta partition</em> di memori</td>
    </tr>
    <tr>
      <td><strong><em>Data Node</em></strong></td>
      <td>Menyimpan <em>data partition</em></td>
    </tr>
    <tr>
      <td><strong><em>Blob Node</em></strong></td>
      <td>Menyimpan <em>erasure code stripe</em></td>
    </tr>
    <tr>
      <td><strong><em>Volume</em></strong></td>
      <td>Unit logis penghubung antara klien dan subsistem penyimpanan</td>
    </tr>
  </tbody>
</table>

---

## Ringkasan

<p align="justify"><strong>File system adalah metode sistem operasi untuk mengelola dan mengatur file pada media penyimpan.</strong> FS diakses lewat driver yang dikelola kernel melalui <em>virtual file system</em>, dan yang dilihat pengguna sebenarnya adalah hirarki FS, bukan FS-nya. Jenisnya bergantung pada sistem operasi, yaitu ext4, XFS, btrfs, dan ReiserFS di Linux; FAT 16, FAT 32, dan NTFS di DOS/Windows; APFS di Mac. Jalur kerjanya berjalan dari aplikasi ke <em>System Call Interface</em>, lalu <em>virtual FS</em>, <em>driver</em> kernel, dan akhirnya perangkat keras, dengan penataan direktori mengikuti FHS.</p>

<p align="justify"><strong>DFS bukan sekadar FS yang tersebar di banyak mesin, melainkan abstraksi terkoordinasi yang menutupi heterogenitas, kegagalan, dan konkurensi.</strong> Banyak media penyimpan tampil sebagai satu media penyimpan. Ia berbeda dari DAS yang menempel langsung pada satu mesin, dari NAS yang membagikan <em>file system</em> lewat jaringan bersama, dan dari SAN yang menyajikan <em>block storage</em> lewat jaringan khusus yang cepat tetapi mahal. DFS lebih mengarah pada DAS di setiap node, dengan contoh umum Google File System, Colossus, CephFS, Gluster, HDFS, dan Lustre. Cara kerjanya bersandar pada satu <em>global namespace</em> yang menaungi banyak <em>namespace</em> di bawahnya, mulai dari NAS di <em>data center</em>, NFS dan SMB di lokasi terpencil, hingga penyimpanan di <em>public cloud</em>.</p>

<p align="justify"><strong>HDFS menjadi contoh konkretnya.</strong> HDFS bersifat <em>open source</em>, <em>scalable</em>, terdistribusi, dan <em>fault-tolerant</em>, dirancang untuk <em>dataset</em> besar di atas <em>commodity hardware</em>. <em>File</em> dibagi menjadi blok, didistribusikan ke beberapa server, lalu direplikasi. NameNode di sisi <em>master</em> memegang metadata, DataNode di tiap <em>slave</em> menyimpan blok, dan klien membaca serta menulis langsung ke DataNode sementara metadata selalu ditanyakan ke NameNode. Replikasi lintas <em>rack</em> menjaga ketersediaan ketika satu rak bermasalah.</p>

<p align="justify"><strong>Cloud storage adalah tempat penyimpanan data, sedangkan pengorganisasiannya dilakukan oleh DFS.</strong> Contohnya S3 yang dipakai sebagai penyimpanan oleh HDFS melalui konfigurasi <code>core-site.xml</code>. Cloud storage terdiri atas <em>object storage</em>, <em>file storage</em>, dan <em>block storage</em>, yang berbeda pada <em>transport</em>, <em>interface</em>, dan <em>use case</em>-nya. Penerapannya terlihat pada Ceph, CubeFS, RustFS, PGSTY SILO, dan Rook, dengan CubeFS yang memperlihatkan susunan Master, Client, Object Node, Meta Node, Data Node, dan Blob Node di sekitar sebuah Volume.</p>

<p align="justify"><strong><em>Inti materi ini adalah bahwa penyimpanan terdistribusi bekerja dengan menyembunyikan kerumitan, bukan menghilangkannya.</em></strong> Banyak mesin, banyak lokasi, dan banyak kemungkinan gagal tetap ada di bawahnya; yang dibuat adalah satu lapisan abstraksi agar pengguna cukup melihat satu tempat menyimpan berkas yang tetap dapat diandalkan.</p>

---

## Referensi

- Slide perkuliahan Pertemuan 6, "Distributed File System", Dr. Bambang Purnomosidi D. P., FTI UTDI.
- Berkas `06-menggunakan-hadoop-hdfs--linux.pdf`, untuk praktik penggunaan HDFS.
- FreeDesktop. _Filesystem Hierarchy Standard_ 3.0 (2025). https://specifications.freedesktop.org/fhs/latest/
- Thomas-Krenn. _Linux Storage Stack Diagram_. https://www.thomas-krenn.com/en/wiki/Linux_Storage_Stack_Diagram
- Tekmart. _Key differences between SAN and NAS storage systems_. https://tekmart.co.za/t-blog/key-differences-between-san-and-nas-storage-systems/
- vmblog. _Five Traits to Look for in a Distributed File System_. https://vmblog.com/archive/2020/11/25/five-traits-to-look-for-in-a-distributed-file-system.aspx
- Apache Hadoop. _HDFS Architecture_ (HdfsDesign). https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html
- GeeksforGeeks. _Hadoop HDFS – Hadoop Distributed File System_. https://www.geeksforgeeks.org/data-engineering/hadoop-hdfs-hadoop-distributed-file-system/
- Databricks. _Hadoop Distributed File System (HDFS)_ glossary. https://www.databricks.com/glossary/hadoop-distributed-file-system-hdfs
- Cloudian. _Object Storage Care_. https://cloudian.com/guides/object-storage/object-storage-care/
- Amazon S3. https://aws.amazon.com/s3/
- Ceph. https://ceph.io/en/ — Gluster. https://www.gluster.org/ — Lustre. https://www.lustre.org/
- CubeFS. https://cubefs.io — _Architecture_. https://cubefs.io/docs/master/overview/architecture.html#architecture
- RustFS. https://rustfs.com — PGSTY SILO. https://silo.pgsty.com — Rook. https://rook.io
- Tanenbaum, A. S., dan van Steen, M. (2007). _Distributed Systems: Principles and Paradigms_.
