---
title: Git撤销单个文件的修改
date: 2020-08-22 17:59:40
tags:
  - Git
categories:
  - Git
---
# 问题描述
Git撤销单个文件的修改

# 解决办法
1. `git log` 查看上一版本的sha-1
2. `git checkout $sha1-of-a-commit $/path_of_file`

<!-- more -->

# 参考网址
> https://blog.csdn.net/w605283073/article/details/80118794