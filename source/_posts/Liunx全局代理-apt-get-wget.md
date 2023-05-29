---
title: Liunx全局代理(apt-get|wget)
date: 2022-10-11 15:26:32
tags:
  - Linux
  - Proxy
categories:
  - Linux
---
# apt-get
1. `vi /etc/apt/apt.conf.d/98proxy`
2. `Acquire::http::proxy "socks5h://202.204.54.180:1080/";`
3. `save`
<!-- more -->
# wget/crul/...
1. `sudo vi ~/.bashrc`
2. 最后一行输入`export ALL_PROXY=socks5://202.204.54.3:8282`