# Instalasi MySQL Cluster Multi Node dengan Load Balancer menggunakan ProxySQL
Creator : Muhammad Isa Senoaji (0511640000078)

Instalasi dalam tutorial ini penulis menggunakan :
 1. Box Ubuntu 16.04
 2. Vagrant
 3. VM Virtual Box

## Daftar Isi
1. [Setting Up Machine menggunakan Vagrant](#1-setting-up-machine-menggunakan-vagrant)
2. [Instalasi MySQL Cluster Multi Node](#2-instalasi-mysql-cluster-multi-node) 
   - [Instalasi NDB Manager](#--instalasi-ndb-manager)
   - [Instalasi Data Node](#--instalasi-data-node)
   - [Instalasi Service Node](#--instalasi-service-node)
3. [Instalasi ProxySQL](#3-instalasi-proxysql)
4. [Testing menggunakan Aplikasi](#4-testing-menggunakan-aplikasi)

## 1. Setting Up Machine Menggunakan Vagrant
Pertama, siapkan konfigurasi Vagrant file guna membuat sekaligus menjalankan ```server```. Dalam kasus ini, penulis menggunakan 1 Load Balancer(ProxySQL),1 NDB Manager, 2 Service API(MySQL Server Client), dan 3 Data Node. 

Arsitektur dan pembagian IP :

| No | Nama |Hostname| IP |
| --- | --- | --- | --- |
| 1. | Load Balancer | proxy |192.168.31.106 |
| 2. | NDB Manager | manager |192.168.31.100 |
| 3. | Service API 1 | service1 | 192.168.31.104 |
| 4. | Service API 2 | service2 |192.168.31.105 |
| 5. | Data Node 1 | data1 | 192.168.31.101 |
| 6. | Data Node 2 | data2 | 192.168.31.102 |
| 7. | Data Node 3 | data3 |192.168.31.103 |

Konfigurasi terdapat pada file bernama ```Vagrant```. Ganti direktori menjadi sesuai tempat penyimpanan file konfigurasi ```Vagrant``` dengan command 

```$ cd (direktori)```

Untuk meng-aktifkan server, jalankan command pada terminal

```$ vagrant up```

Setelah proses pembuatan (bila belum membuat sebelumnya)/menyalakan server (bila sebelumnya sudah melakukan pembuatan) jalankan command 

```$ vagrant ssh (nama server)```

untuk menjalankan server guna instalasi/mengaktifkan layanan pada masing-masing server.

## 2. Instalasi MySQL Cluster Multi Node
###  - Instalasi NDB Manager
Login pada server NDB Manager, download package NDB Manager dengan command 

```
$ wget https://dev.mysql.com/get/Downloads/MySQL-Cluster-7.6/mysql-cluster-community-management-server_7.6.6-1ubuntu16.04_amd64.deb
```

Instal hasil download nya dengan command 

```
$ sudo dpkg -i mysql-cluster-community-management-server_7.6.6-1ubuntu16.04_amd64.deb
```

Buat direktori /var/lib/mysql-cluster untuk menyimpan konfigurasi NDB Managernya 

```
$ sudo mkdir /var/lib/mysql-cluster
```

Edit file pada direktori yang baru dibuat tadi dengan perintah

```
sudo nano /var/lib/mysql-cluster/config.ini
```

Copy isi file pada direktori yang sudah penulis siapkan di /config/manager

<img src="/Screenshot/struktur config.ini.png">

jika sudah pastikan benar IP yang dimasukan, jalankan ndb_mgmd dengan command 

```
$ sudo ndb_mgmd -f /var/lib/mysql-cluster/config.ini
```

sebelum menjalankan service manager, kill dahulu service yang sedang berjalan dengan command

```
$ sudo pkill -f ndb_mgmd
```

edit file konfigurasi management ndb dengan command :

```
$ sudo nano /etc/systemd/system/ndb_mgmd.service
```

Copy paste kan konfig ini :

```
[Unit]
Description=MySQL NDB Cluster Management Server
After=network.target auditd.service

[Service]
Type=forking
ExecStart=/usr/sbin/ndb_mgmd -f /var/lib/mysql-cluster/config.ini
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
Save dan exit, lalu jalankan daemon mode dan enable kan service yang sudah dibuat dengan command

```
$ sudo systemctl daemon-reload
$ sudo systemctl enable ndb_mgmd
```

jalankan service dengan command 

```
$ sudo systemctl start ndb_mgmd
```
cek status service dengan command 

```
$ sudo systemctl status ndb_mgmd
```

Jika berhasil maka akan keluar hasil seperti ini 

<img src="/Screenshot/manager sukses running.png">

jangan lupa untuk allow koneksi dari client dan service dengan command :

```
$ sudo ufw allow from 192.168.31.100
$ sudo ufw allow from 192.168.31.101
$ sudo ufw allow from 192.168.31.102
$ sudo ufw allow from 192.168.31.103
$ sudo ufw allow from 192.168.31.104
$ sudo ufw allow from 192.168.31.105
$ sudo ufw allow from 192.168.31.106
```

### - Instalasi Data Node

Login pada Data Node dan download package untuk cluster node dengan commad :

```
$ wget https://dev.mysql.com/get/Downloads/MySQL-Cluster-7.6/mysql-cluster-community-data-node_7.6.6-1ubuntu16.04_amd64.deb
```

Instal dependency

```
$ sudo apt update
$ sudo apt install libclass-methodmaker-perl
```

Instal package data node 

```
$ sudo dpkg -i mysql-cluster-community-data-node_7.6.6-1ubuntu16.04_amd64.deb
```

Edit konfig pada /etc/my.cnf sesuai dengan yang sudah disiapkan penulis di config/data

```
[mysql_cluster]
# Options for NDB Cluster processes:
ndb-connectstring=192.168.31.100  # location of cluster manager
```

Buat direktori untuk penyimpanan

```
$ sudo mkdir -p /usr/local/mysql/data
$ sudo ndbd  #status data node
```

Allow koneksi dari server-server 

```
$ sudo ufw allow from 192.168.31.100
$ sudo ufw allow from 192.168.31.101
$ sudo ufw allow from 192.168.31.102
$ sudo ufw allow from 192.168.31.103
$ sudo ufw allow from 192.168.31.104
$ sudo ufw allow from 192.168.31.105
$ sudo ufw allow from 192.168.31.106
```

Sebelum membuat service data node, kill dahulu 

```
$ sudo pkill -f ndbd
```

edit konfig pada /etc/systemd/system/ndbd.service

```
$ sudo nano /etc/systemd/system/ndbd.service
```

Paste kode berikut :

```
[Unit]
Description=MySQL NDB Data Node Daemon
After=network.target auditd.service

[Service]
Type=forking
ExecStart=/usr/sbin/ndbd
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Save dan exit, lalu jalankan daemon mode dan enable kan service yang sudah dibuat dengan command

```
$ sudo systemctl daemon-reload
$ sudo systemctl enable ndbd
```

jalankan service dengan command 

```
$ sudo systemctl start ndbd
```
cek status service dengan command 

```
$ sudo systemctl status ndbd
```
Jika berhasil maka akan keluar hasil seperti ini 

<img src="/Screenshot/data1 sukses running.png">

**Lakukan step diatas pada setiap data node**

### - Instalasi Service Node
Login pada service node dan donwload packagenya

```
$ wget https://dev.mysql.com/get/Downloads/MySQL-Cluster-7.6/mysql-cluster_7.6.6-1ubuntu16.04_amd64.deb-bundle.tar
```

buat direktori dan pindah ke direktori tsb ;

```
$ mkdir install
$ cd install
```

extract package hasil download

```
$ tar -xvf mysql-cluster_7.6.6-1ubuntu16.04_amd64.deb-bundle.tar -C
```

update dan instal dependency

```
$ sudo apt update
$ sudo apt install libaio1 libmecab2
```

Instal masing-masing package yang sudah diextract

```
$ sudo dpkg -i mysql-common_7.6.6-1ubuntu16.04_amd64.deb
$ sudo dpkg -i mysql-cluster-community-client_7.6.6-1ubuntu16.04_amd64.deb
$ sudo dpkg -i mysql-client_7.6.6-1ubuntu16.04_amd64.deb
$ sudo dpkg -i mysql-cluster-community-server_7.6.6-1ubuntu16.04_amd64.deb
```

Saat instalasi mysql cluster comunity server, maka akan muncul prompt untuk memasukan password root. masukan password vagrant

Instal mysql server

```
$ sudo dpkg -i mysql-server_7.6.6-1ubuntu16.04_amd64.deb
```

tambahkan kode berikut pada konfigurasi di /etc/mysql/my.cnf

```
. . .
[mysqld]
# Options for mysqld process:
ndbcluster                      # run NDB storage engine
bind-address=192.168.31.104     # based on this IP service

[mysql_cluster]
# Options for NDB Cluster processes:
ndb-connectstring=192.168.31.100  # location of management server
```

restart mysql server 

```
$ sudo systemctl restart mysql
```

enable kan service mysql server 

```
$ sudo systemctl enable mysql
```

**Lakukan step diatas pada semua service**

Verifikasi service node

jalankan command

```
$ mysql -u root -p 
```

akan tampil seperti ini :

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.22-ndb-7.6.6 MySQL Cluster Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>

```

cek dengan command :

```
mysql > SHOW ENGINE NDB STATUS \G
```

jika instalasi berhasil maka akan tampil seperti dibawah ini :

pada service 1:

<img src="/Screenshot/service1 sukses running.png">

pada service 2:

<img src="/Screenshot/service2 sukses running.png">

masuk ke server NDB Manager, jalankan command :

```
$ ndb_mgm
```

lalu command 

```
ndb_mgm > SHOW
```

jika cluster manager (NDB Manager), service serta data node terhubung sukses akan tampil seperti dibawah

<img src="/Screenshot/ndb_mgm struktur.png">

## 3. Instalasi ProxySQL
Download instalasi ProxySQL, disini penulis menggunakan ProxySQL Ubuntu 16.04 Versi 1.4.4

Jalankan command pada server Proxy untuk mendownload

```
$ cd /tmp
$ curl -OL https://github.com/sysown/proxysql/releases/download/v1.4.4/proxysql_1.4.4-ubuntu16_amd64.deb
```

Install package yang sudah didownload dengan command

```
$ sudo dpkg -i proxysql_*
```

Untuk menghubungkan ```Service API``` dengan ProxySQL maka diperlukan instalasi mysql-client. jalankan command 

```
$ sudo apt-get update
$ sudo apt-get install mysql-client
```
Jika sudah, lanjut dengan meng-aktifkan layanan ProxySQL dengan command

```
$ sudo systemctl start proxysql
```

untuk cek apakah sudah berhasil berjalan gunakan command 

```
$ sudo systemctl status proxysql
```

jika berhasil, maka akan tampil seperti berikut :

<img src="/Screenshot/proxy running sukses.png">

### Setting password Admin ProxySQL, secara default awal password nya adalah admin.

```
$ mysql -u admin -p -h 127.0.0.1 -P 6032 --prompt='ProxySQLAdmin> '
```
ganti password(jika ingin mengganti password untuk admin ProxySQL dengan command

```
ProxySQLAdmin > UPDATE global_variables SET variable_value='admin:passwordbaru' WHERE variable_name='admin-admin_credentials';
ProxySQLAdmin > LOAD ADMIN VARIABLES TO RUNTIME;
ProxySQLAdmin > SAVE ADMIN VARIABLES TO DISK;
```


### Konfigurasi Monitoring di Service API

Download file sql addition yang sudah disiapkan dengan command pada kedua service API

```
$ curl -OL https://gist.github.com/lefred/77ddbde301c72535381ae7af9f968322/raw/5e40b03333a3c148b78aa348fd2cd5b5dbb36e4d/addition_to_sys.sql
```
lanjut command untuk memasukan konfigurasi dalam file sql kedalam service

```
$ mysql -u root -p < addition_to_sys.sql
```

login ke MySQL

```
$ mysql -u root -p
```

buat user bernama monitor

```
CREATE USER 'monitor'@'%' IDENTIFIED BY 'monitorpassword';
GRANT SELECT on sys.* to 'monitor'@'%';
FLUSH PRIVILEGES;
```
**note : lakukan step diatas pada semua service API**


### Konfigurasi monitoring pada ProxySQL

```
ProxySQLAdmin > UPDATE global_variables SET variable_value='monitor' WHERE variable_name='mysql-monitor_username';
ProxySQLAdmin > LOAD MYSQL VARIABLES TO RUNTIME;
ProxySQLAdmin > SAVE MYSQL VARIABLES TO DISK;
```


Daftarkan service API kedalam ProxySQL

```
ProxySQLAdmin > INSERT INTO mysql_group_replication_hostgroups (writer_hostgroup, backup_writer_hostgroup, reader_hostgroup, offline_hostgroup, active, max_writers, writer_is_also_reader, max_transactions_behind) VALUES (2, 4, 3, 1, 1, 3, 1, 100);
ProxySQLAdmin > INSERT INTO mysql_servers(hostgroup_id, hostname, port) VALUES (2, '192.168.31.104', 3306);
ProxySQLAdmin > INSERT INTO mysql_servers(hostgroup_id, hostname, port) VALUES (2, '192.168.31.105', 3306);

ProxySQLAdmin > LOAD MYSQL SERVERS TO RUNTIME;
ProxySQLAdmin > SAVE MYSQL SERVERS TO DISK;
```

jalankan command berikut untuk mengecek apakah Service API sudah terhubung dengan ProxySQL

```
ProxySQLAdmin > SELECT hostgroup_id, hostname, status FROM runtime_mysql_servers;
```

Pastikan hasil output seperti gambar ini :

<img src="/Screenshot/sukses link proxy-service.png">


### Buat User MySQL pada Service API untuk dapat diakses melalui ProxySQL
login pada server service API

jalankan command 

```
service1 > mysql -u root -p
mysql > CREATE USER 'mysqlcluster'@'%' IDENTIFIED BY 'vagrant';
mysql > GRANT ALL PRIVILEGES on clustertest.* to 'mysqlcluster'@'%';
mysql > FLUSH PRIVILEGES;
mysql > exit;
```

### Buat User MySQL pada ProxySQL agar dapat diakses melalui aplikasi 
Pada ProxySQL, jalankan command :

```
ProxySQLAdmin > INSERT INTO mysql_users(username, password, default_hostgroup) VALUES ('mysqlcluster', 'vagrant', 2);
ProxySQLAdmin > LOAD MYSQL USERS TO RUNTIME;
ProxySQLAdmin > SAVE MYSQL USERS TO DISK;
```



## 4. Testing Menggunakan Aplikasi
Dalam testing kali ini, penulis menggunakan aplikasi MySQL Workbench untuk memastikan apakah proxy dapat diakses.
Buat koneksi baru dengan input IP Address 192.168.31.106 (alamat ProxySQL) dengan user mysqlcluster dan password vagrant.
bila sudah, lakukan ujicoba cek host name dengan command :

```
select @@hostname
```

maka bila sukses akan tampil seperti gambar ini :

<img src="/Screenshot/testing.png">

jika diputus pada salah satu service pun maka akan segera mengganti ke service yang lain

<img src="/Screenshot/testing2.png">

