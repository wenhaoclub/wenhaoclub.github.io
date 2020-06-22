---
layout: post
title: docker-RabbitMq安装部署
categories: [Docker, rabbitmq]
description: docker-RabbitMq安装部署
keywords: wenhao  , 文浩  , 似水似流年  , wenhaoclub  , fuwenhao.club , 文浩的博客 , docker , rabbitmq
---

## 序：

>  之前docker系列云笔记同步--搭建RabbitMq篇

## 教程：
### 获取：

- 下载：
    - docker search rabbitmq
    - docker pull rabbitmq

### 运行：
- 运行命令：

```
docker run -d --name rabbitmq-smartpark -p 5671:5671 -p 5672:5672 -p 4369:4369 -p 25672:25672 -p 15671:15671 -p 15672:15672 -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=123456 rabbitmq:3.6.6-management
```
- 查看日志：
    - docker logs rabbitmq-smartpark
- 进入命令：
    - docker exec -it rabbitmq-smartpark /bin/bash

### 容器内：
- 查看配置文件：
    - /etc/rabbitmq/rabbitmq.config

### 参考地址：
- https://youmeek.gitbooks.io/linux-tutorial/content/markdown-file/RabbitMQ-Install-And-Settings.html