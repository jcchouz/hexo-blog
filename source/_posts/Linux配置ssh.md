---
title: Linux配置SSH
date: 2022-01-12 13:48:57
tags:
  - Linux
  - SSH
categories:
  - [Linux, SSH]
---
# 服务端（必须）
#### 安装ssh服务端
`sudo apt-get install openssh-server`

#### 确认sshserver是否启动了
`ps -e | grep ssh`

如果只有ssh-agent那ssh-server还没有启动，需要`/etc/init.d/ssh start`，如果看到sshd那说明ssh-server已经启动了

<!-- more -->

#### 启动sshserver  
`/etc/init.d/ssh start`

#### SSH配置（如果需要）
修改配置文件/etc/ssh/sshd_config，这里可以定义SSH的服务端口，默认端口是22，你可以自己定义成其他端口号如32，然后重启服务

#### 重启sshserver  
`/etc/init.d/ssh restart`

# 客户端（非必须）
#### 安装客户端（客户端不是必须的）
apt-get install ssh

#### 如果安装失败，则使用下面命令进行安装
apt-get install openssh-client

#### SSH登录（客户端）
ssh 192.168.159.128

ssh -l cookie 192.168.152.10

ssh cookie@192.168.152.10