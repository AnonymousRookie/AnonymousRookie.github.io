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

