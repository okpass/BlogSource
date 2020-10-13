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
