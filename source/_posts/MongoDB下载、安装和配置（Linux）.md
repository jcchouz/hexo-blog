---
title: MongoDB下载、安装和配置（Linux）
date: 2022-01-19 12:51:56
tags:
  - MongoDB
  - Linux
categories:
  - MongoDB
---
#### 参考网址

> http://www.runoob.com/mongodb/mongodb-linux-install.html
---

#### 新建Java容器
`docker run -itd -p 27017:27017 -p 28017:28017 ubuntu:java`

#### 创建mongodb用户，并设置密码

`useradd -m mongodb`

`passwd mongodb123`

密码：`mongodb123`

#### 把mongodb用户加入sudoers
`su`

`vi /etc/sudoers`

找到这一 行：`root ALL=(ALL) ALL`，在下面添加`mongodb ALL=(ALL) ALL`

保存退出

#### 下载MongoDB
进入容器：`docker exec -it 4f8e /bin/bash`

`cd /opt/`

`mkdir installation-packages`

`cd installation-packages/`

下载（官网：'https://www.mongodb.com/download-center/community'，选择Version:3.2.22 (previous release) / OS:Ubuntu 14.04 Linux 64-bit x64 / Package:TGZ）：`wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu1404-3.2.22.tgz`

解压：`tar -zxvf mongodb-linux-x86_64-ubuntu1404-3.2.22.tgz`

`cd /usr/local/`

`mkdir mongodb`

`cd /opt/installation-packages/`

`mv mongodb-linux-x86_64-ubuntu1404-3.2.22/ /usr/local/mongodb/`

`cd /usr/local/mongodb`

`mv mongodb-linux-x86_64-ubuntu1404-3.2.22/* ./`

`rm -rf mongodb-linux-x86_64-ubuntu1404-3.2.22/`

#### 配置环境变量
`su mongodb`

`vi ~/.bashrc`

在最后加入`export PATH=/usr/local/mongodb/bin:$PATH`

`source ~/.bashrc`

> 验证方法`echo $PATH`，看有没有`/usr/local/mongodb/bin`

#### 创建数据库目录
`mkdir -p /data/db`

把`/data/db`目录的权限分配给mongodb用户:`sudo chown -R mongodb /data/db`

#### 开启MongoDB服务
`cd /usr/local/mongodb/bin`

`./mongod --rest`

显示"waiting for connections on port 27017"表示数据库已经启动，需要新开一个窗口再使用，如下图所示
![](https://i.loli.net/2019/01/19/5c42ff96bd67f.jpg)

#### 进入Shell
新开一个窗口

`su mongodb`

输入`mongo`
![](https://i.loli.net/2019/01/19/5c42ffcee076a.jpg)

访问自带的HTTP用户界面：'http://202.204.62.229:28017'

创建root用户

在MongoDB的命令行中：
> 参考链接：https://www.jianshu.com/p/33e3940db2b2
1. `use admin`
2. `db.createUser({user:"root", pwd: "admin", roles: ["root"]})`
3. `use admin`
4. `db.createUser({user:"admin", pwd:"admin", roles: [{role:"userAdminAnyDatabase", db:"admin"}]})`
5. `use nfca_db` //该命令也是创建数据库的命令
6. `db.createUser({user:"nfca", pwd:"nfca", roles:["readWrite", "dbAdmin"]})`
7. `db.auth('nfca', 'nfca')`
8. `use nfca_db`
9. `db.createCollection("runoob")`
10. `ctrl+c`退出MongoDB
11. `vi /usr/local/mongodb/mongodb.conf`（如果没有，会自动新建）

    写入 `auth=true`
    
    `:wq`保存
12. 开启MongoDB，`sudo /usr/local/mongodb/bin/mongod --config /usr/local/mongodb/mongodb.conf --rest`


#### 保存镜像
`docker commit -m "mongodb installed" 4f8e ubuntu:mongodb`

#### 关闭mongodb

`mongod  --shutdown`

#### 容器更新后， 保存镜像
`docker commit -m "update" 6e2b ubuntu:mongodb`

#### 新建容器时，自动开启mongodb
想在Docker容器启动时执行脚本命令，本来想在`/etc/rc.local`中加命令，但发现无效，原来docker容器启动时并不执行`/etc/rc.local`，这个太内核了，因此就需要找别的办法。如下：

> 首先注意:
> 只能在run的时候设置启动时执行脚本，之后start和restart也会自动执行  
> 但是无法在start或者restart时再设置，只能在run的时候设置

##### 准备脚本
首先run一个容器，在容器里面某个路径新建sh脚本，比如在`/home/mongodb/`中新建`start_mongodb.sh`脚本

脚本内容
```
sudo /usr/local/mongodb/bin/mongod --config /usr/local/mongodb/mongodb.conf --rest
```

##### 生成容器为镜像
`docker commit -m 'auto start mongodb' 6e2b ubuntu:mongodb`

##### 生成能自动执行脚本的容器
`docker run -itd -p 27017:27017 -p 28017:28017 ubuntu:mongodb /bin/bash /home/mongodb/start_mongodb.sh`

和一般生成容器相比，多了：`/bin/bash /home/mongodb/start_mongodb.sh`