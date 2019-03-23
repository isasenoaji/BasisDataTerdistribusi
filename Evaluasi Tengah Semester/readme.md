# Instalasi Wordpress dengan database MySQL Cluster

## Prequisites
1. Ubuntu 16.04
2. Apache2
3. PHP 7.0
4. MySQL Cluster with Load Balancer ProxySQL

## Daftar Isi :

1. [Instalasi Cluster](#1-instalasi-cluster)
2. [Instalasi Wordpress](#2-instalasi-wordpress)
3. [Uji Coba Fail Over](#3-uji-coba-fail-over)
4. [Respond Time Test using JMeter](#4-respond-time-test-using-jmeter)


## 1. Instalasi Cluster

Untuk instalasi cluster, sama pada tutorial <a href="https://github.com/isasenoaji/BasisDataTerdistribusi">berikut</a>.
pada tutorial ini, penulis menggunakan cluster tersebut.

## 2. Instalasi Wordpress
Untuk menginstal wordpress, download terlebih dahulu file instalasi nya <a href="https://wordpress.org/download/">disini</a>.
Setelah itu, pindahkan file hasil download ke file sistem yaitu /vagrant. Sebelum menginstal wordpress, instal ektensi pendukungnya. yaitu Apache2 dan PHP.

Login pada ProxySQL, jalankan query berikut untuk menginstal :

```
sudo apt-get install apache2
sudo apt-get install php
sudo apt-get install php-mysql
sudo apt-get install -y php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-snmp php-soap php-tidy curl
```

Setelah itu pada versi ini dibutuhkan library tambahan guna mengatasi error, 

```
sudo apt-get install libapache2-mod-php
sudo a2enmod php7.0
sudo systemctl restart apache2
```

Jika sudah, buat database untuk menyimpan data wordpress pada salah satu ```service``` bernama wordpress(optional). dengan query :

```
service1 > mysql -u root -p
mysql > create database wordpress;
```

<img src="/Evaluasi Tengah Semester/Screenshot 3/create table.png">

Tambahkan akses pada user ```mysqlcluster``` agar bisa mengakses database wordpress dengan query :

```
GRANT ALL PRIVILEGES on wordpress.* to 'mysqlcluster'@'%';
FLUSH PRIVILEGES;
```
<img src="/Evaluasi Tengah Semester/Screenshot 3/Grant akses.png">

Note :

Karena MySQL Cluster hanya support engine NDBCluster, maka kita perlu set default engine pada semua service pada /etc/my.cnf dengan format diletakan dibawah ```bind-address``` :

```
default-storage-engine=ndbcluster
```

restart service mysql

```
sudo systemctl restart mysql
```

Kembali ke console ProxySQL, ganti direktori saat ini ke /var/www/html

```
cd /var/www/html
```

Untar hasil file wordpress yang sudah  didownload dengan perintah :

```
sudo tar -xvf wordpress-5.1.1.tar.gz
```

Setelah proses Untar telah selesai, ganti nama file ```wp-config-sample.php``` menjadi ```wp-config.php```

```
sudo mv wp-config-sample.php wp-config.php
```

edit konfigurasi database pada file ```wp-config.php``` dengan perintah :

```
sudo nano wp-config.php
```

ubah menjadi seperti berikut :

```
***
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );

/** MySQL database username */
define( 'DB_USER', 'mysqlcluster' );

/** MySQL database password */
define( 'DB_PASSWORD', 'vagrant' );

/** MySQL hostname */
define( 'DB_HOST', '192.168.31.106:6033' );

***
```

Buka browser, disini penulis menggunakan browser chrome dengan alamat 192.168.31.106/wordpress

Isi data-data yang ada sesuai petunjuk.
<img src="/Evaluasi Tengah Semester/Screenshot 3/wordpress sukses instal.png">


Saat penulis membuat tutorial ini, ```data node``` yang aktif/sedang menjadi utama adalah ```data1```. Login untuk membuat sebuah post baru 

<img src="/Evaluasi Tengah Semester/Screenshot 3/login wordpress.png">

<img src="/Evaluasi Tengah Semester/Screenshot 3/wordpress new post.png">


## 3. Uji Coba Fail Over

Untuk pengujian apakah Cluster mampu menghadapi/mengatasi fail over, maka pada case kali ini karna ```node data``` utama adalah ```data1``` sesuai gambar dibawah akan dimatikan lalu diuji akses ke wordpress kembali.

<img src="/Evaluasi Tengah Semester/Screenshot 3/manager status before 1.png">

Matikan layanan pada ``` data1``` dengan perintah :

```
sudo systemctl stop ndbd
sudo pkill -f ndbd
```
<img src="/Evaluasi Tengah Semester/Screenshot 3/data1 matikan.png">

Akses/refresh kembali wordpress, bila berhasil maka akan tidak akan error dan akan memuat seperti biasanya dan juga data yang dimuat tidak mengalami pengurangan sedikitpun.

<img src="/Evaluasi Tengah Semester/Screenshot 3/wordpress new post.png">

Status pada manager :

<img src="/Evaluasi Tengah Semester/Screenshot 3/manager status after 1.png">

### Conclusion

Kesimpulan dari test diatas adalah ketika salah satu node data dimatikan, maka secara otomatis data node lainnya akan menggantikan dengan cepat tanpa perintah khusus, sehingga terbukti cluster memiliki sifat high availability. Untuk pengukuran waktu respond akan dibahas pada section selanjutnya menggunakan aplikasi bernama JMeter.

## 4. Respond Time Test using JMeter

JMeter adalah sebuah software open source yang dapat digunakan secara gratis untuk berbagai pengukuran, salah satunya pengukuran respond time method GET pada http. Pada uji coba respond time/load time pada wordpress sekaligus basis data yang sudah dibuat sebelumnya. Aplikasi dapat didownload <a href="https://jmeter.apache.org/download_jmeter.cgi>disini</a>.
Pada saat pengujian ini, ```data node``` yang sedang aktif/primer adalah ```data3```.
  
Cara Testing Menggunakan JMeter :
1. Buat grup thread
   - Klik kanan pada sisi kiri navigasi bar
   - Pilih Add -> Thread(User) -> Thread Grup
   <img src="/Evaluasi Tengah Semester/Screenshot 3/jmeter step 1.png">
   - Config Thread menjadi forever agar selalu berjalan
   <img src="/Evaluasi Tengah Semester/Screenshot 3/jmeter step 2 config thread.png">
2. Buat request HTTP
   - Klik kanan nama thread grup
   - Pilih Add -> sampler -> HTTP Request
   <img src="/Evaluasi Tengah Semester/Screenshot 3/jmeter step 3.png">
   - Config request HTTP sesuai gambar berikut :
   <img src="/Evaluasi Tengah Semester/Screenshot 3/jmeter step 4.png">
3. Buat report hasil respond (table)
   - Klik kanan nama thread grup
   - Pilih Add -> Listener -> View Result in Table
   <img src="/Evaluasi Tengah Semester/Screenshot 3/jmeter step 5.png">
4. Buat report hasil respond (grafik) **Optional**
   - Klik kanan nama thread grup
   - Pilih Add -> Listener -> Respond Time Graph
   <img src="/Evaluasi Tengah Semester/Screenshot 3/jmeter step 6.png">
5. Jalankan dengan menekan tombol F5 atau segitiga hijau diatas navigasi bar.
   - ketika program sedang melakukan request/load secara terus menerus, matikan dan kill layanan pada ```data3```. Maka terlihat ada jeda sedikit perbedaan pada request time
   <img src="/Evaluasi Tengah Semester/Screenshot 3/data3 matikan jmeter.png">
   
   Status layanan pada manager :
   <img src="/Evaluasi Tengah Semester/Screenshot 3/data3 mati sukses jmeter.png">
   
   Pada report grafik juga akan tampil perbedaan jeda request/load time nya
   
   <img src="/Evaluasi Tengah Semester/Screenshot 3/data3 mati jmeter grafik.png">
