## HIVE
_Tables could be external and internal by default table is internal_

```$xslt
create table if not exists table1 (col1 string,col2 array<string>,col3 string,col4 int) row 
\format delimited  fields terminated by',' 
\collection items terminated by':' 
\lines terminated by '\n' stored as textfile;
```
_Set metastore_
```$xslt
 set hive.metastore.warehouse.dir;
```
_Set custom metastore_
```$xslt
    create table if not exists table7 (col1 string,col2 array<string>,col3 string,col4 int) 
    \row format delimited  fields terminated by',' 
    \collection items terminated by':' lines terminated by '\n' 
    \stored as textfile location '/user/dzmitry/table3';
   ```
   
_Create External table in which if it was dropped the metadata will be gone, however table will still be in HDFS_
   ```$xslt
    create external table if not exists table12 (col1 string,col2 array<string>,col3 string,col4 int) 
    row format delimited  fields terminated by',' 
    collection items terminated by':' lines terminated by '\n' 
    stored as textfile location '/user/dzmitry/table3';
   ```
   
_Link Metadata_
> From Local
```$xslt
load data local inpath '/tmp/table1.txt' into table table1;
```
> From HDFS
```$xslt
load data  inpath 'dzmitry/data/table1.txt' into table table1;
```
```$xslt
Hadoop can be accessed on host:50070
```
```$xslt
create table if not exists assignment1
                          (cust_id STRING,  
                           cust_name STRING ,
                           odr_date STRING,
                           shipdt STRING,
                           Courer STRING,
                           recvd_dt STRING,
                           returned_or_not STRING,
                           returned_dt STRING,
                           reson_of_return STRING)
                           row format delimited  fields terminated by','
                           lines terminated by '\n'
                           tblproperties("skip.header.line.count"="1");
                           
load data local inpath '/tmp/assignment-create-table-2018.txt' overwrite into table assignment1;
select cust_id, from_unixtime(unix_timestamp(odr_date, 'dd-MM-yyyy'),'dd-MM-yyyy') from assignment1 limit 10;

```

> Insert from another table
```$xslt
insert into table table3 select col1,col2,col3 from table1;
insert overwrite table table3 select col1,col2,col3 from table1;
```
> Multiinsert into table 
```$xslt

create table movies_genre(name string,actors array<string>,genre string,rating int)
row format delimited fields terminated by','
collection items terminated by':' 
lines terminated by'\n' stored as textfile;

load data local inpath'/tmp/movies-genre.txt'into table movies_genre;
create table drama(name string,actors array<string>,genre string,rating int) stored as textfile;
create table fiction(name string,actors array<string>,genre string,rating int) stored as textfile;

from movies_genre insert into table drama select name,actors,genre,rating 
where genre='drama' 
insert overwrite table fiction select name,actors,genre,rating 
where genre='science fiction';
```
> Alter Table
```$xslt

create table if not exists table9(col1 int,col2 string,col3 string,col4 int)row format delimited fields terminated by',' lines terminated by'\n' stored as textfile;

load data local inpath'/home/jivesh/files/dynamic'into table table9;

alter table table5 rename to table10;
desc table9;

alter table table10 change column col3 expalination int;
alter table table10 change column  expalination col3 string;


alter table table10 change column col1 id int after col2;

alter table table10 add columns (col5 int,col6 string);
desc table10;
select * from table10;
```

