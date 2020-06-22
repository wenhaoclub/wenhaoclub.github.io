---
layout: post
title: docker-Mysql安装运行部署
categories: [Docker, MySQL]
description: docker-Mysql安装运行部署
keywords: wenhao  , 文浩  , 似水似流年  , wenhaoclub  , fuwenhao.club , 文浩的博客 ,  Docker , mysql
---
## 序：

> 之前docker系列云笔记同步--MySQL搭建篇

## 教程：

### 获取：
- docker search mysql
- docker pull mysql:latest
- 查看命令：docker images

### 运行：
 - 本地命令：

 ```
 docker run -p 3306:3306 --name fwh_mysql -v /Users/fwh/dokcerData/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456  -d mysql:5.6
```

- 线上运行命令记录：

```
docker run -d  -e MYSQL_ROOT_PASSWORD=JdAi123456  -e TZ=Asia/Shanghai  --name mysql-smartpark  --restart always  -v /data/mysql/smartpark/my.cnf:/etc/mysql/my.cnf  -v /data/mysql/smartpark/data:/var/lib/mysql  -p 13306:3306  mysql:5.7

注意：相关目录要提前创建。my.conf文件创建
```

- 参数解释:
- run                 运行一个docker容器
- --name           后面这个是生成的容器的名字qmm-mysql
- -p 3306:3306  表示这个容器中使用3306（第二个）映射到本机的端口号也为3306（第一个） 
- -e MYSQL_ROOT_PASSWORD=123456  初始化root用户的密码
- -d                   表示使用守护进程运行，即服务挂在后台
- -v 指定容器内目录与宿主机目录共享(: 之前是宿主机文件夹，之后是容器需共享的文件夹)，

### 验证:
- docker ps //查看是否运行
- mysql -hlocalhost -uroot -p123456 
    -   查看是否登陆成功
- 注意：
    -  宿主机开放的端口要和MySQL一致，或者对应上，否则导致Navicat连接拒绝

### MySQL配置使用：
- 授权：
    -   GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.*.*' IDENTIFIED BY 'password' WITH GRANT OPTION;
    -   flush privileges;