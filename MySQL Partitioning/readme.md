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
   
   
 ## Create Partisi
 
 ### Range Partition
 
 Untuk membuat range partisi, jalankan query berikut pada service :
 
 Case table Userlogs
 ```
 CREATE TABLE userslogs_range (
    username VARCHAR(20) NOT NULL,
    logdata BLOB NOT NULL,
    created DATETIME NOT NULL,
    PRIMARY KEY(username, created)
)
PARTITION BY RANGE( YEAR(created) )(
    PARTITION from_2013_or_less VALUES LESS THAN (2014),
    PARTITION from_2014 VALUES LESS THAN (2015),
    PARTITION from_2015 VALUES LESS THAN (2016),
    PARTITION from_2016_and_up VALUES LESS THAN MAXVALUE);

 ```
 
 Case Rc1
 
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


# 2. Typical Use Case : Time Series Data

## Explain

## Query dan Running Time

## Delete dan Running Time
