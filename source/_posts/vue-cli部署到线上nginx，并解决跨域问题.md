---
title: vue-cli部署到线上nginx，并解决跨域问题
date: 2019-08-25 19:36:41
tags:
  - 前端开发
  - Vue
  - Vue-cli
  - Nginx
categories:
  - [前端开发]
  - [Vue, Vue-cli]
  - [Nginx]
---
# vue-cli部署到线上nginx，并解决跨域问题

## 参考链接
> https://blog.csdn.net/qq_33036599/article/details/82789666
> https://www.cnblogs.com/web-record/p/9467258.html
> https://www.cnblogs.com/xiangsj/p/8905648.html

<!-- more -->

## 1 把vue项目打包，并传到服务器
在`/config/index.js`文件里，编辑

+ `assetsPublicPath: './',`
+ `productionSourceMap: false,`

再`npm run build`
把生成的`dist`文件夹拷贝到服务器

## 2 配置服务器nginx

打开配置文件，高鸣飞的服务器中nginx配置文件在`/etc/nginx/conf.d`

配置文件中加上：

```
server {
     # 配置域名和端口
     listen     8080;
     server_name  api.iblue.tech;
     # 配置vue项目的位置
     location / {
         root /home/ubuntu/easy_projects_web/dist/;
     }
     # 配置跨域请求，把/api/的请求都转发到https://api.iblue.tech/api/
     location /api/ {
         add_header 'Access-Control-Allow-Origin' '*';
         proxy_pass https://api.iblue.tech/api/;
         proxy_set_header Host $http_host;
         proxy_cookie_path /api /;
         proxy_cookie_domain localhost:8080 https://api.iblue.tech/api/;
     }
}
```

## 重启nginx

`nginx -s reload`

# 后端部署

`source ~/easy-env/bin/activate`

`uwsgi --ini ~/easy_projects_server/easy_projects_server.ini`