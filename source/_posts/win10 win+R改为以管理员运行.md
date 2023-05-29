---
title: Win10-Win+R改为以管理员运行
date: 2022-01-12 22:52:23
tags:
  - Windows
  - 管理员权限
categories:
  - Windows
---

1. `win+R`打开`运行`
1. 输入`gpedit.msc`
1. 计算机配置-Windows设置-安全设置--本地策略--安全选项
<!-- more -->
1. "用户账户控制:以管理员批准模式运行所有管理员"---改为"已禁用"
1. 保存设置
1. 重启电脑