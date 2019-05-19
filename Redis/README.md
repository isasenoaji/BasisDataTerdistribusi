# REDIS IMPLEMENTATION

## Shorcut
   1. [Arsitektur](#1-arsitektur)
   2. [Instalasi](#2-instalasi)
   3. [CRUD Test](#3-crud-test)
   4. [Fail Over Test](#4-fail-over-test)

## 1. Arsitektur
Arsitektur yang digunakan menggunakan 1 master dan 2 slave sebagai berikut :

| No | Nama | Hostname | IP |
|---|------|-------|----|
|1.| Master | Master1 | 192.168.33.100 |
|2.| Slave-1 | rSlave1 | 192.168.33.101 |
|3.| Slave-2 | rSlave2 | 192.168.33.102 |

## 2. Instalasi

Lakukan download file konfigurasi redis pada setiap node dengan command :

```
sudo apt-get update 
sudo apt-get install build-essential tcl
sudo apt-get install libjemalloc-dev  (Optional)
```

Setelah selesai, instal dengan command :

```
curl -O http://download.redis.io/redis-stable.tar.gz
tar xzvf redis-stable.tar.gz
cd redis-stable
make
make test
sudo make install
```

Setelah melakukan penginstalan dimasing-masing node pada langkah diatas, kita perlu melakukan konfigurasi pada masing2 node.

terdapat 2 jenis file yang harus di konfigurasi, yaitu file redis.conf dan sentinel.conf. redis.conf berfungsi sebagai pengaturan utama redis sedangkan sentinel.conf berfungsi sebagai pengaturan sentinel/bila terjadi fail over.

#### Konfigurasi Redis.conf
##### Pada Master

```
protected-mode no
port 6379
logfile "/home/vagrant/redis-stable/redis.log"
```

##### Pada Kedua Slave

```
protected-mode no
port 6379
slaveof 192.168.31.100 6379
logfile "/home/vagrant/redis-stable/redis.log"
```

#### Konfigurasi Sentinel.conf
##### Pada Master dan Slave

```
protected-mode no
logfile "/home/vagrant/redis-stable/sentinel.log"
sentinel monitor mymaster 192.168.31.100 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 10000
```

Catatan : 

pastikan penulisan IP adalah Benar dikarenakan hal tersebut yang menjadi pemicu utama dan angka 2 menrupakan angka quorum pada election ketika terjadi fail over.


## 3. CRUD Test

Untuk dapat menggunakan redis, kita perlu menjalankan redis-server yang berada pada folder redis-stable/src. ganti ke direktori tersebut dan jalankan perintah berikut pada masing2 node :

```
src/redis-server redis.conf &
src/redis-server sentinel.conf --sentinel &
```

gunakan command berikut untuk mengecek apakan=h redis sudah berjalan atau belum :

```
ps -f | grep redis
```

akan tampil seperti berikut :

Untuk mengecek info/status Redis-Cluster, masuk ke redis-cli dan masukan info replication :

-Master

-rslave1

-rslave2



## 4. Fail Over Test
