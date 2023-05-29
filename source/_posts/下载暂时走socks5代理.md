---
title: 下载暂时走socks5代理
date: 2022-02-28 12:20:29
tags:
  - Linux
  - 网络代理
  - socks5
categories:
  - [Linux]
  - [网络代理]
  - [socks5]
---
`ALL_PROXY=socks5://202.204.54.61:8181 $命令`

# 全局代理，写入配置
`git config --global http.proxy 'socks5://127.0.0.1:1086'`

`git config --global https.proxy 'socks5://127.0.0.1:1086'`

# 清除配置
`git config --global --unset http.proxy`

<!-- more -->

`git config --global --unset https.proxy`

# 临时代理
`ALL_PROXY=socks5://127.0.0.1:8888 git clone https://github.com/some/one.git`


>作者：谢昆明
链接：https://www.jianshu.com/p/1a1c764661ed
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。