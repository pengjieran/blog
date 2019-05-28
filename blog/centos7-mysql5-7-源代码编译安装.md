---
title: centos7 mysql5.7 源代码编译安装
tags:
  - mysql
categories:
  - mysql
toc: false
date: 2019-05-28 16:03:57
---

先简单介绍一下编译环境吧：
    操作系统：CentOS 7.6
    使用软件：boost[boost](https://www.boost.org/)[mysql5.7](https://dev.mysql.com/downloads/mysql/5.7.html#downloads)
    参考链接：[mysql install with source code](https://dev.mysql.com/doc/refman/5.7/en/source-installation.html)
从参考链接里可以看出，mysql5.7的源代码要求已经安装好了cmake，boost要求1.59.0的版本，这些环境准备好后就可以开始编译源代码了。
# 安装实验环境
```bash
yum -y install gcc gcc-c++ ncurses ncurses-devel bison cmake make
```
# 创建运行用户，不允许登录
```bash
useradd -M -s /sbin/nologin mysql  //创建用户mysql，不创建家目录，不允许登陆系统
```

# 上传相关源代码，主要是mysql源代码包和boost
```bash
mv boost_1_59_0 boost
```
# 配置编译环境
如果在cmake过程中有报错，需要先解决错误，然后删除mysql源码根目录下的CMakeCache.txt，然后再cmake,否则会一直报同样的错误
```bash
cmake \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \                         	//指定mysql数据库安装目录
-DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \               		//连接文件位置
-DSYSCONFDIR=/etc \                                                     //指定配置文件目录
-DSYSTEMD_PID_DIR=/usr/local/mysql \                                    //进程文件目录
-DDEFAULT_CHARSET=utf8mb4  \                                            //指定默认使用的字符集编码
-DDEFAULT_COLLATION=utf8mb4_general_ci \                              	//指定默认使用的字符集校对规则
-DWITH_INNOBASE_STORAGE_ENGINE=1 \                         		//存储引擎
-DWITH_ARCHIVE_STORAGE_ENGINE=1 \                            		//存储引擎
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \                     		//存储引擎
-DWITH_PERFSCHEMA_STORAGE_ENGINE=1 \                 			//存储引擎
-DMYSQL_DATADIR=/usr/local/mysql/data \                              	//数据库文件
-DWITH_BOOST=/usr/local/boost \                                         //指定Boost库的位置，mysql5.7必须添加该参数
-DWITH_SYSTEMD=1                                                        //使系统支持MySQL数据库
```
# 编译并安装
这一步操作如果服务器配置不行的话会非常耗时，出去吃个大餐，再回来应该就编译完了
```bash
make && make install
```
# 修改数据库目录权限
```bash
chown -R mysql:mysql /usr/local/mysql/
```
# 修改配置文件
```bash
vim /etc/my.cnf
[client]
port = 3306
default-character-set=utf8mb4
socket = /usr/local/mysql/mysql.sock
[mysql]
port = 3306
default-character-set=utf8mb4
socket = /usr/local/mysql/mysql.sock
[mysqld]
user = mysql
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
port = 3306
character_set_server=utf8mb4
pid-file = /usr/local/mysql/mysqld.pid
socket = /usr/local/mysql/mysql.sock
server-id = 1
sql_mode = NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,PIPES_AS_CONCAT,ANSI_QUOTES
[mysqld_safe]

chown mysql:mysql /etc/my.cnf   #修改配置文件的权限
```
# 设置环境变量
```bash
echo 'PATH=/usr/local/mysql/bin:/usr/local/mysql/lib:$PATH' >> /etc/profile
echo 'export PATH' >> /etc/profile
source /etc/profile   //使写入生效
```
# 初始化数据库
```bash
mysqld \
--initialize-insecure \         //生成初始化密码（5.7版本才有），实际会生成空密码
--user=mysql \                  //指定管理用户
--basedir=/usr/local/mysql \    //指定工作目录
--datadir=/usr/local/mysql/data //指定数据文件目录
```
至此，mysql5.7版本的编译过程就完成了