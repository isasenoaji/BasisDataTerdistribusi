# Instalasi MySQL Cluster Multi Node dengan Load Balancer menggunakan ProxySQL
Creator : Muhammad Isa Senoaji (0511640000078)

Instalasi dalam tutorial ini penulis menggunakan :
 1. Box Ubuntu 16.04
 2. Vagrant
 3. VM Virtual Box

## Daftar Isi
1. [Setting Up Machine menggunakan Vagrant](#1-setting-up-machine-menggunakan-vagrant)
2. [Instalasi MySQL Cluster Multi Node](#2-instalasi-mysql-cluster-multi-node) 
   - Instalasi NDB Manager
   - Instalasi Data Node
   - Instalasi Service Node
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

Konfigurasi terdapat pada file bernama ```Vagrant```. Jalankan command pada terminal

```$ vagrant up```

Setelah proses pembuatan (bila belum membuat sebelumnya)/menyalakan server (bila sebelumnya sudah melakukan pembuatan) jalankan command 

```$ vagrant ssh (nama server)```

untuk menjalankan server guna instalasi/mengaktifkan layanan pada masing-masing server.

## 2. Instalasi MySQL Cluster Multi Node

## 3. Instalasi ProxySQL

## 3. Testing Menggunakan Aplikasi
Dalam testing kali ini, penulis menggunakan aplikasi MySQL Workbench untuk memastikan apakah proxy dapat diakses.
