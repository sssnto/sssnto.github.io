---
title: nginx 搭建本地yum源
date: 2018-3-25 10:10:05
author: sssnto
tags: ambari  nginx 
category: 大数据
---


```
## 安装ngxin
yum install -y nginx

## 启动nginx
service nginx start

编写文件服务器配置
vim /etc/nginx/conf.d/ambari.conf


server {
    listen  80;
    server_name  repo.master.local; # 自己PC的ip或者服务器的域名
    charset utf-8; # 避免中文乱码
    root /export/images/ambari;  #路径为文件管理的根目录
    location / {
        autoindex on; # 索引
        autoindex_exact_size on; # 显示文件大小
        autoindex_localtime on; # 显示文件时间
    }
}

##nginx重新加载配置
nginx -s reload
```