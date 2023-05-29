---
title: MongoDB自动删除指定时间之前的数据
date: 2022-10-08 19:46:01
tags:
  - MongoDB
categories:
  - MongoDB
---
> 参考链接：  
https://www.jb51.net/article/150243.htm  
https://docs.mongodb.com/v3.2/core/index-ttl/

`db.gms_monitor.createIndex({"time": -1 }, { expireAfterSeconds: 90*24*60*60 })`

> 注：90天\*24小时\*60分钟\*60秒
<!-- more -->
![image.png](https://i.loli.net/2020/10/08/jlLFBeg47ho8ZSv.png)

