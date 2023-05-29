---
title: 项目上线，vue-cli部署到新的服务器
date: 2019-11-29 10:04:38
tags:
  - 前端开发
  - Vue
  - Vue-cli
categories:
  - [前端开发]
  - [Vue, Vue-cli]
---
### 新的服务器参数
```
47.94.173.161:22
root
lixiang123.
```

### 开始部署

#### 1 把vue项目打包，并传到服务器

在`/config/index.js`文件里，编辑名为"build"的json:

+ `assetsPublicPath: './',`
+ `productionSourceMap: false,`

<!-- more -->

再`npm run build`，把生成的`dist`文件夹拷贝到服务器`/root/easy_projects_web`

#### 2 配置服务器nginx

打开配置文件，高鸣飞的服务器中nginx配置文件在`/etc/nginx/conf.d`

配置文件中加上：

```
server {
     listen 443;
     server_name www.zhlixiang.com; #填写绑定证书的域名
     ssl on;
     ssl_certificate 1_www.zhlixiang.com_bundle.crt;
     ssl_certificate_key 2_www.zhlixiang.com.key;
     ssl_session_timeout 5m;
     ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
     ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
     ssl_prefer_server_ciphers on;

     location / {
         alias /root/easy_projects_web/;
     }
     location /api/ {
         include         uwsgi_params;
         uwsgi_pass      unix:/root/easy_projects_server/easy_projects_server.sock;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header Host $http_host;
         proxy_http_version 1.1;
         proxy_redirect off;
         proxy_buffering off;
         proxy_set_header Upgrade $http_upgrade;
         proxy_set_header Connection "upgrade";
         proxy_read_timeout 86400;
         client_max_body_size 0;
     }
    location /media/ {
        alias /root/easy_projects_server/media/;
    }
}

server {
    listen 80;
    server_name www.zhlixiang.com;
    rewrite ^(.*)$ https://${server_name}$1 permanent;
}
```

#### 3 重启nginx

`nginx -s reload`

#### 4 尝试访问

https://www.zhlixiang.com/

访问成功