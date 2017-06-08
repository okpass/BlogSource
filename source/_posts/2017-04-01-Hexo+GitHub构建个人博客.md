---
title: Hexo+GitHub构建个人博客
date: 2017-04-01 23:59:57
tags: 
- Hexo
- GitHub
---

本篇教程讲述如何用Hexo和GitHub搭建一个静态博客。

# 一、安装Node.js
- 到Node.js[官网](http://nodejs.cn/download/)下载最新版本安装。本教程使用6.3.0版本。
- 安装完毕后打开命令行，输入
{% codeblock lang:js %}
node -v
{% endcodeblock %}若正常显示版本号，则安装成功
{% iframe \images\2017-04-01-Hexo+GitHub构建个人博客\1.png 181 41 %}

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
程序将自动克隆GitHub上的hexo初始工程到目录中，并安装package.json里默认的依赖包
- 测试Hexo博客。输入命令
{% codeblock lang:js %}
hexo server
{% endcodeblock %}
本地测试默认监听4000端口，开启后显示以下信息则启动成功
![](\images\2017-04-01-Hexo+GitHub构建个人博客\2.png)
还可以使用以下命令启动博客，以输出更详细的启动信息
{% codeblock lang:js %}
hexo server --debug
{% endcodeblock %}
服务器启动后会自动监听文件改变，大部分改变在文件保存后直接访问[localhost:4000](localhost:4000)即可查看效果。
也可以使用以下简化命令来启动博客
{% codeblock lang:js %}
hexo s
{% endcodeblock %}

# 三、安装Git
- 网上很多教程推荐在[msysgit.github.io](msysgit.github.io)下载，但是我访问不了。我推荐在[git-scm.com](https://git-scm.com/download/win)下载。本教程使用[Git-2.12.2-64-bit](https://github.com/git-for-windows/git/releases/download/v2.12.2.windows.1/Git-2.12.2-64-bit.exe)版本。一般安装一直往下一步点就行。
- 安装完毕在命令行输入命令，显示git版本号则成功安装
{% codeblock lang:js %}
git --version
{% endcodeblock %}
![](\images\2017-04-01-Hexo+GitHub构建个人博客\3.png)

# 四、使用GitHub托管你的博客
GitHub提供GitHub Pages服务，为你建立的每一个项目提供专有的页面给其他人访问，如果我们将博客生成的静态HTML文件等内容布置到GitHub Pages上，那么其他人就可以访问这个页面来阅读你的博客，相当于一个服务器的作用。

假设你的GitHub用户名为yourname，那么如果你想建立你的GitHub个人主页，可以新建一个名为yourname.github.io的仓库，这样GitHub会为你生成一个名为yourname.github.io/的页面作为你的个人主页。而其他名字的仓库(例如other)生成的GitHub Pages则会是yourname.github.io/other/的地址形式。

我们需要建立两个repository，一个是用来存放博客的源代码，另一个用来存放博客生成的静态文件。我的博客源代码存放在[https://github.com/drawfromwreck/BlogSource](https://github.com/drawfromwreck/BlogSource)，博客静态文件存放在[https://github.com/drawfromwreck/drawfromwreck.github.io](https://github.com/drawfromwreck/drawfromwreck.github.io)中，于是我建立的两个仓库名字分别为"drawfromwreck.github.io"和"BlogSource"。
- 首先在[GitHub](https://github.com/)上申请自己的帐号。
- 申请好之后，在主页点击Start a project建立新的代码仓库(repository)
![](\images\2017-04-01-Hexo+GitHub构建个人博客\4.png)
- 输入你所设定的两个仓库名字，本教程中分别为"drawfromwreck.github.io"和"BlogSource"。这样GitHub仓库就创建好了。
![](\images\2017-04-01-Hexo+GitHub构建个人博客\5.png)
![](\images\2017-04-01-Hexo+GitHub构建个人博客\6.png)

# 五、配置SSH
- 在E:/MyBlog/里点击鼠标右键，点击"Git Bash Here"
- 输入命令
{% codeblock lang:js %}
cd ~/.ssh
{% endcodeblock %}
如果目录不存在，则创建目录
{% codeblock lang:js %}
cd ~/
mkdir .ssh
{% endcodeblock %}
- 进入.ssh目录，生成ssh证书
{% codeblock lang:js %}
cd ~/.ssh
ssh-keygen -t rsa -C "your e-mail"
{% endcodeblock %}
这里的-t表示type为rsa加密，-C(Common)表示生成的key最后加上你GitHub帐号的邮箱名，以提示这段加密串是属于哪个帐号的。
- 提示选择保存证书的文件名，直接回车使用默认值
![](\images\2017-04-01-Hexo+GitHub构建个人博客\7.png)
- 提示设置passphrase，此密码以后每次提交的时候需要输入。再次输入以确认密码，则成功创建证书。
![](\images\2017-04-01-Hexo+GitHub构建个人博客\8.png)
- 查看ssh公钥
{% codeblock lang:js %}
cat id_rsa.pub
{% endcodeblock %}
![](\images\2017-04-01-Hexo+GitHub构建个人博客\9.png)
- 点击GitHub右上角的用户头像，点击Settings，点击右侧的SSH and GPG keys，点击右上角的New SSH key加入新的ssh证书。Title我设置成为home表示这是家里的电脑提交时用的证书，Key里粘贴上刚刚生成的ssh公钥，记得把最后加上的邮箱名去掉。
![](\images\2017-04-01-Hexo+GitHub构建个人博客\10.png)

# 六、上传博客静态文件
- 配置_config.yml文件。用文本编辑器打开博客根目录下的_config.yml文件，拉到最下面，如图设置
![](\images\2017-04-01-Hexo+GitHub构建个人博客\11.png)
deploy表示发布设置，type选择git发布，repo表示仓库地址的配置，这里我们按如下格式填写，yourname表示你的GitHub用户名，master表示发布到仓库的master分支。
{% codeblock lang:yml %}
github:git@github.com:yourname/yourname.github.io.git,master
{% endcodeblock %}
- 安装hexo的git发布工具
{% codeblock lang:js%}
npm install hexo-deployer-git
{% endcodeblock %}
- 生成静态文件并发布到GitHub仓库。输入以下其中一种命令，hexo便会生成public文件并将public文件夹内容发布到仓库
{% codeblock lang:js %}
hexo d -g
hexo g -d
{% endcodeblock %}
提示输入ssh设置的密码，输入密码即可发布成功
![](\images\2017-04-01-Hexo+GitHub构建个人博客\12.png)
- 进入GitHub仓库github.com/yourname/yourname.github.io，此时仓库中已有上传的静态文件
![](\images\2017-04-01-Hexo+GitHub构建个人博客\13.png)
- 点击右侧Settings，在GitHub Pages项选择Source为master branch，点击Save，页面刷新后则已生成个人博客页面，点击链接即可查看
![](\images\2017-04-01-Hexo+GitHub构建个人博客\14.png)

# 七、上传博客源文件
上传博客源文件的目的是为了多地开发。比如你在家里创建了hexo博客，将生成的静态文件上传到GitHub，但你在公司也想写博客，最方便的方式就是用GitHub管理博客的源文件，这样在公司的电脑将博客项目克隆下来，再进行相应配置，便可进行博客的编写。现在将博客源文件发布到BlogSource仓库。
- 打开博客根目录，将.git文件夹删除
- 如下编辑.gitignore文件
{% codeblock lang:gitignore %}
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
themes/
{% endcodeblock %}
这样在上传源文件时就会忽略掉node依赖模块、生成的public静态文件、themes下的主题库等不需要上传的文件
- 打开Git Bash，创建一个空的git项目
{% codeblock lang:bash %}
git init
{% endcodeblock %}
- 输入命令将所有有改动的文件加入git缓冲区，因为是第一次发布，等于加入全部需要上传的文件
{% codeblock lang:bash %}
git add .
{% endcodeblock %}
- 查看git缓冲区文件状态，或查看简化信息
{% codeblock lang:bash %}
git status
git status -s
{% endcodeblock %}
- 第一次上传执行以下命令，git会将此仓库地址设置成默认的地址
{% codeblock lang:git %}
git remote add origin git@github.com:yourname/BlogSource.git
{% endcodeblock %}
在.git/config文件里可以修改默认仓库地址
{% codeblock %}
[remote "origin"]
	url = http://github.com/yourname/BlogSource
	fetch = +refs/heads/*:refs/remotes/origin/*
{% endcodeblock %}
- 提交git更改，m参数填写更新原因，如为空会失败
{% codeblock %}
git commit -m "..."
{% endcodeblock %}
- 推送更改。则成功将博客源文件上传至BlogSource仓库。
{% codeblock lang:bash %}
git push origin
{% endcodeblock %}
![](\images\2017-04-01-Hexo+GitHub构建个人博客\15.png)