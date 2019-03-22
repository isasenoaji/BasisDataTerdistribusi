# MySQL Partitioning using InnoDb in MySQL Cluster

## Daftar Isi :

1. Create Partisi :
   - [Range Partition](#range-partition)
   - [List Partition](#list-partition)
   - [Hash Partition](#hash-partition)
   - [Key Partition](#key-partition)
2. Typical Use Case : Time Series Data
   - [Explain](#explain)
   - [Query dan Running Time](#query-dan-running-time)
   - [Delete dan Running Time](#delete-dan-running-time)
   - [Conclusion](#conclusion)
   
   
 ## Create Partisi
 
 ### Range Partition
 
 Untuk membuat range partisi, jalankan query berikut pada service :
 
 ```
 CREATE TABLE rc1_range (
    a INT,
    b INT
)
PARTITION BY RANGE COLUMNS(a, b) (
    PARTITION p0 VALUES LESS THAN (5, 12),
    PARTITION p3 VALUES LESS THAN (MAXVALUE, MAXVALUE)
);

```

<img src="/MySQL Partitioning/Screenshot 2/range sukses.png"> 

Insert data dengan query :

```
INSERT INTO rc1_range (a,b) VALUES (4,11);
INSERT INTO rc1_range (a,b) VALUES (5,11);
INSERT INTO rc1_range (a,b) VALUES (6,11);
INSERT INTO rc1_range (a,b) VALUES (4,12);
INSERT INTO rc1_range (a,b) VALUES (5,12);
INSERT INTO rc1_range (a,b) VALUES (6,12);
INSERT INTO rc1_range (a,b) VALUES (4,13);
INSERT INTO rc1_range (a,b) VALUES (5,13);
INSERT INTO rc1_range (a,b) VALUES (6,13);
```

untuk melihat partisi yang sudah dibentuk gunakan query :

```
explain select * from rc1_range;
```


<img src="/MySQL Partitioning/Screenshot 2/explain range.png"> 

## List Partition

Untuk membuat gunakan query berikut :

```
CREATE TABLE rc1_list (
    a INT NULL,
    b INT NULL
)
PARTITION BY LIST COLUMNS(a,b) (
    PARTITION p0 VALUES IN( (0,0), (NULL,NULL) ),
    PARTITION p1 VALUES IN( (0,1), (0,2), (0,3), (1,1), (1,2) ),
    PARTITION p2 VALUES IN( (1,0), (2,0), (2,1), (3,0), (3,1) ),
    PARTITION p3 VALUES IN( (1,3), (2,2), (2,3), (3,2), (3,3) )
);
```


<img src="/MySQL Partitioning/Screenshot 2/list sukses.png"> 


Insert data dengan query berikut :

```
INSERT INTO rc1_list (a,b) VALUES (0,0);
INSERT INTO rc1_list (a,b) VALUES (NULL,NULL);
INSERT INTO rc1_list (a,b) VALUES (0,1);
INSERT INTO rc1_list (a,b) VALUES (1,0);
INSERT INTO rc1_list (a,b) VALUES (1,3);
```

Cek dengan query :

```
explain select * from rc1_list;
```


<img src="/MySQL Partitioning/Screenshot 2/list explain.png"> 

## Hash Partition

Untuk membuat gunakan query :


```
CREATE TABLE rc1_hash (
    a INT NULL,
    b INT NULL
)
PARTITION BY HASH (a)
PARTITIONS 10;
```



Insert data dengan query :

```
INSERT INTO rc1_hash (a,b) VALUES (4,11);
INSERT INTO rc1_hash (a,b) VALUES (5,11);
INSERT INTO rc1_hash (a,b) VALUES (6,11);
INSERT INTO rc1_hash (a,b) VALUES (4,12);
INSERT INTO rc1_hash (a,b) VALUES (5,12);
INSERT INTO rc1_hash (a,b) VALUES (6,12);
INSERT INTO rc1_hash (a,b) VALUES (4,13);
INSERT INTO rc1_hash (a,b) VALUES (5,13);
INSERT INTO rc1_hash (a,b) VALUES (6,13);
```

Cek dengan query :

```
explain select * from rc1_hash;
```
<img src="/MySQL Partitioning/Screenshot 2/hash explain.png"> 

## Key Partition

Untuk membuat gunakan query :


```
CREATE TABLE rc1_key (
    a INT NOT NULL,
    b INT NULL,
	PRIMARY KEY(a)
)
PARTITION BY KEY (a)
PARTITIONS 10;
```

<img src="/MySQL Partitioning/Screenshot 2/key sukses.png"> 
Insert data dengan query :

```
INSERT INTO rc1_key (a,b) VALUES (4,11);
INSERT INTO rc1_key (a,b) VALUES (5,11);
INSERT INTO rc1_key (a,b) VALUES (6,11);
```

Cek dengan query :

```
explain select * from rc1_key;
```

<img src="/MySQL Partitioning/Screenshot 2/key explain.png"> 


# 2. Typical Use Case : Time Series Data

## Explain
Import data dahulu file <a href="https://drive.google.com/open?id=0B2Ksz9hP3LtXRUppZHdhT1pBaWM">disini</a> ke server anda

jalankan query 

```
explain select * from measures;
explain select * from partitioned_measures;
```

<img src="/MySQL Partitioning/Screenshot 2/explain.png"> 

## Query dan Running Time

Disini akan diuji running time pada query select antara non partisi dengan partisi

jalankan query 

```
SELECT SQL_NO_CACHE
    COUNT(*)
FROM
    measures
WHERE
    measure_timestamp >= '2016-01-01'
        AND DAYOFWEEK(measure_timestamp) = 1;
	
SELECT SQL_NO_CACHE
    COUNT(*)
FROM
    partitioned_measures
WHERE
    measure_timestamp >= '2016-01-01'
        AND DAYOFWEEK(measure_timestamp) = 1;
```

Outpun lokal penulis :

<img src="/MySQL Partitioning/Screenshot 2/select benchmark before.png">

Mungkin hasil perbedaan running time tidak jauh berbeda, namun jika kita hapus index pada ```measure_time``` maka akan nampak jelas seperti berikut :

Jalankan query berikut untuk menghapus index :

```
ALTER TABLE `measures` 
DROP INDEX `measure_timestamp` ;
 
ALTER TABLE `partitioned_measures` 
DROP INDEX `measure_timestamp` ;
```
<img src="/MySQL Partitioning/Screenshot 2/remove index benchmark.png">
berikut perbedaannya :

<img src="/MySQL Partitioning/Screenshot 2/select benchmark after.png">

## Delete dan Running Time

pada section ini, akan diuji perbedaan running time dalam penghapusan besar data 

tambah dahulu index yang sudah dihapus :

```
ALTER TABLE `measures` 
ADD INDEX `index1` (`measure_timestamp` ASC);

ALTER TABLE `partitioned_measures` 
ADD INDEX `index1` (`measure_timestamp` ASC);
```

jalankan query delete :

```
DELETE
FROM measures
WHERE  measure_timestamp < '2016-01-01';

ALTER TABLE partitioned_measures DROP PARTITION prev_year_logs ;
```

Lihat perbandingan hasil akhirnya  berikut ini :

<img src="/MySQL Partitioning/Screenshot 2/delete benchmark.png">

## Conclusion

Dari hasil uji query antara partisi dan non partisi, sangat jauh sekali perbandingannya. jelas bahwa partisi lebih efisien.
Ingat! ketika membuat partisi pastikan semua aturan partisi terpenuhi.

Trimakasih
