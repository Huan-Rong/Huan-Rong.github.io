---
layout:     post
title:      "MySQL 基础教程"
subtitle:   "官方文档第三章的总结"
date:       2018-05-07
author:     "ZHR"
header-img: "img/mysql.png"
tags:
    - MySQL
---

> 本文参考 [MySQL 官方文档第三章](https://dev.mysql.com/doc/refman/5.7/en/tutorial.html)。

## mysql client program

本文通过使用 **mysql client program** 创建和使用一个简单的数据库来对 MySQL 进行介绍。*mysql client* 是一个交互性的程序，使用它可以连接到 **MySQL Server**，然后执行查询和查看查询结果。我们也可以以 **batch mode** 的非交互方式来使用它：事先在一个文件里写好查询语句，然后使用 *mysql client* 执行该文件的内容。

### 安装

当安装了 MySQL 之后，*mysql client* 就存在于根目录下的 **bin/**。

### 帮助

* 通过 `man mysql` 或者 `mysql --help` 命令可以查看有关 *mysql client* 的在线帮助。
* 查看 [官方文档对 mysql client 的介绍](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)。

## 连接和断开 MySQL Server

通常，使用 *mysql client* 连接到 *MySQL Server* 需要提供的信息有：主机、用户名和密码。如果 *mysql client* 和 *MySQL Server* 在同一台机器上，则可以不用提供主机信息。

```shell
shell> mysql -h host -u user -p
shell> mysql -u user -p
```

### 连接失败

* 使用以上命令尝试连接到 *MySQL Server* 却得到 `ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)` 的错误信息时，这表示 *MySQL Server* 进程并未运行。

* 对于其他常见的连接问题，参见 [文档](https://dev.mysql.com/doc/refman/5.7/en/common-errors.html)。

### 匿名用户登陆

本地运行的 *MySQL Server* 可以设置允许匿名用户进行连接。在这种情况下，使用 *mysql client* 连接时可以不需要提供任何信息，语法形式如下。

```shell
shell> mysql
```

### 断开连接

* 当成功连接到 *MySQL Server* 之后，随时可以使用 `quit` 命令断开连接。
* 在 Unix 系统上，也可以使用 `Control+D` 快捷键来断开连接。

## 输入查询语句

上一节提供方法可以让我们连接到 *MySQL Server*，尽管这种方法并没有指定使用哪个数据库，但现在更为重要的是知道如何创建表、插入数据以及查询数据。

### SQL 常识

* *mysql client* 将用户输入的语句发送给 *MySQL Server* 执行并显示执行结果。

* 一条 SQL 语句必须以分号结束，但 *mysql client* 的内置命令则可以省略分号。这类内置命令有 `quit`，更多的内置命令可以通过 `man mysql` 查看，也可以在登陆 *MySQL Server*  之后输入 `help` 来查看。
* *mysql client* 会显示执行语句所耗费的时间，这会让我们对语句的性能有粗略的了解。但这个时间是不精确的，因为它代表的是壁钟时间 **wall clock time**，它会受到网络延迟等因素的影响。
* SQL 关键字是大小写不敏感的。

### mysql client prompt

*mysql client* 是一个交互性的程序，在使用 *mysql client* 的时候明白它需要我们做什么事情是很重要的。**mysql client prompt** 会让我们更加深入地理解 *mysql client*。常见的 *mysql client prompt* 如下表所示。

| Prompt | Meaning                                                      |
| ------ | ------------------------------------------------------------ |
| mysql> | Ready for new query                                          |
| ->     | Waiting for next line of multiple-line query                 |
| '>     | Waiting for next line, waiting for completion of a string that began with a single quote |
| ">     | Waiting for next line, waiting for completion of a string that began with a double quote |
| `>     | Waiting for next line, waiting for completion of an identifier that began with a backtick |
| /*>    | Waiting for next line, waiting for completion of a comment that began with /* |

当一条 SQL 语句太长时，*mysql client* 允许我们将输入一条多行组成的 SQL 语句。能够这么做的，是因为 *mysql client* 是通过分号来判断语句结束与否的，而不是通过换行符来决定的。

对于 mutliple-line query 而言，如果想在中途取消执行该 query，那么直接输入 `\c` 即可。

## 创建和使用数据库

### show databases

`SHOW DATABASES` 用于查看 *MySQL Server* 当前存在哪些数据库。

![show databases](/img/in-post/show-databases.jpg)

其中，**mysql database** 描述了用户访问权限。

### create database

`CREATE DATABASE menagerie` 创建了一个名为 *menagerie* 的数据库。

![create database](/img/in-post/create-database.jpg)

需要注意的是：

* 在 Unix 中，数据库名和表名是大小写敏感的，这一点跟 SQL 关键字不同。
* 创建一个数据库并不意味着同时使用了这个刚创建的数据库；如果需要使用，则需额外使用 `use` 命令明确地指定要使用的数据库。

### 登陆时指定数据库

除了在连接 *MySQL Server* 之后使用 `use` 命令来指定要使用的数据库之外，也可以在连接时就指定数据库。

```shell
shell> mysql -h host -u root -p menagerie
```

需要注意的是，`menagerie` 并不是密码，而是指定的数据库名。如果要在 `-p` 选项之后显式地提供密码，正确的形式如下，密码和 `-p` 选项之间没有任何空白：

```shell
shell> mysql -h host -u root -ppassword menagerie
```

### 查看正在使用的数据库

`select database();` 用于查看正在使用的数据库。

## 创建表

### show tables

`show tables` 用于查看正在使用的数据库中已有的表。

![show-tables](/img/in-post/show-tables.jpg)

### create table

```sql
mysql> CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20), 
    -> species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
```

`varchar` 类型的字段必须指定长度，否则建表语句执行将会报错；长度的取值范围是 [1, 65535]。

![create-table](/img/in-post/create-table.jpg)

### describe table

`describe` 可以用于查看表结构。我们可以随时使用这条语句，或是创建表之后核对表结构的时候，或是忘记表字段名的时候。

![describe-table](/img/in-post/describe-table.jpg)

## Loading Data into a Table

对于一个刚创建的表，目前有两种方式向该表插入数据。一种是使用 `load data`，另一种是使用 `insert`。

### load data

`load data` 是一种批量插入记录的方法，事先将数据按照某种格式保存在一个文件中（譬如 `pet.txt`），然后使用 `load data`将文件的数据插入到表中。下图是 `pet.txt `文件的内容，其中 `\N` 表示字段值为 `NULL`，值的顺序应当与 `CREATE TABLE` 时的顺序一致。

![cat pet.txt](/img/in-post/cat-pet.jpg)

`load data` 可以指定 `pet.txt` 文件中的数据格式，比如换行使用 `\r\n` 、`\r`  或 `\n`，值之间的分隔符使用 `,`。

```sql
mysql> LOAD DATA LOCAL INFILE '/path/pet.txt' INTO TABLE pet
    -> FIELDS TERMINATED BY ',';
```



![load data local infile](/img/in-post/load-data-local-infile.jpg)

### insert

```sql
mysql> INSERT INTO pet VALUES('Pullball', 'Diane', 'Hamster', 'f', '1999-03-30', NULL);
```

值的顺序应当与 `CREATE TABLE` 时的顺序一致。

## 其他

* 在表中记录的是出生日期而不是年龄的原因是，年龄是随着时间变化的，如果将年龄记录在表中，则必须经常更新表。因此，更好的做法是在库里记录一个固定的值。



















