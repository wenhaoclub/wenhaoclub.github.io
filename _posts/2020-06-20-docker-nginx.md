---
layout: post
title: Docker-Nginx安装启动
categories: [Docker, Nginx]
description: Docker之Nginx安装启动
keywords: wenhao  , 文浩  , 似水似流年  , wenhaoclub  , fuwenhao.club , Docker , Nginx
---

## 序：
> 之前docker系列云笔记同步--Nginx搭建篇

## 教程：
### 安装：
- docker search nginx
- docker pull nginx
- 本地创建挂载目录，方便管理（可不选择创建）
    -  mkdir -p ~/nginx/www ~/nginx/logs ~/nginx/conf
    -  注意：提前准备好nginx.conf文件放在conf文件夹内。否则容易报错。
    -  html目录内放入index.html 用于验证。
- **启动命令：**
    - `docker run -p 80:80 --name fwh-nginx -v $PWD/www:/www -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf --privileged=true -v $PWD/logs:/www/logs -v $PWD/html:/etc/nginx/html  -d nginx`

## 问题：
- docker启动报错：
    - 解决：https://www.itread01.com/content/1545288193.html
    - 提前准备nginx.conf文件