---
layout: post
title:  "MySQL学习笔记"
categories: MySQL
tags: MySQL
author: ZhangJie
---

* content
{:toc}
MySQL学习笔记。




## 基础知识

### 列属性（列约束）

#### 1. 主键
- 能唯一标识记录的字段，可以作为主键；
- 一个表只能有一个主键；
- 主键具有唯一性；
- 声明字段时，用 primary key 标识，也可以在字段列表之后声明，例：create table tab ( id int, stu varchar(10), primary key (id))；
- 主键字段的值不能为null；
- 主键可以由多个字段共同组成，此时需要在字段列表后声明的方法，例：create table tab ( id int, stu varchar(10), age int, primary key (stu, age))。

#### 2. unique唯一索引（唯一约束）

- 使得某字段的值不能重复。

#### 3. null约束

- null不是数据类型，是列的一个属性；
- 表示当前列是否可以为null，表示什么都没有；
- null, 允许为空，默认；
- not null, 不允许为空。

#### 4. auto_increment自动增长约束
- 自动增长必须为索引（主键或unique）；
- 只能存在一个字段为自动增长；
- 默认为1开始自动增长。

### MySQL数据类型

#### 1. 数值类型

类型 | 大小  | 范围（有符号）  | 范围（无符号）
---|---|---|---
TINYINT | 1 字节 | (-128，127) | (0，255)
SMALLINT | 2 字节 | (-32768，32767) | (0，65535)
MEDIUMINT | 3 字节 | (-8388608，8388607) | 	(0，16777215)
INT或INTEGER | 4 字节 | (-2147483648，2147483647) | (0，4294967295)
BIGINT | 8 字节 | ... | ... 
FLOAT | 4 字节 | ... | ... 
DOUBLE | 8 字节 | ... | ... 


#### 2. 字符串类型

类型 | 大小 | 用途
---|---|---
CHAR |0-255字节 |定长字符串
VARCHAR |0-65535字节 |变长字符串
TINYBLOB |0-255字节 |不超过 255 个字符的二进制字符串
TINYTEXT |0-255字节 |短文本字符串
BLOB |0-65535字节 |二进制形式的长文本数据
TEXT |0-65535字节 |长文本数据
MEDIUMBLOB |0-16777215字节 | 	二进制形式的中等长度文本数据
MEDIUMTEXT |0-16777215字节 | 中等长度文本数据
LONGBLOB | 0-4294967295字节 | 	二进制形式的极大文本数据
LONGTEXT | 0-4294967295字节 | 极大文本数据


- 经常变化的字段用varchar
- 知道固定长度的用char
- 尽量用varchar
- 超过255字符的只能用varchar或者text
- 能用varchar的地方不用text


#### 3. 日期和时间类型

类型 | 大小(字节) | 范围 | 格式 | 用途
---|---|---|---|---
DATE | 3 | 1000-01-01 到 9999-12-31 | YYYY-MM-DD | 	日期值
TIME | 3 | '-838:59:59' 到 '838:59:59' | HH:MM:SS | 	时间值或持续时间
YEAR | 1 | 1901 到 2155 | YYYY | 年份值
DATETIME | 8 | 1000-01-01 00:00:00 到 9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值
TIMESTAMP | 4 | 1970-01-01 00:00:00 到 2038 结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07 | YYYYMMDD HHMMSS | 混合日期和时间值，时间戳


### 事务

在确定哪些事务存储引擎可用之后，可以通过在CREATE TABLE语句中增加ENGINE = tbl_engine子句来创建使用给定引擎的表：
```sql
CREATE TABLE t1(
    i int
) ENGINE = InnoDB;
```

更改表使用的事务存储引擎：
```sql
ALTER TABLE t1 ENGINE = InnoDB;
```

在改变一个表之前，需要考虑到将其改变为使用事务存储引擎的表可能会在其他方面影响其行为。

使用START TRANSACTION(或BEGIN)语句以挂起自动提交模式，然后发出组成事务的语句，如果语句执行成功，将结果记录到DB并通过发出COMMIT来结束事务。

