---
title: Python-pip换源，永久
date: 2019-07-02 10:06:59
tags:
  - Python
  - pip
categories:
  - [Python]
  - [pip]
---
# 更换pip源到国内镜像

## pip国内的一些镜像

+ 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
+ 豆瓣(douban) http://pypi.douban.com/simple/
+ 阿里云 https://mirrors.aliyun.com/pypi/simple/
+ 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
+ 中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/

## 修改源方法

### 临时使用
可以在使用pip的时候在后面加上-i参数，指定pip源
eg:	pip install scrapy -i https://pypi.tuna.tsinghua.edu.cn/simple

### 永久修改

#### linux:
修改 ~/.pip/pip.conf (没有就创建一个)， 内容如下：

[global]

index-url = https://pypi.tuna.tsinghua.edu.cn/simple

#### windows:
直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下

[global]

index-url = https://pypi.tuna.tsinghua.edu.cn/simple

--------------------- 
作者：linux凯 
来源：CSDN 
原文：https://blog.csdn.net/chenghuikai/article/details/55258957 
版权声明：本文为博主原创文章，转载请附上博文链接！