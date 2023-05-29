---
title: virtualenv-Python创建虚拟环境
date: 2020-07-01 18:23:52
tags:
  - Python
  - virtualenv
categories:
  - [Python, virtualenv]
---
> 创建虚拟环境的命令:  
> `virtualenv E:\PythonProjects\Envs\hbase_env`
> 
> 指定python版本：  
> `virtualenv -p C:\Python27\python2.exe E:\PythonProjects\Envs\hbase_env`

<!-- more -->

1. 创建虚拟环境：`virtualenv -p C:\Python27\python2.exe E:\PythonProjects\Envs\hbase_env`
2. 激活：`E:\PythonProjects\Envs\hbase_env\Scripts\activate`
3. 执行命令（把pip改成pip2,因为电脑中有两个版本的Python，所以需要这一步）：`python2 -m pip install --upgrade pip --force-reinstall`
4. 之后安装包可以使用`pip`或`pip2`
