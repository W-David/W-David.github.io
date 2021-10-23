---
title: nginx部署多个前端项目
date: 2021-05-05 14:58:37
cover: /gallery/cover/001.jpg
thumbnail: /gallery/cover/001.jpg
tags: nginx
---
## 介绍
关于如何将多个前端SPA部署在同一个域名服务器下,经过一番搜索和测试,个人总结了两种方式,实现这种需求.
分别是:
<br>
1. <p style="text-decoration: underline;">通过二级域名配置<p>
2. <p style="text-decoration: underline;">通过location目录配置<p>

> 使用腾讯云的轻量云服务器, 系统 centos 7,Nginx版本 1.16.1,nginx配置文件位置: <p style="text-decoration: underline;">/etc/nginx/nginx.conf<p>

### 通过二级域名配置
* 一种相对简单的方案是配置多个server,监听80端口下的不同server_name,示例如下:
``` shell
// server a
server {
  listen 80;
  server_name a.test.com;

  location / {
    root /usr/share/nginx/a-test;
    index index.html;
  }
}

// server b
server {
  listen 80;
  server_name b.test.com;

  location / {
    root /usr/share/nginx/b-test;
    index index.html;
  }
}
```
> 还可以通过端口转发的方式来实现多项目部署在多个端口下,配置相对繁琐,示例如下:

``` bash
// server a
server {
  listen 80;
  server_name a.test.com;

  location / {
    proxy_pass http://localhost:7070;
  }
}

server {
  listen 7070;

  location / {
    root /usr/share/nginx/a-test;
    index index.html;
  }
}
// server b
server {
  listen 80;
  server_name b.test.com;

  location / {
    proxy_pass http://localhost:7777;
  }
}

server {
  listen 7777;

  location / {
    root /usr/share/nginx/b-test;
    index index.html;
  }
}
```
### 通过Location目录配置
* 这种方式不需要申请二级域名,nginx配置也比较简单,不过需要在项目中做一些修改.具体如下,假如域名为(www.test.com):
1. <p style="background-color: rgba(0,0,0, 0.6); color: white; padding: 5px;">修改静态资源文件的根路径 publicPath, 比如该项目的域名目录为 www.test.com/a ,则修改publicPath 为 /a/ <p>
2. <p style="background-color: rgba(0,0,0, 0.6); color: white; padding: 5px;">提供vue-router的base参数,笔者使用了hash路由,应该对应修改为 craeteWebHashHistory('/a/')<p>
* nginx 配置示例如下:
``` bash
server {
  listen 80;
  server_name www.test.com;

  location / {
    root /usr/share/nginx/html;
    index index.html;
  }

  location /a {
    alias /usr/share/nginx/html/a;
    index index.html;
  }

  location /b {
    alias /usr/share/nginx/html/b;
    index index.html;
  }
}
```
