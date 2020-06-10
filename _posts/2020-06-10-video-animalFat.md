---
layout: post
title: 视频---《如果动物都变胖》
categories: Video
description: The Video
keywords: wenhao, 文浩, video, animal
---

> 序： 中午茶余饭后，不睡觉的精神小伙搞起了视频博文，遂发生如下故事：

## 如果动物都变胖：

<video controls="controls" src="https://f.video.weibocdn.com/TJeEhRlHlx07DXhPVxNe01041202qMuE0E010.mp4?label=mp4_720p&template=1280x720.25.0&trans_finger=721584770189073627c6ee9d880087b3&Expires=1591779732&ssig=%2FoQekrzTlJ&KID=unistore,video"></video>


## 视频博文：
### 准备工作：
- 源视频文件
- 登录微博
- 博文加载

### 操作步骤：
- 个人微博主页上传视频文件发布
- 发布成功后点击视频文件主页
- 通过network获取mp4文件的请求地址URL路径，可以通过“mp4”过滤请求。
- 这个URL路径是后续博文中添加要使用的。
- 博文代码如下：
`<video controls="controls" src="https://f.video.weibocdn.com/TJeEhRlHlx07DXhPVxNe01041202qMuE0E010.mp4?label=mp4_720p&template=1280x720.25.0&trans_finger=721584770189073627c6ee9d880087b3&Expires=1591779732&ssig=%2FoQekrzTlJ&KID=unistore,video"></video>`
- 其中src对应的内容，即URL路径详细地址。

### 相关问题：
- 视频目前无法自适应屏幕，需要做iframe处理
- 获取视频URL路径流程有些繁琐，后续考虑B站上直接获取用于发布博文

### 总结：
- 后续考虑授权认后证观看指定视频
- Dowine4工具方便获取视频，但需要破解