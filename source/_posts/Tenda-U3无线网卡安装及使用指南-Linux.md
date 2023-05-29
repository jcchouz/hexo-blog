---
title: Tenda-U3无线网卡安装及使用指南-Linux
date: 2020-01-17 14:27:13
tags:
  - Tenda
  - 无线网卡
categories:
  - [Tenda]
  - [无线网卡]
---
> 参考链接：https://www.tenda.com.cn/faq/9209.html

# 下载驱动
> 驱动下载网址：`tenda.com.cn/default.html`  
> 智能家用产品-无线网卡-U3-下载-U3网卡驱动（Linux）-立即下载

得到驱动安装包`Linux_(kernel 2.6.24-4.17).zip`

# 移动驱动到Liunx主机上
用WinScp或其他方式，只要复制到Linux主机上就行

<!-- more -->

本文档示例复制位置：`/home/afish/tenda/`(在自己那台134服务器上)

# 插入U3无线网卡
将U3无线网卡插在电脑可用的USB接口上

# 解压驱动安装包
- `cd /home/afish/tenda/`
- `unzip Linux_(kernel 2.6.24-4.17).zip`

# 安装驱动
- `cd RTL8192EU_linux_v5.6.4_35685_COEX20171113-0047.20191108/`
- `su`(切换到root用户)
- `输入root用户的密码`
- `sh install.sh`

等待安装完成

# 测试WiFi连接
连接WiFi试一试，看能否连接成功

# 注意事项
注意：电脑重启后需要**重新插拔**u3无线网卡，不然会一直显示在连接网络，也就是一直转圈圈，但不会连接成功