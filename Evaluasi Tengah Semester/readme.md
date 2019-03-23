# Instalasi Wordpress dengan database MySQL Cluster

# Prequisites
1. Ubuntu 16.04
2. Apache2
3. PHP 7.0

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

Tambahkan akses pada user ```mysqlcluster``` agar bisa mengakses database wordpress dengan query :

```
GRANT ALL PRIVILEGES on wordpress.* to 'mysqlcluster'@'%';
FLUSH PRIVILEGES;
```

Note :

Karena MySQL Cluster hanya support engine NDBCluster, maka kita perlu set default engine pada semua service pada /etc/my.cnf dengan format diletakan dibawah bind-address :

```
default-storage-engine=ndbcluster
```

restart service mysql

```
sudo systemctl restart mysql
```




## 3. Uji Coba Fail Over

## 4. Respond Time Test using JMeter