如果发生错误, 则不能使用COMMIT, 相反地, 应该通过ROLLBACK语句取消事务。
```sql
START TRANSACTION;

INSERT INTO t1(i) values(1);
INSERT INTO t1(i) values(2);

COMMIT;
```
不是一切都可以取消的，事务具有局限性，如果发起了一个DROP DATABASE语句，不可能通过执行ROLLBACK来恢复DB。




## 一些有用的小技巧

- Windows中mysql，按F7，将会显示一个最近所使用的命令的菜单。

- 如果查询结果太长，不便于阅读，可以在结尾加上'\G'，垂直输出。

## 基本操作


- 查看表

```sql
SHOW TABLES [LIKE 'pattern'];
SHOW TABLES FROM 数据库名;
```

- 删除表
```sql
DROP TABLE [IF EXISTS] 表名;
```

- 清空表数据
```sql
TRUNCATE [TABLE] 表名;
```

- 修改表数据
```sql
UPDATE 表名 SET 字段名=新值 [, 字段名=新值] [更新条件]
```

- SELECT详解
```sql
select [all|distinct] select_expr from -> where -> group by [合计函数] -> having -> order by -> limit
```

- group by 子句, 分组子句
```
group by 字段/别名 [排序方式]
分组后会进行排序。升序：ASC，降序：DESC
    
以下[合计函数]需配合 group by 使用：
count 返回不同的非NULL值数目
sum 求和
max 求最大值
min 求最小值
avg 求平均值
```

- having 子句，条件子句
```
与 where 功能、用法相同，执行时机不同。
where 在开始时执行检测数据，对原数据进行过滤。
having 对筛选出的结果再次进行过滤。
having 字段必须是查询出来的，where 字段必须是数据表存在的。
where 不可以使用字段的别名，having 可以。因为执行WHERE代码时，可能尚未确定列值。
where 不可以使用合计函数。一般需用合计函数才会用 having
SQL标准要求HAVING必须引用GROUP BY子句中的列或用于合计函数中的列。
```

- order by 子句，排序子句
```
order by 排序字段/别名 排序方式 [,排序字段/别名 排序方式]...
升序：ASC，降序：DESC
支持多个字段的排序
```

- limit 子句，限制结果数量子句
```
仅对处理好的结果进行数量限制。
将处理好的结果的看作是一个集合，按照记录出现的顺序，索引从0开始。
limit 起始位置, 获取条数。省略第一个参数，表示从索引0开始。
```

- distinct, all 选项
```
- distinct去除重复记录；
- 默认为all, 全部记录。
```


- 格式化datetime
```sql
-- t datetime, '2018-08-11 10:12:32'
mysql> select date_format(t, '%M %e, %Y') from mail;
+-----------------------------+
| date_format(t, '%M %e, %Y') |
+-----------------------------+
| August 11, 2018             |
+-----------------------------+
```



- if(可读性好), ifnull(效率高)
```sql
-- 若id为空，显示"unknown"， 否则显示id。
mysql> select if(id is null, "unknown", id) as "id" from taxpayer;
mysql> select ifnull(id, "unknown") as "id" from taxpayer;
```

- 拷贝表

```sql
-- 新建一张和现有的某张表结构一致的表(不含数据)
create table new_table like original_table;

-- 新建一张和现有的某张表结构一致的表, 同时拷贝数据
insert into new_table select * from original_table;
```


- 合并队列来构建复合值

```sql
mysql> select concat(srcuser, '@', srchost) as sender from mail;
+------------+
| sender     |
+------------+
| jason@mars |
+------------+
```

- 修改表所使用的存储引擎(耗时、耗CPU、I/O资源)

```sql
mysql> alter table mail engine=InnoDB;
```

- 查看系统中所有字符集

```sql
mysql> show character set;
```

- 获取当前时间

```sql
mysql> select curdate(), curtime(), now();
+------------+-----------+---------------------+
| curdate()  | curtime() | now()               |
+------------+-----------+---------------------+
| 2018-08-05 | 21:26:35  | 2018-08-05 21:26:35 |
+------------+-----------+---------------------+
```

- 获取表结构

