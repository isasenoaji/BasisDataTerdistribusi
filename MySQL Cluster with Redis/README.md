# MySQL CLuster with Redis Using Wordpress

Wordpress with MySQL CLuster Architecture and Redis Cluster

## Daftar Isi
   - [Arsitektur](#arsitektur)
   - [Instalasi MySQL Cluster](#instalasi-mysql-cluster)
   - [Instalasi Wordpress](#instalasi-wordpress)
   - [Instalasi Redis](#instalasi-redis)
   - [Konfigurasi Redis](#konfigurasi-redis-pada-wp)
   - [Uji Coba](#uji-coba)
   

## 1. Arsitektur

Arsitektur yang digunakan adalah sebagai berikut :

| No | Nama |Hostname| IP |
| --- | --- | --- | --- |
| 1. | Load Balancer | proxy |192.168.31.106 |
| 2. | NDB Manager | manager |192.168.31.100 |
| 3. | Service API 1 | service1 | 192.168.31.104 |
| 4. | Service API 2 | service2 |192.168.31.105 |
| 5. | Data Node 1 | data1 | 192.168.31.101 |
| 6. | Data Node 2 | data2 | 192.168.31.102 |
| 7. | Data Node 3 | data3 |192.168.31.103 |
|8.| Master Redis | master1 |192.168.31.10 |
|9.| Slave1 Redis | rslave1 | 192.168.31.11 |
|10.| Slave2 Redis | rslave2 | 192.168.31.12 |

## 2. Instalasi MySQL Cluster

Untuk instalasi MySQL Cluster, Anda dapat melihatnya di <a href="https://github.com/isasenoaji/BasisDataTerdistribusi">sini</a>

## 3. Instalasi Wordpress

Untuk instalasi Wordpress pada MySQL Cluster, Anda dapat melihatnya di <a href="https://github.com/isasenoaji/BasisDataTerdistribusi/tree/master/Evaluasi%20Tengah%20Semester">sini</a>

## 4. Instalasi Redis

Untuk instalasi Redis Cluster, Anda dapat melihatnya di <a href="https://github.com/isasenoaji/BasisDataTerdistribusi/tree/master/Redis">sini</a>

## 5. Konfigurasi Redis Pada WP

Setelah memastikan semua point-point diatas telah tercapai tanpa error. maka hal selanjutnya adalah dengan mengkonfigurasikan wordpress agar dapat terhubung dengan Redis yang telah dibuat.

Pertama, download Plugin 'Redis Object Cache' pada wordpress Anda. download dan install. Jika sudah tambahkan baris Redis Sentinel pada file ```wp-config.php``` dimana letak instalasi wordpress berada(dalam case saya install di proxy). letakan dibawah konfigurasi databasenya.

```
define( 'WP_REDIS_CLIENT', 'predis' );
define( 'WP_REDIS_SENTINEL', 'mymaster' );
define( 'WP_REDIS_SERVERS', [
    'tcp://192.168.31.10:26379?alias=master',
    'tcp://192.168.31.11:26379?alias=slave1',
    'tcp://192.168.31.12:26379?alias=slave2',
] );
```

Setelah itu, buka plugin yang telah di download dan instal tadi. dan pastikan list redis sudah muncul. Dan jika sudah muncul, aktifkan plugin maka akan tampil seperti berikut ini :

<img src="\MySQL Cluster with Redis\screenshot\redis.PNG">

## 6. Uji Coba
   
