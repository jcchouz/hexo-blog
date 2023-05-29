---
title: 磁盘删除OEM分区
date: 2022-01-06 13:54:19
tags:
  - Windows
  - Windows好奇兴趣
categories:
  - Windows
  - Windows好奇兴趣
---
1. win + r 输入 diskpart
1. list vol 回车
1. 记下 OEM 分区的卷号
1. sel vol 卷号 回车
1. del vol override
1. 回车