```sql
mysql> show columns from t_user;
+----------+------------------+------+-----+---------+----------------+
| Field    | Type             | Null | Key | Default | Extra          |
+----------+------------------+------+-----+---------+----------------+
| id       | int(11) unsigned | NO   | PRI | NULL    | auto_increment |
| name     | varchar(32)      | NO   |     |         |                |
| password | varchar(32)      | NO   |     |         |                |
| created  | int(11) unsigned | NO   |     | NULL    |                |
| updated  | int(11) unsigned | NO   |     | NULL    |                |
+----------+------------------+------+-----+---------+----------------+

mysql> show create table t_user\G; 
*************************** 1. row ***************************
       Table: t_user
Create Table: CREATE TABLE `t_user` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '用户id',
  `name` varchar(32) COLLATE utf8mb4_bin NOT NULL DEFAULT '' COMMENT '用户名',
  `password` varchar(32) COLLATE utf8mb4_bin NOT NULL DEFAULT '' COMMENT '密码',
  `created` int(11) unsigned NOT NULL COMMENT '创建时间',
  `updated` int(11) unsigned NOT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2004 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin
```

- 检查MySQL服务器支持哪些引擎

```sql
MariaDB [(none)]> show engines\G; 
*************************** 1. row ***************************
      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 2. row ***************************
      Engine: MRG_MyISAM
     Support: YES
     Comment: Collection of identical MyISAM tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 3. row ***************************
      Engine: MyISAM
     Support: YES
     Comment: Non-transactional engine with good performance and small data footprint
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 4. row ***************************
      Engine: BLACKHOLE
     Support: YES
     Comment: /dev/null storage engine (anything you write to it disappears)
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 5. row ***************************
      Engine: CSV
     Support: YES
     Comment: Stores tables as CSV files
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 6. row ***************************
      Engine: PERFORMANCE_SCHEMA
     Support: YES
     Comment: Performance Schema
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 7. row ***************************
      Engine: ARCHIVE
     Support: YES
     Comment: gzip-compresses tables for a low storage footprint
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 8. row ***************************
      Engine: FEDERATED
     Support: YES
     Comment: Allows to access tables on other MariaDB servers, supports transactions and more
Transactions: YES
          XA: NO
  Savepoints: YES
*************************** 9. row ***************************
      Engine: InnoDB
     Support: DEFAULT
     Comment: Percona-XtraDB, Supports transactions, row-level locking, and foreign keys
Transactions: YES
          XA: YES
  Savepoints: YES
*************************** 10. row ***************************
      Engine: Aria
     Support: YES
     Comment: Crash-safe tables with MyISAM heritage
Transactions: NO
          XA: NO
  Savepoints: NO
10 rows in set (0.00 sec)
```


- 版本查询

```
mysql> status;
--------------
D:\MySQL\MySQL Server 5.0\bin\mysql.exe  Ver 14.12 Distrib 5.0.89, for Win32 (ia32)

Connection id:          3
Current database:       cookbook
Current user:           root@localhost
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.0.89-community-nt MySQL Community Edition (GPL)
Protocol version:       10
Connection:             localhost via TCP/IP
Server characterset:    utf8
Db     characterset:    utf8
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 49 min 34 sec

Threads: 2  Questions: 41  Slow queries: 0  Opens: 17  Flush tables: 1  Open tables: 1  Queries per second avg: 0.014
--------------
```


```sql
-- 获取服务器版本字符串
mysql> select version();
+---------------------+
| version()           |
+---------------------+
| 5.0.89-community-nt |
+---------------------+
1 row in set (0.00 sec)


-- 当前数据库名称, 如果没有则为空
mysql> select database();
+------------+
| database() |
+------------+
| cookbook   |
+------------+
```





- 常见语句

```
:: 查看表结构
desc book;

:: 修改表book的author列的长度
alter table book modify author varchar(50); 

:: 删除price列
alter table book drop price;

:: 修改表名book 为 books
rename table book to books;

:: 修改表的字符集
alter table books character set GB2312;
```


#### 子查询

##### from型
```sql
-- from后要求是一个表，必须给子查询结果取个别名。
-- 简化每个查询内的条件。
-- 子查询返回一个表，表型子查询。

select * from (select * from tb where id > 0) as subfrom where id > 1;
```