>SPECIFY COLUMNS TO KEEP IN REPLACE STATEMENT!!!
```
alter table table10 replace columns (col7 int,col8 int);
desc table10;
select * from table10;

alter table table10 set tblproperties('creator'='jivesh');
desc formatted table10;

alter table table10 set fileformat sequencefile;

```
> ORDER BY,DISTRIBUTE,SORT BY,CLUSTER BY
_P.S. CLUSTER BY same as DISTRIBUTE  by SORT BY_
```$xslt
aa,1
bb,1
dd,5
ef,2
teh,1
dth,4
dfh,5

select col2 from table11 order by col2;
select col2 from table11 sort  by col2;
select col2 from table11 DISTRIBUTE  by col2;
select col2 from table11 DISTRIBUTE  by col2 SORT BY col2;
SAME!
select col2 from table11 CLUSTER BY col2;
```
> DATE AND MATH FUNCTIONS
```$xslt
SELECT unix_timestamp('2018-10-08 00:00:00');
WILL TURN INTO UNIX TIMESTAMP
SELECT from_unixtime(1538956800);
TO DATE('2018-10-08 00:00:00'); => 2018-10-08
SELECT DATEDIFF('2018-10-08','2018-10-29');
SELECT DATE_SUB('2018-10-08',55);
```
> MATH
```$xslt
hive> select ceil(9.5);
OK
10

hive> select floor(9.5);
OK
9

hive> select round(9.5);
OK
10.0

hive> select rand();
OK
0.5251216166526347

```
> STRING FUNCTIONS
```$xslt
hive> select concat(col1,'-',col3) from table1;
OK
499-England
501-England

hive> select length(col3) from table1;
OK
7
7

hive> select lower(col3) from table1;
OK
england
england

hive> select upper(col3) from table1;
OK
ENGLAND
ENGLAND

hive> select lpad(col3,10,'v') from table1;
OK
vvvEngland
vvvEngland

hive> select rpad(col3,10,'v') from table1;
OK
Englandvvv
Englandvvv

hive> select ltrim('    Dzmitry');
OK
Dzmitry

hive> select rtrim('    Dzmitry');
OK
Dzmitry

hive> select repeat(col3,2) from table1;
OK
EnglandEngland
EnglandEngland

hive> select reverse(col3) from table1;
OK
dnalgnE

hive> select split('hive:hadoop',':');
OK
["hive","hadoop"]

hive> select substr('hive is quering tool',4);
OK
e is quering tool

hive> select substr('hive is quering tool',4,3);
OK
e i

hive> select instr('hive is quering tool','i');
OK
2


```
> CONDITIONALS
```$xslt
hive> select * from table1;
OK
499     ["Poole","GBR"] England 141000
501     ["Blackburn","GBR"]     England 140000
500     ["Bolton","GBR"]        England 139020
502     ["Newport","GBR"]       Wales   139000
503     ["PrestON","GBR"]       England 135000
504     ["Stockport","GBR"]     England 132813

hive> select if(col3='England',col1,col4) from table1;
OK
499
501
500



hive> select isnull(col1) from table1;
 OK
 false
 false
 false

hive> select isnotnull(col1) from table1;
 OK
 true
 true
 true
 
hive> select col1 , NVL(col3,col4) from table1;
NVL( ifvlue incolumn, else value incolumn)
OK
499     England
501     England
500     England

 
```
> ARRAY  FUNCTIONS
```$xslt
hive> select * from table1;
OK
499     ["Poole","GBR"] England 141000
501     ["Blackburn","GBR"]     England 140000
500     ["Bolton","GBR"]        England 139020
502     ["Newport","GBR"]       Wales   139000
503     ["PrestON","GBR"]       England 135000
504     ["Stockport","GBR"]     England 132813

ONLY FOR ONE COLUMN
hive> select explode(col2) from table1;
OK
Poole
GBR
Blackburn
GBR
FOR MULTBLE COLUMNS
hive> select col3,col2 from table1 lateral view explode(col2) dummy_table as dummy_table;
England ["Poole","GBR"]
England ["Poole","GBR"]
England ["Blackburn","GBR"]
England ["Blackburn","GBR"]
England ["Bolton","GBR"]
England ["Bolton","GBR"]

```
> Rlike
```$xslt
hive> select 'hadoop' rlike 'ha';
OK
true
or JAVA EXPRESSION
Time taken: 0.079 seconds, Fetched: 1 row(s)
hive> select 'hadoop' rlike 'ha*';
OK
f
```