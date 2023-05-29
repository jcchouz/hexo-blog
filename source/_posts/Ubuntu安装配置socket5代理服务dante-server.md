---
title: Ubuntu安装配置socket5代理服务dante-server
date: 2020-01-12 14:35:10
tags:
  - Linux
  - 网络代理
  - socks5
categories:
  - [Linux]
  - [网络代理, socks5]
---
> 服务器：宿舍的虚拟机10.13.157.19

# 安装
安装比较容易，直接执行：

`sudo apt-get install dante-server`

# 创建代理用户
## 创建组
`groupadd proxy`

<!-- more -->

注意：系统可能已经存在proxy组，就不用再创建了
## 创建用户
`useradd ss5 -g proxy`
## 设置密码
`passwd ss5`

`ss5`
## 禁止ss5登录（为了安全）
` vi /etc/passwd`

在ss5用户那行后增加:`/bin/false`

# 配置
安装完毕后，有个配置文件：/etc/danted.conf，配置文件内容就是配置说明，建议好好阅读。

`cp /etc/danted.conf /etc/danted.conf.bak`。

可参考：官方文档 https://www.inet.no/dante/doc/latest/config/index.html，然后编辑配置文件：

`sudo vi /etc/danted.conf`

连接代理，需要有用户名密码的配置如下：
```
logoutput: /var/log/sockd.log
internal: ens33 port = 65522
external: ens33
method: username
clientmethod: none
user.privileged: root
user.notprivileged: ss5
user.libwrap: nobody
compatibility: sameport
compatibility: reuseaddr
extension: bind
client pass {
    from: 0.0.0.0/0 to: 0.0.0.0/0
}
pass {
    from: 0.0.0.0/0 to: 0.0.0.0/0
    protocol: tcp
}
```
连接代理，不需要用户名密码的配置如下：
```
logoutput: /var/log/sockd.log
internal: ens33 port = 65522
external: ens33
method: none
clientmethod: none
user.privileged: root
user.notprivileged: ss5
user.libwrap: nobody
compatibility: sameport
compatibility: reuseaddr
extension: bind
client pass {
    from: 0.0.0.0/0 to: 0.0.0.0/0
}
pass {
    from: 0.0.0.0/0 to: 0.0.0.0/0
    protocol: tcp
}
```

# 启动服务
`sudo /etc/init.d/danted start`

# 查看服务是否监听
`sudo netstat -anp | grep 65522`

# 用Proxifier 连接可以连接成功

# 重启服务
`/etc/init.d/danted restart`
总结：

查看端口占用情况，可以用命令： sudo lsof -i:端口号

查看iptables规则：sudo iptables -nvL

查看某端口是否开放，可以用telnet ip 端口号查看，比如：

telnet 127.0.0.1 80

当时配置有问题，telnet后，提示：

Escape character is '^]'.——说明telnet成功了

Connection closed by foreign host.——说明：成功后立刻被外界切断了，请求被拦截，可能配置问题

于是，再次阅读配置说明，找到配置问题，修改就可以连接。

# 参考资料：
> https://www.jianshu.com/p/9ccd2cc347d4
> https://blog.csdn.net/chenweilong20/article/details/86540005

