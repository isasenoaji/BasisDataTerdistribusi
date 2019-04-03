# MongoDB Atlas & Compass

# Daftar Isi
1. [Register](#1-register)
2. [Instalasi](#2-instalasi)
   - [Mongo Shell](#--mongo-shell)
   - [Mongo Compass](#--mongo-compass)
3. [Import data](#3-import-data)
4. [Query Test](#4-query-test)

## 1. Register

Untuk dapat menggunakan MongoDB, register terlebih dahulu cloud cluster MongoDB <a href="https://www.mongodb.com/cloud/atlas">disini</a>. 

<img src="/MongoDB/resources/register.png">

Masukan nama cluster yang akan dibuat

<img src="/MongoDB/resources/make name.png">

Setelah itu, kita akan mendapat 3 node cluster secara gratis yang terdiri dari 1 primer dan 2 secondary. 
<img src="/MongoDB/resources/proses make cluster.png">

Tambahkan WhiteList IP Anda untuk melakukan akses ke cluster cloud yang sudah di daftarkan pada tab security->IP WhiteList. Disini penulis memasukan IP 0.0.0.0/0 yang berarti nantinya cluster cloud ini dapat diakses globar dari IP manapun (not recommended and not save).
<img src="/MongoDB/resources/whitelist.png">


## 2. Instalasi

Agar dapat mengakses dan memanipulasi database MongoDB Cluster yang sudah dibuat, perlu aplikasi tambahan. Adapun aplikasi pengaksesan bisa melalui Mongo Shell yang berbasis command line atau langsung menggunakan interface bernama MongoDB Compass.

Untuk instalasi Mongo Shell bisa download <a href="https://downloads.mongodb.org/win32/mongodb-shell-win32-x86_64-2008plus-ssl-4.0.8.zip">disini</a>.

Untuk instalasi MongoDB Compass bisa download <a href="https://www.mongodb.com/download-center/community">disini</a>.

**note**
Pastikan untuk menambah ```PATH``` pada environment komputer/laptop anda yang diarahkan pada direktori ```{path Anda menginstal Mongo Shell}\bin``` dan ```{path Anda menginstal MongoDB Compass}\bin```

## 3. Import Data

Dalam case ini, penulis menggunakan dataset yang sudah disediakan pada folder <a href="">resources</a>.

Sebelum melakukan import data ke cluster, kita perlu Address dimana primer berjalan. Untuk mengetahui Address nya, buka MongoDB Cluster Anda melalui browser, klik nama Cluster lalu akan tampil node yang ada.
<img src="/MongoDB/resources/import1.png">
<img src="/MongoDB/resources/import2.png">

Klik pada primary node dan copy address yang berada pada judul grafik.
<img src="/MongoDB/resources/import3.png">

Buka CMD Anda, disini Penulis menggunakan CMDER jalankan command :

```
mongoimport --host [alamat] --db [namadatabase] --type json --file [alamat file yang akan di import] --jsonArray --authenticationDatabase admin --ssl --username [username yang tadi dibuat] --password [passwordnya] 
```
<img src="/MongoDB/resources/import.png">

## 4. Query Test

Instal MongoDB Compass yang sudah didownload, gunakan setting akses via network agar nantinya bisa melakukan koneksi keluar/jaringan.

Sambil menunggu instalasi selesai, buka cloud Anda klik connect dan pilih connect using compass. maka akan tampil seperti berikut.

<img src="/MongoDB/resources/compass connect.png">

copy alamat point nomer 3.

Setelah instalasi selesai, pada halaman connect to, jika Anda masih menyimpan copy alamat, maka aplikasi akan secara otomatis mengeluarkan prompt terdapat string isian didalamnya klik yes. isikan username dan password anda.

<img src="/MongoDB/resources/compass connect 2.png">

Klik Connect.

Jika berhasil harusnya akan tampil data yang sebelumnya sudah diimport sebelumnya

Penulis melakukan insert data dengan query berikut :

<img src="/MongoDB/resources/insert.png">

dan meng-query untuk menampilkan hasil query yang sudah dibuat.

<img src="/MongoDB/resources/query.png">


Trimakasih :)

