---
title: FFmpeg下载安装（Linux、Windows）
date: 2022-02-28 19:37:08
tags:
  - FFmpeg
  - Windows
  - Linux
categories:
  - [FFmpeg]
  - [Windows]
  - [Linux]
---
# Linux
>访问`https://www.johnvansickle.com/ffmpeg/`，进old releases下载ffmpeg

+ 下载ffmpeg
+ `tar xvf ffmpeg-release-64bit-static.tar.xz`
+ `cd ./ffmpeg-3.3.3-64bit-static/`
<!-- more -->
+ `sudo cp ffmpeg ffprobe /usr/local/bin`

# Windows
1. FFmpeg下载

下载地址：http://ffmpeg.zeranoe.com/builds/

根据你的系统选择下载

2. 下载好后解压，会生成一个类似名为“ffmpeg-20180820-78d4b6b-win64-static”的文件夹


3. 打开你想安装的任意磁盘，例如：d盘。新建一个名为“ffmpeg”的文件夹，将第二步解压生成的文件夹中的内容全部拷贝到“ffmpeg”文件夹中

4. 配置FFmpeg环境变量，属性-高级系统设置-环境变量-下面'系统变量'里面的Path-增加`C:\Program Files (x86)\ffmpeg\bin`

5.打开命令提示符窗口。输入命令“ffmpeg –version”。如果命令提示窗口返回FFmpeg的版本信息，那么就说明安装成功了，你可以在命令提示行中任意文件夹下运行FFmpeg。

6. FFmpeg的相关命令见官方文档

https://www.ffmpeg.org/ffmpeg.html
