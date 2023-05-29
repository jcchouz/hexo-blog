---
title: BigData-5-OpenTSDB下载、安装、配置
date: 2018-12-02 12:21:29
tags:
  - BigData（大数据）
  - OpenTSDB
categories:
  - [BigData（大数据）, OpenTSDB]
---
#### 参考网址：
> https://blog.csdn.net/olinbsoft/article/details/79416813（最主要的）
> https://www.jianshu.com/p/e151bdb70172
> https://blog.csdn.net/zhangzhensea/article/details/77890504

---
#### 下载OpenTSDB

从 github 上下载 OpenTSDB 的 release 版本的deb包，下载地址：https://github.com/OpenTSDB/opentsdb/releases

**还是以root用户执行**

1. `cd /opt/`
1. `wget https://github.com/OpenTSDB/opentsdb/releases/download/v2.4.0RC2/opentsdb-2.4.0RC2_all.deb`

<!-- more -->

1. 备份一份到宿主机
    1. 退出容器但不关闭，`ctrl+P+Q`
    1. 切换到root用户`su`，输入密码
    1. `docker cp 62f1:/opt/opentsdb-2.4.0RC2_all.deb /home1/jupyter_data/ZJC/`

#### 安装OpenTSDB
1. 进入容器`docker exec -it 62f1 /bin/bash`
1. 切换到root用户`su`，输入密码
1. `cd /opt/`
1. 安装
    + `dpkg -i opentsdb-2.4.0RC2_all.deb`
    + `apt-get install -f`

#### 配置OpenTSDB
编辑`/opt/opentsdb-2.3.1/src/opentsdb.conf`文件

1. `vi /etc/opentsdb/opentsdb.conf`
1. 配置  
    + `tsd.storage.hbase.zk_quorum = localhost`

#### 建表
第一次用HBase实例运行OpenTSDB，需要创建必要的HBase表

1. 切换到hadoop用户`su hadoop`
1. `cd /usr/share/opentsdb/tools`
1. `env COMPRESSION=NONE HBASE_HOME=/opt/hbase-1.4.8 JAVA_HOME=/usr/lib/jvm/java-8-oracle/  /usr/share/opentsdb/tools/create_table.sh`

> COMPRESSION参数指定压缩方式，可选值是 NONE，LZO，GZIP，或者 SNAPPY 。这个命令将在指定的HBase中创建四张表：tsdb, tsdb-uid, tsdb-tree 和 tsdb-meta。如果你只是评估OpenTSDB，现在就不用关心压缩方式。在生产环境中，你要使用一个最合适的有效压缩库。

#### 启动
1. 切换到root用户`su`，输入密码
2. `service opentsdb start`

> 注：停止命令`service opentsdb stop`

浏览器访问：http://202.204.62.145:4242/

截图：  
![](https://i.loli.net/2018/12/02/5c0394f40ec57.jpg)

#### 安装Gnuplot
**还是以root用户执行**

1. `apt-get install gnuplot`
1. `apt-get install gnuplot-x11`

#### 制作镜像

1. 在容器里
    + 切换到root用户`su`，输入密码
    + `service opentsdb stop`
    + 切换到hadoop用户`su hadoop`
    + `stop-hbase.sh`
    + `stop-all.sh`
2. 退出容器，但不关闭`ctrl+P+Q`
3. 制作镜像`docker commit -m "opentsdb installed" 62f1 ubuntu:opentsdb`