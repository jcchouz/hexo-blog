---
title: 安装win10+Ubuntu16.04双系统
date: 2021-10-25 23:39:24
tags:
  - Windows
  - Linux
  - 双系统
categories:
  - [Windows]
  - [Linux]
  - [双系统]
---
安装win10+Ubuntu16.04双系统
参考链接：https://blog.csdn.net/ifreewolf_csdn/article/details/81330921

复盘，几个要点：
1. 双硬盘都要改为GPT分区格式，主板启动方式设置为UEFI

<!-- more -->

2. 装win10，要直接用U盘启动盘装，我第一次用PE系统装就出错了
3. 提前在C盘分出500MB的空白卷，用来装Ubuntu16.04的引导。因为是双硬盘，一般就是一块硬盘分C盘，一块硬盘分D、E...盘，而系统引导最好是放在一块硬盘上，因为系统启动时是按照硬盘启动的，所以要把Ubuntu16.04的引导放在C盘，即同一块硬盘上
4. 其余Ubuntu16.04的安装，都放在另一块硬盘分出的区域，一般至少100G
5. 分区方案：
    1. Ubuntu16.04系统引导放在第一块硬盘，即C盘分出的500MB空白卷，重点！
        + /boot，C盘的分出的500MB，主分区，空间起始位置（Ubuntu16.04系统引导）
    2. 其余分区，另一块硬盘的空白卷
        + swap，内存大小的2倍，逻辑分区，空间起始位置，交换空间
        + /，20G，逻辑分区，空间起始位置，ext4
        + /tmp，5G，逻辑分区，空间起始位置，ext4
        + /home，剩余所有容量，逻辑分区，空间起始位置，ext4
6. 安装完成后，默认Ubuntu16.04是第一启动项，如果需要把win10改为第一启动项，做法是（参考链接：http://blog.sina.com.cn/s/blog_53a99cf30102yyyt.html）：
    1. 终端中运行 `sudo gedit /boot/grub/grub.cfg`
    2. 找到`set default="0"`，修改`set default="0"`， 从第一条开始算，0是第一条，第三条就是2