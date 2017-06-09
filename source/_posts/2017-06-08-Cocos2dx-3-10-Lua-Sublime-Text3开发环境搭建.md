---
title: Cocos2dx-Lua 3.10 + Sublime Text3开发环境搭建
date: 2017-06-08 16:43:42
tags: 
- cocos2d-x
- Lua
categories: cocos2dx
---

本篇教程讲述如何基于sublime text3编辑器配置cocos2dx-lua的开发环境
最近到了新公司，开始接触cocos2dx lua项目，用的是公司一位大神写的框架，打算写篇教程讲讲怎么配置开发环境
网上的教程都比较零散，或是版本太旧，只有对应quick框架的教程，或是不能很好运行。开发cocos lua项目可以选择vs作为编辑器，可以打断点调试，在复杂情况下有用。如果只是像我一样开发游戏逻辑，推荐Sublime Text3作为编辑器，优点轻便，撸代码爽，不像vs一样启动都要半天

# 一、创建cocos lua项目
- 下载cocos2dx-3.10 + Sublime Text3
自行下载安装即可，cocos框架安装教程网上很多，不再赘述
- 使用命令行创建lua工程
在F:盘下运行
{% codeblock lang:cmd %}
cocos new TestProject -l lua
{% endcodeblock %}
则成功创建lua项目
- 编译项目
进入项目根目录，执行
{% codeblock lang:cmd %}
cocos run -p win32
{% endcodeblock %}
开始编译项目，等待很久之后运行成功，则自动生成simulator文件夹

# 二、cocos目录结构介绍
- 先来看一下cocos2dx-3.10创建的目录结构
![](\images\2017-06-08-Cocos2dx-3-10-Lua-Sublime-Text3开发环境搭建\1.png)
重点关注src和res文件夹，src存放了我们编写的lua代码，res存放了游戏资源，如果用VS开发，在运行时VS会把src和res中改动过的文件复制到simulator/win32目录下对应文件夹里（包括新增，修改，删除）
- 再来看win32目录
![](\images\2017-06-08-Cocos2dx-3-10-Lua-Sublime-Text3开发环境搭建\2.png)
simulator\win32目录下是游戏运行默认的工作目录，游戏会读取此目录下的src和res文件，而不是根目录下的
所以我们此次教程的目的就是要搭建一个自动读取根目录下src,res文件的环境，因为simulator/win32下的资源是临时的，很有可能会被覆盖，而我们一般编写的都是根目录下的文件，所以才读取根目录下的。当然，你可以直接复制simulator/win32下除了src,res外的文件到根目录下，或者直接修改win32目录下的src和res，但这是很危险的，不仅和项目原有结构不同，难以使用svn工具同步代码，而且转而使用VS调试时会被覆盖，所以不建议采用这些办法。

# 三、配置sublime开发环境

&emsp;&emsp;这里介绍两种配置方法
&emsp;&emsp;方法1.新增编译系统
&emsp;&emsp;方法2.使用快捷方式

- 新增编译系统
1. 点击Sublime菜单工具->编译系统->新编译系统，新建编译规则文件
2. 复制以下配置
{% codeblock lang:json %}
{
    "cmd": ["python", "-u", "Sublime目录/Data/Packages/User/run.py"],
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.lua"
}
{% endcodeblock %}
3. 保存配置文件到Sublime目录/Data/Packages/User/
4. 在同一目录下新建run.py，复制以下代码
{% codeblock lang:python %}
import ctypes

import os,sys

handler = None
operator = "open"
fpath = "F:\TestProject\simulator\win32\TestProject.exe"
param = "-workdir \"F:\TestProject\""
dirpath = "F:\TestProject"
ncmd = 1
shell32 = ctypes.windll.LoadLibrary("shell32.dll")
shell32.ShellExecuteA(handler,operator,fpath,param,dirpath,ncmd)
{% endcodeblock %}

5. 选择工具->编译系统，勾选刚刚保存的编译方式，按F7或者ctrl+B即可运行游戏

 当按下F7时，Sublime执行build命令，根据编译规则运行cmd对应文件run.py
run.py执行TestProject.exe，并且加入参数-workdir，指定工作目录为项目根目录，这样游戏就会读取根目录下的src和res，不需要再手动复制到win32文件夹里

- 使用快捷方式
1. 在项目根目录下创建run.bat文件，复制以下代码
{% codeblock lang:cmd %}
start ./simulator/win32/TestProject.exe -workdir ./
{% endcodeblock %}

2. 在桌面新建此批处理文件的快捷方式，右击属性->快捷方式，将快捷键设为ctrl+2等，则可使用快捷键快速打开游戏（快捷键只在桌面有效）

3. 或者直接在桌面创建TestProject.exe的快捷方式，并右击属性->快捷方式，在目标一栏最后加上参数
{% codeblock lang:cmd %}
-workdir F:/TestProject
{% endcodeblock %}
即可快速调试游戏，免去复制src和res文件夹的步骤