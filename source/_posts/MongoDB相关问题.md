---
title: MongoDB相关问题
date: 2020-01-20 10:38:01
tags:
  - MongoDB
categories:
  - MongoDB
---
# 问题一

#### 问题描述

MongoDB中的几种权限

#### 解决方案

角色安全权限访问控制：

Read：允许用户读取指定数据库

readWrite：允许用户读写指定数据库

<!-- more -->

dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile

userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户

clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。

readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限

readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限

userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限

dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。

root：只在admin数据库中可用。超级账号，超级权限

#### 参考网址
> https://blog.csdn.net/zmx729618/article/details/52091804
> https://www.2cto.com/database/201507/420854.html

# 问题二

#### 问题描述
出现'Unclean full-time diagnostic data capture shutdown detected, found interim file, some metrics may have been lost.'

#### 解决方案
`rm -f /var/lib/mongo/diagnostic.data/*`

# 问题三

#### 问题描述

错误提示：

'listen(): bind() failed errno:98 Address already in use for socket: 0.0.0.0:27017'
'addr already in use'

#### 解决方案
`ps aux | grep mongod`

找到对应进程id,杀死:

`kill -9 pid`

如下图：
![](https://i.loli.net/2019/01/20/5c447f42b9786.jpg)

#### 参考网址
> https://www.cnblogs.com/wjw-blog/p/5995237.html

# 问题四

#### 问题描述

后端对数据排序、分页时报错：

`pymongo.errors.OperationFailure: Executor error during find command :: caused by :: Sort operation used more than the maximum 33554432 bytes of RAM. Add an index, or specify a smaller limit.`

按照错误提示，知道这是排序的时候报的错，因为 mongo 的 sort 操作是在内存中操作的，必然会占据内存，同时mongo 内的一个机制限制排序时最大内存为 32M，当排序的数据量超过 32M,就会报上面的这个错，解决办法就像上面提示的意思：

一是加大 mongo的排序内存，这个一般是运维来管，也有弊端，就是数据量如果再大，还要往上加。

另一个办法就是加索引，这个方法还是挺方便的。创建索引及时生效，不需要重启服务。

#### 解决方案
创建索引：

1. `db.你的collection.createIndex({"你的字段": -1})`，此处 -1 代表倒序，1 代表正序
2. `db.你的collecton.getIndexes()`

实际操作：

1. 按时间索引，且倒序，最新的在最前面：`db.gms_monitor.createIndex({time:-1})`
2. 查询gms_monitor表的索引：`db.gms_monitor.getIndexes()`

#### 参考网址
> https://blog.csdn.net/kdc18333608478/article/details/74635838
> https://blog.csdn.net/zzq900503/article/details/78666865

# 定时删除数据

`db.gms_monitor.createIndex({"time":1}, {expireAfterSeconds: 7776000})`

gms_monitor:collection名
time:字段名
7776000:有效时间（秒）