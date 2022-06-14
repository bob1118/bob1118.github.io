---
title: "Postgresql How To"
date: 2021-12-22T16:09:56+08:00
lastmod: 2021-12-22T16:09:56+08:00
draft: true
keywords: [postgresql,postgres,pgsql]
description: "postgresql how to"
tags: [postgresql]
categories: [pub]
author: "bob"

---

<!--more-->
# postgresql

Postgresql 是一个功能强大的开源关系数据库系统.  
Postgresql的官方网站是 <https://postgresql.org>.  
最新的版本是14，了解可以访问 <http://www.postgres.cn/docs/14/index.html>.

## 安装

### windows

由EDB认证的Postgresql installer.  
<https://www.enterprisedb.com/downloads/postgres-postgresql-downloads>  
下载并安装，按照提示设置postgres数据库的密码就可以了。

### linux

linux不同发行版本都可以从自己的软件库进行安装，以debian为例.  
先安装postgresql，以postgres用户local（Unix domain socket）方式连接到postgres数据库.  
修改用户postgres密码，再以postgres用户host（localhost）方式连接到数据库。

```zsh
root@debian1110:~# apt-get install postgresql
…
root@debian1110:~# su postgres
postgres@debian1110:/root$  cd
postgres@debian1110:~$ psql
psql (13.5 (Debian 13.5-0+deb11u1))
Type "help" for help.

postgres=#
postgres=# \password
Enter new password:
Enter it agin:
postgres=# \q
postgres@debian1110:~$ exit
root@debian1110:~#


root@debian1110:~# psql -hlocalhost postgres postgres
Password for user postgres: 
psql (13.5 (Debian 13.5-0+deb11u1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=# select version();
                                                          version                                                          
------------------------------------------------------------------------------------------------------------------------
PostgreSQL 13.5 (Debian 13.5-0+deb11u1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
(1 row)
postgres=# 
postgres=# \l
                                  List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
-----------+----------+----------+-------------+-------------+-----------------------
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
(3 rows)

postgres=# 
```

### mac

mac系统上可以去EDB下载，也可以用brew直接安装（brew安装后要以当前用户登入数据库）。

```zsh
brew install postgresql
➜  ~ psql postgres bob
psql (14.1)
Type "help" for help.

postgres=# 
postgres=# select version();
                                                      version                                                      
-------------------------------------------------------------------------------------------------------------------
 PostgreSQL 14.1 on aarch64-apple-darwin21.1.0, compiled by Apple clang version 13.0.0 (clang-1300.0.29.3), 64-bit
(1 row)

postgres=# \l
                         List of databases
   Name    | Owner | Encoding | Collate | Ctype | Access privileges 
-----------+-------+----------+---------+-------+-------------------
 postgres  | bob   | UTF8     | C       | C     | 
 template0 | bob   | UTF8     | C       | C     | =c/bob           +
           |       |          |         |       | bob=CTc/bob
 template1 | bob   | UTF8     | C       | C     | =c/bob           +
           |       |          |         |       | bob=CTc/bob
(3 rows)

postgres=# 
```

## 客户端

### psql

命令行客户端工具psql。

```zsh
psql --help
Usage:
  psql [OPTION]... [DBNAME [USERNAME]]
  ...
Connection options:
  -h, --host=HOSTNAME      database server host or socket directory (default: "local socket")
  -p, --port=PORT          database server port (default: "5432")
  -U, --username=USERNAME  database user name (default: "bob")
  -w, --no-password        never prompt for password
  -W, --password           force password prompt (should happen automatically)
```

### DBeaver

开源图形客户端工具。

```zsh
brew search dbeaver
....
brew install dbeaver-community
```