##### where型
```sql
-- 子查询
select * from tb where money = (select max(money) from tb);

-- 列子查询
select column1 from t1 where exists (select * from t2);

-- 行子查询
select * from t1 where (id, gender) in (select id, gender from t2);
```

#### 处理重复项

- 防止在表中发生重复

使用 primary key 或 unique索引, 不同在于建立unique索引的列可以允许NULL值, 可以手动将列声明为NOT NULL。

```sql
create table person (
    last_name char(20) not null,
    first_name char(20) not null,
    address char(40),
    primary key(last_name, first_name)
);

create table student (
    last_name char(20) not null,
    first_name char(20) not null,
    address char(40),
    unique(last_name, first_name)
);

mysql> insert into person(last_name, first_name) values('x2', 'y2');
Query OK, 1 row affected (0.12 sec)

-- 重复插入产生错误
mysql> insert into person(last_name, first_name) values('x2', 'y2');
ERROR 1062 (23000): Duplicate entry 'x2-y2' for key 'PRIMARY'

-- 未发生重复则插入, 否则丢弃并且不会产生错误
mysql> insert ignore into person(last_name, first_name) values('x2', 'y2');
Query OK, 0 rows affected (0.00 sec)

-- 使用replace代替insert, 当重复发生时, 用新行取代原有行, 否则正常插入
replace into person(last_name, first_name) values('x2', 'y2');
Query OK, 1 row affected (0.07 sec)
```




#### 连接查询(join)

将多个表的字段进行连接，可以指定连接条件。

- 内连接(inner join)

```sql
-- 默认就是内连接，可省略inner。
-- 只有数据存在时才能发送连接，即连接结果不能出现空行。
-- on表示连接条件，其条件表达式与where类似，也可以省略条件（表示条件永远为真）
-- 也可用where表示连接条件。
-- 还有using, 但需字段名相同。using(字段名)

select * from table_a, table_b where table_a.id = table_b.id;
-- 等价于
select * from table_a inner join table_b on table_a.id = table_b.id;
```

- 交叉连接(cross join)

```
-- 没有条件的内连接
select * from tb1 cross join tb2;
```

- 外连接(outer join)
```
-- 如果数据不存在，也会出现在连接结果中。
```

- 左外连接 left join
```
-- 如果数据不存在，左表记录会出现，而右表为null填充
```

- 右外连接 right join
```
-- 如果数据不存在，右表记录会出现，而左表为null填充

right join是另外一种外部连接，与left join类似，
只不过将左表和右表的角色调过来。
从语义上讲，right join强制匹配过程为右表的每一条记录产生一行，
即使左表中不存在相应的行。
```


