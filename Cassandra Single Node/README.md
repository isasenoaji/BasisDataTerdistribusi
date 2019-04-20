# Cassandra Single Node Instalation with CRUD

## Table of Content

1. [Definisi dan Perbedaan](#1-definisi-dan-perbedaan)
2. [Arsitektur Server](#2-arsitektur-server)
3. [Instalasi](#3-instalasi)
4. [Dataset dan Penjelasan](#4-dataset-dan-penjelasan)
5. [Import Dataset](#5-import-dataset)
6. [CRUD](#6-crud)

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

## 3. Instalasi

## 4. Dataset dan Penjelasan

## 5. Import Dataset

## 6. CRUD
