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




## 一些有用的小技巧

- Windows中mysql，按F7，将会显示一个最近所使用的命令的菜单。

- 如果查询结果太长，不便于阅读，可以在结尾加上'\G'，垂直输出。

## 基本操作

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


```
mysql> select version();
+---------------------+
| version()           |
+---------------------+
| 5.0.89-community-nt |
+---------------------+
1 row in set (0.00 sec)
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



#### 连接查询(join)

将多个表的字段进行连接，可以指定连接条件。

- 内连接(inner join)

```
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

内连接、外连接对比:

```
select * from artist inner join painting on artist.a_id = painting.a_id;
+------+----------+------+------+-------------------+-------+-------+
| a_id | name     | a_id | p_id | title             | state | price |
+------+----------+------+------+-------------------+-------+-------+
|    1 | Da vinci |    1 |    1 | The Last Supper   | IN    |    34 |
|    1 | Da vinci |    1 |    2 | The Mona Lisa     | MI    |    88 |
|    5 | Renoir   |    5 |    6 | Les Deux Soeurs   | NE    |    76 |
|    3 | Van Gogh |    3 |    3 | Starry Night      | KY    |    43 |
+------+----------+------+------+-------------------+-------+-------+

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
+------+----------+------+------+-------------------+-------+-------+
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

