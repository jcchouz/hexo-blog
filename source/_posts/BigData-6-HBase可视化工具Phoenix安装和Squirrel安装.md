---
title: BigData-6-HBase可视化工具Phoenix安装和Squirrel安装
date: 2018-12-03 11:58:03
tags:
  - BigData（大数据）
  - HBase
  - Phoenix
  - Squirrel
categories:
  - [BigData（大数据）, HBase]
---
#### 参考网址
> https://www.cnblogs.com/raphael5200/p/5260198.html

---

#### 在服务器中下载Phoenix
Phoenix下载地址：http://mirror.bit.edu.cn/apache/phoenix/

在145服务器中切换到root用户`su root`

输入密码

<!-- more -->

`cd /home1/jupyter_data/ZJC/`

`wget http://archive.apache.org/dist/phoenix/apache-phoenix-4.14.1-HBase-1.4/bin/apache-phoenix-4.14.1-HBase-1.4-bin.tar.gz`

#### 拷贝一份到容器中
`docker cp /home1/jupyter_data/ZJC/apache-phoenix-4.14.1-HBase-1.4-bin.tar.gz 62f1:/opt/`

#### 在容器中解压
`docker exec -it 62f1 /bin/bash`

`cd /opt/`

`tar -zxvf apache-phoenix-4.14.1-HBase-1.4-bin.tar.gz`

#### 在容器中配置Phoenix
1. 将apache-phoenix-4.14.1-HBase-1.4-bin.tar.gz目录下phoenix-core-4.14.1-HBase-1.4.jar、phoenix-4.14.1-HBase-1.4-server.jar拷贝到hbase的lib目录下：

    `cp ./phoenix-core-4.14.1-HBase-1.4.jar /opt/hbase-1.4.8/lib/`
    
    `cp ./phoenix-4.14.1-HBase-1.4-server.jar /opt/hbase-1.4.8/lib/`

2. 将hbase的配置文件hbase-site.xml、 /opc/hadoop-2.7.7/etc/hadoop下的core-site.xml 、hdfs-site.xml放到phoenix-4.5.2-HBase-1.1-bin/bin/下，替换Phoenix原来的配置文件：

    `cp /opt/hbase-1.4.8/conf/hbase-site.xml /opt/apache-phoenix-4.14.1-HBase-1.4-bin/bin/hbase-site.xml`
    
    `cp /opt/hadoop-2.7.7/etc/hadoop/core-site.xml /opt/apache-phoenix-4.14.1-HBase-1.4-bin/bin/`
    
    `cp /opt/hadoop-2.7.7/etc/hadoop/hdfs-site.xml /opt/apache-phoenix-4.14.1-HBase-1.4-bin/bin/`

3. 验证是否成功

    `cd /opt/apache-phoenix-4.14.1-HBase-1.4-bin/bin`
    
    `./sqlline.py 62f169f8d9d1:2181`
    
    成功截图：
    ![](https://i.loli.net/2018/12/01/5c023b4d74784.jpg)
4. 输入`!tables`，查看都有哪些表
    如图：  
    ![](https://i.loli.net/2018/12/01/5c023c41ccda9.jpg)

5. 输入`!exit`退出

#### 在145服务器中解压
`tar -zxvf /home1/jupyter_data/ZJC/apache-phoenix-4.14.1-HBase-1.4-bin.tar.gz`

#### 在Windows中配置Hosts
1. 用记事本打开`C:\Windows\System32\drivers\etc\hosts`
2. 确保`127.0.0.1       localhost`被注释
3. 在最后加入`202.204.62.145 62f169f8d9d1`
4. 保存

#### 在Windows中下载SQuirreL SQL Client
**注意在Windows中操作**

SQuirreL SQL Client下载地址：http://www.squirrelsql.org/

下载完成后，将`squirrel-sql-3.9.0-standard.jar`文件移动到`E:\Software\SQuirreL`

#### 在Windows中安装SQuirreL SQL Client
**注意在Windows中操作**

**注意jdk版本要求至少1.8！**

1. 进入cmd
2. `e:`
3. `cd Software`
4. `cd SQuirreL`
5. `java -jar squirrel-sql-3.9.0-standard.jar`
![](https://i.loli.net/2018/12/01/5c02146a592be.jpg)
6. 弹出安装界面，一路Next，记住安装路径为`C:\Program Files\squirrel-sql-3.9.0`

#### 从服务器中拷贝jar包到Windows
使用WinSCP

将服务器中解压到的`/home1/jupyter_data/ZJC/apache-phoenix-4.14.1-HBase-1.4-bin`目录下的如下两个jar包：
1. `phoenix-4.14.1-HBase-1.4-client.jar`
2. `phoenix-core-4.14.1-HBase-1.4.jar`

拷贝到squirrel安装目录的lib下`C:\Program Files\squirrel-sql-3.9.0\lib`

#### 启动SQuirreL SQL Client
在安装目录`C:\Program Files\squirrel-sql-3.9.0`下双击`squirrel-sql.bat`

#### 配置SQuirreL SQL Client
1. 点击左侧`Drivers`  
    ![](https://i.loli.net/2018/12/01/5c02251602391.jpg)
2. 点击`+`  
    ![](https://i.loli.net/2018/12/01/5c0229e26abd3.jpg)
3. 按图配置  
    Name:`phoenix`

    Example URL:`jdbc:phoenix:202.204.62.145:2181`
    
    Class Name:`org.apache.phoenix.jdbc.PhoenixDriver`
    
    ![](https://i.loli.net/2018/12/01/5c022b953cee2.jpg)
    
#### 连接Phoenix
1. 点击左侧`Aiiasses`  
    ![](https://i.loli.net/2018/12/01/5c022c19acf73.jpg)
2. 点击`+`  
    ![](https://i.loli.net/2018/12/01/5c022c2e63837.jpg)
3. 按图配置  
    ![](https://i.loli.net/2018/12/01/5c02371f93c80.jpg)
    用户名hadoop
    密码hadoop123
4. 点击Test进行测试连接
出现connection successful，测试成功
![](https://i.loli.net/2018/12/01/5c0237694a8d5.jpg)
5. 点击OK连接
