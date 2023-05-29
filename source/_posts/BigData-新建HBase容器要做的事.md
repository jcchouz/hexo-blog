---
title: BigData-新建HBase容器要做的事
date: 2018-11-30 10:47:06
tags:
  - BigData（大数据）
  - HBase
categories:
  - [BigData（大数据）, HBase]
---
#### 制作镜像
1. 在容器里
    + 切换到root用户`su`，输入密码
    + `service opentsdb stop`
    + 切换到hadoop用户`su hadoop`
    + `stop-hbase.sh`
    + `stop-all.sh`
2. 退出容器，但不关闭`ctrl+P+Q`
3. 制作镜像`docker commit -m "thrift installed" 5ac0 ubuntu:thrift`

<!-- more -->

#### 停止原来的容器，释放端口占用
`docker stop 5ac0`

#### 新建容器
`docker run -itd -p 2181:2181 -p 4242:4242 -p 8088:8088 -p 9000:9000 -p 9090:9090 -p 16000:16000 -p 16010:16010 -p 16201:16201 -p 16301:16301 -p 50020:50020 -p 50070:50070 ubuntu:thrift`

#### 进入容器
`docker exec -it $container_id /bin/bash`

#### 配置/etc/hosts文件，加入hadoop1
**root**身份下执行

查看ip地址`ifconfig`

`vi /etc/hosts`

加入：
```
#Hadoop Name Node
$container_ip  hadoop1
```

#### 开启ssh
**root**身份下执行

`service ssh start`

#### 测试ssh
1. 切换到hadoop用户`su hadoop`
2. `ssh hadoop1`

#### 删除HDFS使用的Linux目录
**hadoop**身份下执行

~~rm -rf /home/hadoop/dfs/data/*~~

~~rm -rf /home/hadoop/dfs/name/*~~

~~rm -rf /home/hadoop/dfs/temp/*~~

#### 格式化文件系统
`hdfs namenode -format`

#### 开启Hadoop、HBase
**hadoop**身份下执行

1. `start-all.sh`
1. `start-hbase.sh`

#### 开启thrift
`/opt/hbase-1.4.8/bin/hbase-daemon.sh start thrift`

~~**#### 开启OpenTSDB**~~

~~1. 切换到root用户`su`，输入密码~~

~~2. `service opentsdb start`~~