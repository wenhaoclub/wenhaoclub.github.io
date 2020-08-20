---
layout: post
title: 记--博客迁移搭建
categories: [Blog]
description: 记--博客迁移搭建
keywords: wenhao  , 文浩  , 似水似流年  , wenhaoclub  , fuwenhao.club , 文浩的博客
---
# 序：
> 所有的白嫖资源，后续还是要自己来买单。

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.css">
<script src="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/meting@1.1.0/dist/Meting.min.js"></script>

<div class="aplayer" data-id="1468270651" data-server="netease" data-type="song" data-mode="single" data-autoplay="true"></div>

## 碎碎念：
- 很长一段时间都是在GitHub上使用个人博客。鉴于免费性，高效性，稳定性以及简易性，算是白嫖资源的头牌。
- But自从三月份GitHub被攻击后，现在的个人博客网站很不稳定，经常性打不开。
- 因此才有了后话，准备将个人博客迁移到个人服务器上，带宽差了点，但支撑这些应该还是足够的。


## 正文：
> 域名：fuwenhao.club 绑定的是http://wenhaoclub.github.io/

> 域名：plus.fuwenhao.club 绑定的是https://fwh666.github.io/

### 博客搭建：
#### 方案一：Docker安装（推荐）
> 采用docker搭建，可以说是一键傻瓜式操作，简单，高效。

- 步骤：
	- 编写Dockerfile文件
	
	```
	FROM ruby:latest
	RUN gem install jekyll bundler
	COPY blog /blog/
	RUN cd blog \
	         && bundler install
	WORKDIR /blog
	CMD bundle exec jekyll serve --host 0.0.0.0
	```
	- docker build 
	- docker run
	- 后续可以采用docker-compose.yml运行容器
	
	```
	version: '3'
services:
  blog:
      build: .
      image: jekyll/blog:latest
      ports:
   		- "4000:4000"
      networks:
        - test-net
networks:
  test-net:
	```
	- 鉴于方便性，可维护性，可以编写脚本。和使用Python后台运行
	- build.sh:

	```
	git pull origin master \
        && docker-compose build \
        && docker-compose down \
        && docker-compose up -d
	```
	- build.py:

	```
	from wsgiref.simple_server import make_server
	import subprocess
	def application(environ, start_response):
	    start_response('200 OK', [('Content-Type', 'text/html')])
	    subprocess.Popen('/mnt/blog/build.sh')
	    print('update success.')
	    return [b'success, web hook!']
	httpd = make_server('127.0.0.1', 18000, application)
	print('Serving HTTP on port 18000...')
	httpd.serve_forever()
	```

#### 方案二：Cenos7系统安装
> 这个方案比较冗余麻烦些，主要是从国内访问国外的镜像源效率太低，此步骤需要耐心。

- 步骤：
	- 安装gem
	- ruby升级
	- 安装rvm
	- 安装jekyll
	- 安装bundle
	- 以上安装，网上搜索一大堆文章，不做细述
	- 注意点：
		-  bundle的版本和ruby的版本一定要匹配，不然容易出现编译错误，总之是很恶心人的错误
		-  建议：都用最新版本的。

#### 方案三：Nginx直接映射
> 这个思路很简单，就是将生成的site站点文件直接放在nginx目录下，启动即可。

- 步骤：
	- 生成_site站点文件夹，可以本地生成，或者服务器系统环境生成
	- 放在html目录下
	- 配置nginx.conf文件，监听端口映射到对应的目录下。

#### 其他注意项：
- 需要服务器的安全组中配置入站规则，放开指定端口
- 服务器本身的防火墙需要放开指定端口
- 需要生成ssl证书，才能让https访问。
	- 由于之前一直白嫖的GitHub，所以这里的服务器需要重新SSL证书配置
- 服务器和域名都要备案，这个常识不细讲了。

## 总结：
- 首先：
	- 本次搭建耗时了三个晚上，主要是方案一有一个问题无法解决，试错成本高了，才又采用方案三搭建。
	- 问题描述：能访问主页，但是主页内容文章变成0.0.0.0的ip地址导致无法打开，没有找到映射方法。
- 其次：
	- 服务器上安装jekyll真的太需要耐心了，由于下载速度慢的变态。
- 最后：
	- 就是由于把问题想简单了，总是在弯路上走了很远，才又从头开始，试错成本很高，下次需要仔细控制下。