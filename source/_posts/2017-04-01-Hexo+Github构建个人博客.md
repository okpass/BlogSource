---
title: Hexo+Github构建个人博客
date: 2017-04-01 23:59:57
tags: 
- Hexo
- Github
---

本篇教程讲述如何用Hexo和Github搭建一个静态博客。

# 一、安装Node.js
- 到Node.js[官网](http://nodejs.cn/download/)下载最新版本安装。本教程使用6.3.0版本。
- 安装完毕后打开命令行，输入
{% codeblock lang:js %}
node -v
{% endcodeblock %}若正常显示版本号，则安装成功
{% iframe \images\2017-04-01-Hexo+Github构建个人博客\1.png 181 41 %}

# 二、安装Hexo
[Hexo](https://hexo.io/zh-cn/)是一个快速、简洁且高效的博客框架，支持Markdown格式编写，能够快速构建、部署博客。
- 创建一个文件夹，你的博客文件将存放在此文件夹内，例如E:/MyBlog
- 进入MyBlog文件夹，shift+右键弹出菜单，点击"在此处打开命令窗口"，输入命令在全局安装hexo框架
{% codeblock lang:js %}
npm install hexo-cli -g
{% endcodeblock %}
- hexo安装完成后，输入
{% codeblock lang:js %}
hexo init
{% endcodeblock %}
程序将自动克隆Github上的hexo初始工程到目录中，并安装package.json里默认的依赖包
