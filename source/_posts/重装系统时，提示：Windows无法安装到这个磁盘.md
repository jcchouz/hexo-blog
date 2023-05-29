---
title: 重装系统时，提示：“Windows无法安装到这个磁盘，选中的磁盘具有MBR分区表。在EFI系统上，Windows只能安装到GPT磁盘。”
date: 2020-08-16 14:40:16
tags:
  - Windows
  - 重装系统
categories:
  - Windows
---
# 问题描述
重装系统时，提示:
> Windows无法安装到这个磁盘，选中的磁盘具有MBR分区表。在EFI系统上，Windows只能安装到GPT磁盘。

# 解决办法

退回到选择语言的界面，按`Shift+F10`，就会启动具有管理员权限的CMD

1. `diskpart`（启动Diskpart程序）
2. `list disk` （查看电脑中有哪些磁盘）
3. `select disk 0`（选中编号为0的磁盘）
<!-- more -->
4. `clean`（清除磁盘所有分区）
5. `convert gpt` （将磁盘转换成GPT格式）
6. `list partition`（查看当前磁盘分区情况）
7. `create partition efi size=100`（默认大小为M）
8. `create partition msr size =128`
9. `create partition primary size =102400`（处为你想设置C盘的大小）
10. 两次输入`exit`

> 注：7、8、9三步可以不做，在装系统的时候直接选择未分配区域即可。系统会自动创建EFI分区和MSR分区。

# 参考网址
> https://www.cnblogs.com/John-2011/p/10287001.html