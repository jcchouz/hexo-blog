---
title: MySQL的数据从Windows迁移到Linux上
date: 2020-11-09 14:56:23
tags:
  - Windows
  - MySQL
  - Linux
  - 数据迁移
categories:
  - [Windows]
  - [MySQL]
  - [Linux]
---
1.导出数据库
  mysqldump -uroot -p 要导出的数据库名子  >  文件名  
2.将生成的数据库文件传到linux的/opt文件下  
3.登陆Linux的数据库  
  mysql -uroot -p  
<!-- more -->
4.创建与windows同名的数据库  
5.进入到数据库  
  use 数据库  
6.迁移数据  
  source /opt/filename  