---
layout: post
title: template page
categories: [cate1, cate2]
description: some word here
keywords: wenhao  , 文浩  , 似水似流年  , wenhaoclub  , fuwenhao.club , 文浩的博客
link: https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/head2.jpg
---

Content here

- 音乐
<!--<div align=life> 
<iframe frameborder="no" marginwidth="0" marginheight="0" width=400 height=140 src="https://music.163.com/outchain/player?type=2&id=34341360&auto=0&height=66"></iframe>
</div>-->

- 要放在文章头部
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.css">
<script src="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/meting@1.1.0/dist/Meting.min.js"></script>

- 具体音乐框-  需要替换不同的id
<div class="aplayer" data-id="29764564" data-server="netease" data-type="song" data-mode="single" data-autoplay="true"></div>

- 内容详解
data-id	歌曲/专辑/歌单 ID
data-server	netease（网易云音乐）tencent（QQ音乐） xiami（虾米） kugou（酷狗）
data-type	song （单曲） album （专辑） playlist （歌单） search （搜索）
data-mode	random （随机） single （单曲） circulation （列表循环） order （列表）
data-autoplay	false（手动播放） true（自动播放）

-  图片

<img src="https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Life/fandeng/renzhitianxing.JPG" width="150" height="250">

![垃圾算法](/images/posts/jvm/GC_memory02.png)
- newTable链接：

<a href="baidu.com" target="_blank">链接</a>

- 阅读权限--根据 container-1

<script src="https://my.openwrite.cn/js/readmore.js" type="text/javascript"></script>
<script>
    const btw = new BTWPlugin();
    btw.init({
        id: 'container-1',
        blogId: '22645-1591856403112-769',
        name: '似水似流年',
        qrcode: 'https://s1.ax1x.com/2020/06/04/tBkyU1.jpg',
        keyword: '文浩',
    });
</script>