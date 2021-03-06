# Cassandra Single Node Instalation with CRUD

Author : Muhammad Isa Senoaji - 05111640000078

## Table of Content

1. [Definisi dan Perbedaan](#1-definisi-dan-perbedaan)
2. [Arsitektur Server](#2-arsitektur-server)
3. [Instalasi](#3-instalasi)
   - [Instal Oracle Java Virtual Machine](#--instal-oracle-java-virtual-machine)
   - [Instal Cassandra](#--instal-cassandra)
   - [Troubleshoot dan Starting Cassandra](#--troubleshoot-dan-starting-cassandra)
   - [Koneksi ke Cassandra](#--koneksi-ke-cassandra)
4. [Dataset dan Penjelasan](#4-dataset-dan-penjelasan)
5. [Import Dataset](#5-import-dataset)
6. [CRUD](#6-crud)
7. [Kesimpulan](#7-kesimpulan)

## 1. Definisi dan Perbedaan

- Apa itu Cassandra?

Cassandra atau lengkap APACHE CASSANDRA adalah salah satu produk open source untuk menajemen database yang didistribusikan oleh Apache yang sangat scalable (dapat diukur) dan dirancang untuk mengelola data terstruktur yang berkapasitas sangat besar (Big Data) yang tersebar di banyak server. Cassandra merupakan salah satu implementasi dari NoSQL (Not Only SQL) seperti mongoDB. NoSQL merupakan konsep penyimpanan database dinamis yang tidak terikat pada relasi-relasi tabel yang kaku seperti RDBMS. 

- Komponen

Node : ini adalah server tempat penyimpanan data.
Data Center : kumpulan dari beberapa node.
Cluster : Kumpulan dari beberapa data center.
Commit Log : adalah log dari proses penulisan di Cassandra , yang berfungsi juga sebagai Crash Recovery Mechanism.
Mem-Table : Adalah memory-resident data structure. Setelah menulis dalam commit log , cassandra melakukan penulisan di sini.
CQL : Cassandra Query Language , adalah bahasa perintah query di cassandra .

- Perbedaan

Karena Cassandra termasuk dalam kategori NoSql, maka dalam perbedaan dibawah ini saya akan membedakan antara NoSql dengan Sql Relational.

| No. | Jenis | SQL | NoSQL |
|------|------|-------| -------|
| 1.  | Struktur | Terstruktur | Non Struktur (dinamis) |
|2.   | Penyimpanan Data | Tabel-tabel | json (grafik, text, dll) |
| 3. | Skala | Vertikal Terukur | Dinamis ( add other Node ) |
| 4. | Penekanan/Sifat | Atomicity, Consistency, Isolation and Durability (ACID) | Consistency, Availability and Partition (CAP) |
| 5.| Lisence | Komersial | Open Source |



## 2. Arsitektur Server

Arsitektur yang digunakan penulis untuk membuat Data Center Cassandra Single Node adalah berikut :

| No | Hostname | IP | Keterangan |
|----|----------|----|------------|
| 1. | Node | 192.168.11.100 | Node |


- Kenapa hanya menggunakan 1 node?

Dalam cassandra single node, datacenter dan node menjadi satu kesatuan.

## 3. Instalasi

###   - Instal Oracle Java Virtual Machine

Cassandra membutuhkan Oracle Java Virtual Machine (JRE) untuk dapat diinstal. Untuk menginstall :

Add Personal Package Archive (PPA), command :

```
sudo add-apt-repository ppa:webupd8team/java
```

update package

```
sudo apt-get update
```

Install package 

```
sudo apt-get install oracle-java8-set-default
```

Bila terjadi error, lakukan langkah berikut :

1. Tambahkan baris di bawah ini ke /etc/apt/sources.list:

```
deb http://debian.opennms.org/ stable main
```

2. Instal GPG key repositori

```
wget -O - http://debian.opennms.org/OPENNMS-GPG-KEY | sudo apt-key add -
```

3. Update package index

```
sudo apt-get update
```

4. Instal oracle-java8-installer deb package:

```
sudo apt-get install oracle-java8-set-default
```

setelah itu, verifikasi apakah sudah terinstal atau belum, dengan command :

```
java -version
```

bila sudah, akan tampil seperti berikut :

<img src="/Cassandra Single Node/Screenshot/java.png">


###   - Instal Cassandra

Instalasi Cassandra menggunakan versi 2.2, maka gunakan command :

```
echo "deb http://www.apache.org/dist/cassandra/debian 22x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list

Note : ganti 22x dengan versi yang anda inginkan
```

Tambah repository dengan command :

```
echo "deb-src http://www.apache.org/dist/cassandra/debian 22x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
```

untuk mencegah update warning signature :

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A278B781FE4B2BDA
gpg --keyserver pgp.mit.edu --recv-keys F758CE318D77295D
gpg --export --armor F758CE318D77295D | sudo apt-key add -

gpg --keyserver pgp.mit.edu --recv-keys 2B5C1B00
gpg --export --armor 2B5C1B00 | sudo apt-key add -

gpg --keyserver pgp.mit.edu --recv-keys 0353B12C
gpg --export --armor 0353B12C | sudo apt-key add -
```

akan muncul seperti berikut :

<img src="/Cassandra Single Node/Screenshot/key pgp.png">

Update

```
sudo apt-get update
```

Install Cassandra

```
sudo apt-get install cassandra
```


###   - Troubleshoot dan Starting Cassandra

Untuk mengecek apakah sudah berjalan atau belum, gunakan commad :

```
sudo service cassandra status
```

Bila sukses berjalan, maka akan tampil sebagai berikut :

<img src="/Cassandra Single Node/Screenshot/cassandra service.PNG">

###   - Koneksi ke Cassandra

Ketika sudah sukses menjalankan Cassandra, maka untuk menghubungkan/kontrol penggunaan, gunakan command untuk cek status lagi nih :

```
sudo nodetool status
```

Bila berhasil akan tampil seperti berikut :

<img src="/Cassandra Single Node/Screenshot/node tool.PNG">

untuk konek yang sesungguhnya gunakan command :

```
cqlsh
```

akan tampil seperti berikut :

<img src="/Cassandra Single Node/Screenshot/test.PNG">


## 4. Dataset dan Penjelasan

Pada case kali ini, penulis menggunakan dataset berjudul "Black Friday" yang bisa didonwload pada link<a href="https://www.kaggle.com/mehdidag/black-friday/version/1"> berikut</a>.

- Penjelasan :

Dataset berikut merupakan 550 k observasi tentang 'black friday' disebuah toko retail, yang mengandung berbagai perbedaan variabel antara numerik atau sebuah kategori serta mengandung missing value alias null value.

| No | Coloumn | Keterangan |
|----|---------|--------|
| 1. | User_ID | ID User |
| 2. |  Product_ID | ID Product |
| 3. | Gender (Boolean) | Jenis Kelamin (Male/Female)|
| 4. | Age | Usia customer |
| 5. | OccupationId | Okupasi setiap customer|
| 6. | City_Category | - |
| 7. | Stay_In_Current_City_Years | - |
| 8. | Marital_Status (Boolean) | - |
| 9. | Product_Category_1 | - |
| 10. | Product_Category_2 |- |
| 11. | Product_Category_3 |- |
| 12. | Purchase | besaran pembelian dalam dollar |



## 5. Import Dataset

Buat keyspace terlebih dahulu, keyspace ini mirip dengan database pada MySQL. Untuk membuat, jalankan :

```
CREATE KEYSPACE blackfriday
WITH replication = {
   'class':'NetworkTopologyStrategy',
   'datacenter1': 1 
};

blackfriday merupakan nama keyspace
class NetworkTopologyStrategy bisa diganti dengan class yang di support oleh cassandra
```

<img src="/Cassandra Single Node/Screenshot/keyspace.PNG">

Gunakan Keyspace tersebut dengan command :

```
use blackfriday;
```
<img src="/Cassandra Single Node/Screenshot/usse.PNG">

Buat Tabel yang akan menampung hasil import dataset nantinya dengan command :

```
create table observation(User_ID int, Product_ID text, Gender boolean, Age text, Occupation int, City_Category text, Stay_In_Current_City_Years text, Marital_Status int, Product_Category_1 int, Product_Category_2 int, Product_Category_3 int, Purchase int, PRIMARY KEY(User_ID)); 
```
<img src="/Cassandra Single Node/Screenshot/create table.PNG">

Import data dengan command :

```
COPY observation(User_ID, Product_ID , Gender , Age , Occupation , City_Category , Stay_In_Current_City_Years , Marital_Status , Product_Category_1 , Product_Category_2 , Product_Category_3 , Purchase) FROM '/home/vagrant/BlackFriday.csv' WITH HEADER = TRUE;
```

Jika sukses akan keluar output berikut :

<img src="/Cassandra Single Node/Screenshot/import.PNG">


## 6. CRUD

- Insert data 

Untuk insert data, penulis akan mencoba memasukan satu contoh data dengan query :

```
INSERT INTO observation(User_ID, Product_ID , Gender , Age , Occupation , City_Category , Stay_In_Current_City_Years , Marital_Status , Product_Category_1 , Product_Category_2 , Product_Category_3 , Purchase)
VALUE (354313, '313354', TRUE, '21', 34, 'A', '3', 1, 5, 11, 5,30000);
```

<img src="/Cassandra Single Node/Screenshot/insert.PNG">

- Read Data

Untuk read data gunakan query ;

```
select * from observation where User_ID = 354313 ;
``` 

hasil :

<img src="/Cassandra Single Node/Screenshot/read.PNG">

- Update Data

update data dengan query :

```
update observation set purchase = 50000 where User_ID = 354313;

```

Hasil :

<img src="/Cassandra Single Node/Screenshot/update.PNG">


- Delete Data

Delete data dengan query :

```
DELETE FROM observation WHERE User_ID = 354313;
```

Hasil :

<img src="/Cassandra Single Node/Screenshot/delete.PNG">


## 7. Kesimpulan

Cassandra merupakan salah satu database NoSQL yang penggunaannya menggunakan query Cassandra Query Language (clq) yang mana dalam pemrosesan data sangat cepat, dibuktikan dengan saat proses import data sebesar 8 MB hanya membutuhkan waktu 30 detik saja, dibanding dengan SQL Relational yang memakan waktu sekitar 8 menit. dan juga query yang digunakan mirip seperti pada SQL RDBMS sehingga mudah digunakan.
