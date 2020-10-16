---
title: Hexo Geektutu主题Gitalk初始化位置
date: 2020-10-13 00:52:14
tags: 
- Hexo
- Geektutu
categories: 博客搭建
image: img/category/blog.jpeg
keywords: [Hexo, Hexo Blog, Hexo博客, Geektutu]
description: 如何在geektutu主题中使用gitalk
---
Geektutu是[Hexo主题官网](https://hexo.io/themes/)上的一款主题，其风格适合程序员个人博客使用，Geektutu的作者在其[GitHub](https://github.com/geektutu/hexo-theme-geektutu)上的更新日记中有提及对接入Gitalk的支持，但并未提供配置方法，我稍微在主题工程代码里搜索出了Gitalk的配置方式，如下

- _config.xml加上一行配置
``` xml
gitalk: true 
```
- 找到geektutu/layout/_widget/comments.ejs文件，将参数填入Gitalk初始化参数列表即可
``` js
<script>
    window.addEventListener('load', function () {
        function renderCommentUrl(data) {
            var url = (data[window.location.pathname] || {}).url
            url && getDom('#gitalk-container button').addEventListener('click', function (e) { window.open(url)})
            url || (getDom('#gitalk-container').style.display = 'none')
        }
        const gitalk = new Gitalk({
            clientID: 'ff21300f马赛克c1e60d4a',
            clientSecret: '83cfe65f4马赛克2b820acf48ac8c5099',
            // accessToken: '<%- config.gitalk.accessToken %>',
            repo: 'Blog_Comment',
            owner: 'drawfromwreck',
            admin: ['drawfromwreck'],
            id: window.location.pathname,
            distractionFreeMode: false
        });
        fetch("https://api.github.com/user").then(function(resp){
            gitalk.render('gitalk-container');
        }).catch(function(e){
            fetch('/tool/issues.json').then(function (r) { return r.json() }).then(renderCommentUrl).catch(function (e) { })
        })
        getDom('#gitalk-container').addEventListener('click', function (e) {
            e && e.stopPropagation && e.stopPropagation();
        });
    })
</script>
```

Gitalk是一款评论插件，只需在GitHub个人主页新建Application，获取相应的clientID，clientSecret，并新建一个用于存储评论数据的仓库，将以上数据配置到上述位置即可在Geektutu主题中使用Gitalk功能，读者只需要用GitHub账号登录便可评论交流。