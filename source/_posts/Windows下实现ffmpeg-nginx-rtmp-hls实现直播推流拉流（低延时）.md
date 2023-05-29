---
title: Windows下实现ffmpeg+nginx+rtmp+hls实现直播推流拉流（低延时）
date: 2022-08-22 20:26:22
tags:
  - Windows
  - FFmpeg
  - Nginx
  - RTMP
  - HLS
categories:
  - [Windows]
  - [FFmpeg]
  - [Nginx]
---
> 参考链接  
https://blog.csdn.net/u011925282/article/details/102580420
https://blog.csdn.net/eguid_1/article/details/83000705
https://zhuanlan.zhihu.com/p/136959101
https://blog.csdn.net/u014552102/article/details/103302731
https://www.cnblogs.com/tocy/p/using-ffmpeg-build-hls-live-system.html
https://blog.csdn.net/ai2000ai/article/details/80756892

#### 额外补充

显示所有摄像头名称：`ffmpeg -list_devices true -f dshow -i dummy`

<!-- more -->

### 用于摄像头的rtmp推流（需要浏览器flash支持，另外目前有黑屏问题，能看到网卡有数据流，但黑屏）
`ffmpeg -f dshow -i video="摄像头名称" -vcodec libx264 -preset:v ultrafast -tune:v zerolatency -f flv rtmp://127.0.0.1:1935/live/home`

我的：`ffmpeg -f dshow -i video="Daheng Imaging Device 1" -vcodec libx264 -preset:v ultrafast -tune:v zerolatency -f flv rtmp://127.0.0.1:1935/live/home`

`ffmpeg -rtbufsize 50000 -f dshow -i video="Daheng Imaging Device 1" -vcodec libx264 -preset:v ultrafast -tune:v zerolatency -f flv rtmp://127.0.0.1:1935/live/home`

> '-rtbufsize 50000ffmpeg'：表示缓存设置为50000，（默认缓存:3041280）

### 用于摄像头的m3u8(hls)推流（不需要浏览器flash支持，推荐）
`ffmpeg -f dshow -i video="Daheng Imaging Device 1" -vcodec libx264 -preset:v ultrafast -tune:v zerolatency -force_key_frames "expr:gte(t,n_forced*1)" -f flv rtmp://127.0.0.1:1935/hls/home`
> 其中，参数-force_key_frames "expr:gte(t,n_forced*1)"表示强制每1秒一个关键帧

http访问地址：`http://127.0.0.1:8765/hls/home.m3u8`

摄像头参数设置：
+ 图像宽度 1280
+ 图像高度 720
+ 水平偏移 2108
+ 垂直偏移 1476

湖南卫视 rtmp: `rtmp://58.200.131.2:1935/livetv/hunantv`

CCTV1 hls: `http://ivi.bupt.edu.cn/hls/cctv1hd.m3u8`