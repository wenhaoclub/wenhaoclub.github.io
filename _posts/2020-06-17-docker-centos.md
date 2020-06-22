---
layout: post
title: CentOs7安装docker
categories: [Docker]
description: CentOs7安装docker
keywords: wenhao  , 文浩  , 似水似流年  , wenhaoclub  , fuwenhao.club , docker , centos
---
## 序：

> 之前docker系列云笔记同步……

## 教程：
- 卸载旧版本：
    - $ sudo yum remove docker \
           docker-common \
           docker-selinux \
           docker-engine

- 配置国内源：
    - $ sudo yum-config-manager \
    --add-repo \
    https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo

- yum更新安装：
    - sudo yum update
    - sudo yum install -y yum-utils \
        device-mapper-persistent-data \
        lvm2

- 安装docker：
    - sudo yum install docker-ce

- 加入docker组命令：
    - sudo usermod -aG docker USER_NAME(此处为当前登陆账号)

- 开机启动：
    - sudo systemctl enable docker

- 启动：
    - sudo systemctl start docker

- 其他：
    - 更新：yum update docker-ce
    - 卸载：sudo yum remove docker-ce
    - 删除本地文件：
        - sudo rm -rf /var/lib/docker

- 参考文档：
    - [https://juejin.im/post/5dc241ce6fb9a04aa333c1bd](https://juejin.im/post/5dc241ce6fb9a04aa333c1bd)
    - [https://qizhanming.com/blog/2019/01/25/how-to-install-docker-ce-on-centos-7](https://qizhanming.com/blog/2019/01/25/how-to-install-docker-ce-on-centos-7)

## 相关链接：

- [docker中文官网](https://www.docker.org.cn/)
- [docker英文官网下载地址](https://www.docker.com/products)

## 总结：
- 此处为Linux系统yum安装方式，后续增加其他安装方式。
- 待增加Windows系统安装，需要了解docker toolbox
- 待增加Mac系统安装，采用brew cask install docker方式
- 后续记录docker安装其他工具，常用命令以及应用场景
