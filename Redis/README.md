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



## 3. CRUD Test

## 4. Fail Over Test
