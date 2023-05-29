<!--
 * @Author: jcchouz 39292190+jcchouz@users.noreply.github.com
 * @Date: 2023-05-30 01:03:24
 * @LastEditors: jcchouz 39292190+jcchouz@users.noreply.github.com
 * @LastEditTime: 2023-05-30 01:08:57
 * @FilePath: \hexo-blog\source\_posts\WinSCP使用root用户连接服务器.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
---
title: WinSCP使用root用户连接服务器
date: 2020-11-29 06:18:37
tags:
  - WinSCP
  - Linux
  - Root
categories:
  - [WinSCP]
---

### 1 在202.104.62.145服务器中查看sftp服务在你服务器的路径

以root身份输入命令`find / -name sftp-server -print`

得到路径为`/usr/lib/openssh/sftp-server`

### 2 WinSCP设置

1. 点击'高级'-'SFTP'，在'SFTP服务器'中输入`sudo /usr/lib/openssh/sftp-server`
<!-- more -->
![](https://i.loli.net/2018/11/29/5bff8a39b7de3.jpg)

2. 点击'Shell'，在Shell中输入`sudo -i`
![](https://i.loli.net/2018/11/29/5bff8a7b0c5b9.jpg)

3. 点击'确定'
4. 输入用户名`root`
5. 输入root用户的密码
6. 连接