```sql
create table artist (
    a_id int unsigned not null auto_increment,
    name varchar(30) not null,
    primary key(a_id),
    unique(name)
);
insert into artist(name) values('Da vinci'),('Monet'),('Van Gogh'),('Picasso'),('Renoir');

create table painting (
    a_id int unsigned not null,
    p_id int unsigned not null auto_increment,
    title varchar(100) not null,
    state varchar(2) not null,
    price int unsigned,
    index(a_id),
    primary key(p_id)
);
insert into painting(a_id, p_id, title, state, price) values(1,1,'The Last Supper', 'IN', 34), (1,2,'The Mona Lisa', 'MI', 88), (3,3,'Starry Night', 'KY', 43), (3,4,'The Potato Eaters', 'KY', 64), (3,5,'The Rocks', 'IA', 90), (5,6,'Les Deux Soeurs', 'NE', 76);


-- 简单的连接2个表
mysql> select * from artist, painting where artist.a_id = painting.p_id;
mysql> select * from artist inner join painting on artist.a_id = painting.p_id;
+------+----------+------+------+-------------------+-------+-------+
| a_id | name     | a_id | p_id | title             | state | price |
+------+----------+------+------+-------------------+-------+-------+
|    1 | Da vinci |    1 |    1 | The Last Supper   | IN    |    34 |
|    2 | Monet    |    1 |    2 | The Mona Lisa     | MI    |    88 |
|    4 | Picasso  |    3 |    4 | The Potato Eaters | KY    |    64 |
|    5 | Renoir   |    3 |    5 | The Rocks         | IA    |    90 |
|    3 | Van Gogh |    3 |    3 | Starry Night      | KY    |    43 |
+------+----------+------+------+-------------------+-------+-------+


mysql> select * from artist inner join painting on artist.a_id = painting.p_id where painting.state = 'KY';
+------+----------+------+------+-------------------+-------+-------+
| a_id | name     | a_id | p_id | title             | state | price |
+------+----------+------+------+-------------------+-------+-------+
|    4 | Picasso  |    3 |    4 | The Potato Eaters | KY    |    64 |
|    3 | Van Gogh |    3 |    3 | Starry Night      | KY    |    43 |
+------+----------+------+------+-------------------+-------+-------+


-- 哪幅画是Van Gogh的作品
mysql> select painting.title from artist inner join painting on artist.a_id=painting.a_id where artist.name='Van Gogh';
+-------------------+
| title             |
+-------------------+
| Starry Night      |
| The Potato Eaters |
| The Rocks         |
+-------------------+


-- 在Kentucky或Indiana州买了哪些画家的画作？
mysql> select distinct artist.name from artist inner join painting on artist.a_id=painting.a_id where painting.state in ('KY', 'IN');
+----------+
| name     |
+----------+
| Da vinci |
| Van Gogh |
+----------+



-- 找到对于每个画家你各收藏了多少作品？
mysql> select artist.name, count(*) as 'number of paintings' from artist inner join painting on artist.a_id=painting.a_id group by artist.name;
+----------+---------------------+
| name     | number of paintings |
+----------+---------------------+
| Da vinci |                   2 |
| Renoir   |                   1 |
| Van Gogh |                   3 |
+----------+---------------------+


-- 你为每个画家的作品花费了多少钱，包括总和以及每幅作品平均的花费
mysql> select artist.name, count(*) as 'number of paintings', sum(painting.price) as 'total price',
    -> avg(painting.price) as 'average price' from artist inner join painting on artist.a_id=painting.a_id
    -> group by artist.name;
+----------+---------------------+-------------+---------------+
| name     | number of paintings | total price | average price |
+----------+---------------------+-------------+---------------+
| Da vinci |                   2 |         122 |       61.0000 |
| Renoir   |                   1 |          76 |       76.0000 |
| Van Gogh |                   3 |         197 |       65.6667 |
+----------+---------------------+-------------+---------------+


mysql> select * from artist inner join painting on artist.a_id=painting.a_id;
+------+----------+------+------+-------------------+-------+-------+
| a_id | name     | a_id | p_id | title             | state | price |
+------+----------+------+------+-------------------+-------+-------+
|    1 | Da vinci |    1 |    1 | The Last Supper   | IN    |    34 |
|    1 | Da vinci |    1 |    2 | The Mona Lisa     | MI    |    88 |
|    5 | Renoir   |    5 |    6 | Les Deux Soeurs   | NE    |    76 |
|    3 | Van Gogh |    3 |    3 | Starry Night      | KY    |    43 |
|    3 | Van Gogh |    3 |    4 | The Potato Eaters | KY    |    64 |
|    3 | Van Gogh |    3 |    5 | The Rocks         | IA    |    90 |
+------+----------+------+------+-------------------+-------+-------+
6 rows in set (0.00 sec)

mysql> select * from artist left join painting on artist.a_id=painting.a_id;
+------+----------+------+------+-------------------+-------+-------+
| a_id | name     | a_id | p_id | title             | state | price |
+------+----------+------+------+-------------------+-------+-------+
|    1 | Da vinci |    1 |    1 | The Last Supper   | IN    |    34 |
|    1 | Da vinci |    1 |    2 | The Mona Lisa     | MI    |    88 |
|    2 | Monet    | NULL | NULL | NULL              | NULL  |  NULL |
|    4 | Picasso  | NULL | NULL | NULL              | NULL  |  NULL |
|    5 | Renoir   |    5 |    6 | Les Deux Soeurs   | NE    |    76 |
|    3 | Van Gogh |    3 |    3 | Starry Night      | KY    |    43 |
|    3 | Van Gogh |    3 |    4 | The Potato Eaters | KY    |    64 |
|    3 | Van Gogh |    3 |    5 | The Rocks         | IA    |    90 |
+------+----------+------+------+-------------------+-------+-------+
8 rows in set (0.06 sec)


mysql> select * from artist left join painting on artist.a_id=painting.a_id where painting.a_id is null;
+------+---------+------+------+-------+-------+-------+
| a_id | name    | a_id | p_id | title | state | price |
+------+---------+------+------+-------+-------+-------+
|    2 | Monet   | NULL | NULL | NULL  | NULL  |  NULL |
|    4 | Picasso | NULL | NULL | NULL  | NULL  |  NULL |
+------+---------+------+------+-------+-------+-------+
2 rows in set (0.00 sec)


mysql> select artist.* from artist left join painting on artist.a_id=painting.a_id where painting.a_id is null;
+------+---------+
| a_id | name    |
+------+---------+
|    2 | Monet   |
|    4 | Picasso |
+------+---------+
2 rows in set (0.00 sec)


-- 每个画家画了哪些作品？
mysql> select artist.name, painting.title from artist left join painting on artist.a_id=painting.a_id order by name, tit
le;
+----------+-------------------+
| name     | title             |
+----------+-------------------+
| Da vinci | The Last Supper   |
| Da vinci | The Mona Lisa     |
| Monet    | NULL              |
| Picasso  | NULL              |
| Renoir   | Les Deux Soeurs   |
| Van Gogh | Starry Night      |
| Van Gogh | The Potato Eaters |
| Van Gogh | The Rocks         |
+----------+-------------------+
-- 结果中title列为NULL的行对应了存在于artist表中，但是你没有收藏其作品的画家。



-- 在painting表中每个画家的作品有多少？
mysql> select artist.name as painter, count(painting.a_id) as count from artist inner join painting
    -> on artist.a_id = painting.a_id group by artist.name;
+----------+-------+
| painter  | count |
+----------+-------+
| Da vinci |     2 |
| Renoir   |     1 |
| Van Gogh |     3 |
+----------+-------+

-- 每个画家的作品有多少？
mysql> select artist.name as painter, count(painting.a_id) as count from artist left join painting
    -> on artist.a_id = painting.a_id group by artist.name;
+----------+-------+
| painter  | count |
+----------+-------+
| Da vinci |     2 |
| Monet    |     0 |
| Picasso  |     0 |
| Renoir   |     1 |
| Van Gogh |     3 |
+----------+-------+
-- count(*) 会对所有的连接行计数，甚至包含NULL值的行；
-- count(painting.a_id) 仅对非空值的行计数。 
```


