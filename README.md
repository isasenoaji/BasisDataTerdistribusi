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
Pertama, siapkan konfigurasi Vagrant file guna membuat sekaligus menjalankan ```Machine```. Dalam kasus ini, penulis menggunakan 1 Load Balancer(ProxySQL),1 NDB Manager, 2 Service API(MySQL Server Client), dan 3 Data Node. 
Pembagian IP :

| No | Nama | IP |
| --- | --- | --- |
| 1. | Load Balancer | 192.168.31.106 |
| 2. | NDB Manager | 192.168.31.100 |
| 3. | Service API 1 | 192.168.31.104 |
| 4. | Service API 2 | 192.168.31.105 |
| 5. | Data Node 1 | 192.168.31.101 |
| 6. | Data Node 2 | 192.168.31.102 |
| 7. | Data Node 3 | 192.168.31.103 |

## 2. Instalasi MySQL Cluster Multi Node

## 3. Instalasi ProxySQL

## 3. Testing Menggunakan Aplikasi
Dalam testing kali ini, penulis menggunakan aplikasi MySQL Workbench untuk memastikan apakah proxy dapat diakses.
