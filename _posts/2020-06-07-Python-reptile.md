---
layout: post
title: 入坑Python爬虫之小试牛刀
categories: Python
description: 一个精神小伙一时兴起入坑Python爬虫
keywords: wenhao,文浩, Python, 爬虫
---

> 序： 在六月六号的一个早晨，一个精神小伙头脑一时兴起，想学爬虫技术。遂，发生如下故事：

## 准备工作：
- 工具下载：
	-  pycharm： 是用于Python开发的集成工具。 
		-  下载地址：https://www.jetbrains.com/zh-cn/pycharm/download/
		-  免费三十天试用。
		-  可下载jetbrains-agent-latest.zip破解包，无限期使用
- 环境介绍：
	- 电脑系统环境：MacPro（10.14.6 ）
	- Python环境版本 ：3.7.7
- 视频课程：
	- 爬虫五天速成：https://www.bilibili.com/video/BV12E411A7ZQ?from=search&seid=14927025725998045044
	- 个人是参考B站视频学习实践的。上述视频讲的很精细，需要耐着性子听讲。我是按照1.5倍速快进学习的。

## 具体实操：
### 请求获取：
- 常用请求方式为get和post两种
- 相关代码如下：
    ![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/posts/python/requestMethod.png)

### 数据解析：
- 此处是爬虫最重要也最繁琐的一部分
- 需要根据爬取下来的html网页进行分析，解析出我们想要的数据
- 部分代码截图如下：
	![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/posts/python/spider01.png)

### 数据保存：
- 此处用到xls文件保存，即Excel表格
- 核心点：在于确认数据有多少列，每列数据行对应上，关键点在于熟悉双层for循环
- 部分代码截图如下：
	![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/posts/python/spider02.png)

### 其他功能：
#### 图片下载：
- 代码如下：
	![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/posts/python/pic.png)

#### 异步操作：
- 测试代码如下：
	![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/posts/python/testAsy.png)


## 开发Demo地址：	
- 其中一部分demo为爬取豆瓣电影250
- 另一分部demo为爬取婚恋网站会员数据
- [GitHub](https://github.com/fwh666/spider.git)：https://github.com/fwh666/spider.git

## 总结：
- 能力上:
	- 算是获得了一个新的技术，后续打算把这个技能点加满。
- 时间上：
	- 耗费了周末两天时间，但因为感兴趣，所以愿意投入更多的时间精力去做这件事情。享受其中专注的过程。
- 其他上:
	- 周末两天从学习到个人实践，开发整理出demo来，收获颇丰，喜欢充实自己的生活。
	- 想想外面38摄氏度的高温，不如宅在家里吃着冰镇西瓜，敲着代码，晚上刷部电影，开心的做个死宅男。
	- 另：本来一个白净小伙，两天时间有点邋遢大叔的样子，幸亏还是有坚持跑步的习惯，避免成为了死肥宅……[😱]