## 常见错误及解决方法

1. ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost' (10061)

```
> mysql -uroot -p
Enter password: ****
ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost' (10061)
```

- 解决方法:

```
> net start mysql
MySQL 服务正在启动 .
MySQL 服务已经启动成功。
```

2. ERROR 1130 -Host '192.168.1.3' is not allowed to connect to this MySQL server

- 解决方法1: 改表法。

可能是你的帐号不允许从远程登陆，只能在localhost。这个时候只要在localhost的那台电脑，登入mysql后，更改 "mysql" 数据库里的 "user" 表里的 "host" 项，从"localhost"改称"%"

```sql
> mysql -u root -pvmwaremysql
> use mysql;
mysql> update user set host = '%' where user = 'root';
mysql> select host, user from user; 
```

- 解决方法2: 授权法。

```sql
-- 允许用户myuser使用mypassword从任何主机连接到mysql服务器 
> GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION; 
-- 允许用户myuser从ip为192.168.1.3的主机连接到mysql服务器，并使用mypassword作为密码 
> GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.3' IDENTIFIED BY 'mypassword' WITH GRANT OPTION; 
```

## 其它

### 如何快速删除大量数据

对于现有数据，时间字段应该被索引，这样就会比较快了，同时不要使用datediff(MM,tabletime,getdate())>1;这种条件，该条件会导致索引失效使执行效率降低，应该直接使用time > '2016-07-01 00:00:00.000'，同时，你可以考虑分批处理。鉴于数据量很大，可以考虑按周为单位创建多个表，通过逻辑循环创建。



