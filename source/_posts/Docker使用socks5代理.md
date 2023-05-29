---
title: Docker使用socks5代理
date: 2021-10-11 16:26:55
tags:
  - Docker
  - Proxy
categories:
  - Docker
---
1. 创建docker服务插件目录
`sudo mkdir -p /etc/systemd/system/docker.service.d`
2. 创建一个名为http-proxy.conf的文件
`sudo touch /etc/systemd/system/docker.service.d/http-proxy.conf` 
3. 编辑http-proxy.conf的文件
`sudo vim /etc/systemd/system/docker.service.d/http-proxy.conf`
4. 写入内容(将代理ip和代理端口修改成你自己的)
```
[Service]
Environment="HTTP_PROXY=socks5://代理ip:代理端口/"
```
5. 重新加载服务程序的配置文件
`sudo systemctl daemon-reload`
6. 重启docker
`sudo systemctl restart docker`
7. 验证是否配置成功
`systemctl show --property=Environment docker`  
输出`"Environment=HTTP_PROXY=socks5://代理ip:代理端口/"`表示成功