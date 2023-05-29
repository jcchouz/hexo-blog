---
title: BigData-4-HBase-Thrift下载、安装、配置（用于Python操作HBase）
date: 2018-12-27 13:56:21
tags:
  - BigData（大数据）
  - HBase
  - Thrift
categories:
  - [BigData（大数据）, HBase]
  - [Thrift]
---
#### 参考网址：
> https://www.cnblogs.com/junle/p/7611540.html

---

#### 下载安装Thrift
> 其他语言调用HBase时，需要通过Thrift进行连接

1. 安装Thrift依赖：
    + 切换到root用户：`su`
    + 更新apt-get：`apt-get update`

<!--more-->

    + 安装Thrift依赖：`apt-get install automake bison flex g++ git libboost1.55 libevent-dev libssl-dev libtool make pkg-config`
2. 下载Thrift，解压后进行编译安装：
    + 切换到root用户：`su`
    + `cd /opt/`
    + `wget http://mirrors.tuna.tsinghua.edu.cn/apache/thrift/0.11.0/thrift-0.11.0.tar.gz`
    + 退出容器，但不关闭`ctrl+P+Q`
    + 改为root用户`su`，输入密码
    + 拷贝安装包`docker cp af86:/opt/thrift-0.11.0.tar.gz /home1/jupyter_data/ZhouJiacheng/`
    + 进入容器`docker exec -it c86a /bin/bash`
    + `tar zxf thrift-0.11.0.tar.gz`
    + `cd thrift-0.11.0`
    + `./configure --with-cpp --with-boost --with-python --without-csharp --with-java --without-erlang --without-perl --with-php --without-php_extension --without-ruby --without-haskell  --without-go`
    + `make`# 这一步是编译，耗时较长
    + `make install`

#### 启动HBase的Thrift服务
`/opt/hbase-1.4.8/bin/hbase-daemon.sh start thrift`

#### 增加端口映射
1. 制作镜像
2. 新建容器  
`docker run -itd -p 2181:2181 -p 4242:4242 -p 8088:8088 -p 9000:9000 -p 9090:9090 -p 16000:16000 -p 16010:16010 -p 16201:16201 -p 16301:16301 -p 50020:50020 -p 50070:50070 ubuntu:hbase`

#### 在Python环境中安装Thrift
1. 用Virtualenv创建虚拟环境，命名`hbase_env`
    + 创建虚拟环境：`virtualenv -p C:\Python27\python2.exe E:\PythonProjects\Envs\hbase_env`
    + 激活：`virtualenv E:\PythonProjects\Envs\hbase_env\Scripts\activate`
    + 执行命令（把pip改成pip2,因为电脑中有两个版本的Python，所以需要这一步）：`python2 -m pip install --upgrade pip --force-reinstall`
    + 之后安装包可以使用`pip`或`pip2`
1. `pip install thrift`
1. `pip install hbase-thrift`