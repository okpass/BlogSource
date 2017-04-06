---
title: quick-cocos2d-x-3.3 genbindings生成lua绑定
date: 2017-04-06 16:40:59
tags:
- Lua
- cocos2d-x
---

cocos2d框架提供了genbindings.py，将Lua函数与C++类绑定，即可在Lua中调用C++类。
写本教程的时候quick框架早已并入cocos2d-x，cocos2dx版本已经是3.14，触控好像也早已放弃了对Cocos Code IDE的支持，团队完全转向了Cocos Creator这个新宠的开发。但鉴于网上的教程基本都是三四年前的，用新版本框架调试难度重重，本文选择用quick-cocos2dx-3.3 + Cocos Code IDE 1.2 + cocos2d-x-3.4来作Lua绑定工具使用的说明。只针对Windows操作说明。

# 一、下载安装quick-cocos
- 下载地址: [http://pan.baidu.com/s/1dVYgu](http://pan.baidu.com/s/1dVYgu)
- 解压到cocos目录，例如C:\Cocos\quick-3.3
- 双击运行setup_win.bat即可

# 二、下载安装Cocos Code IDE
- Cocos Code IDE 1.2.0 Win32 下载地址：
http://www.cocos2d-x.org/filedown/cocos-code-ide-win32-1.2.0.exe
- Cocos Code IDE 1.2.0 Win64 下载地址：
http://www.cocos2d-x.org/filedown/cocos-code-ide-win64-1.2.0.exe
- Cocos Code IDE 1.2.0 Mac 下载地址：
http://www.cocos2d-x.org/filedown/cocos-code-ide-mac64-1.2.0.dmg
## Cocos Code IDE配置方法
http://jingyan.baidu.com/article/19020a0a06c863529c28426a.html

# 三、下载cocos2d-x-3.4
- 下载地址
http://cocostudio.download.appget.cn/Cocos2D-X/cocos2d-x-3.4.zip
- 解压到与quick-3.3相同目录，例如C:\Cocos\cocos2d-x-3.4
- 命令行cd到该目录下，运行
``` 
python setup.py
```
配置相应SDK、NDK目录即可

# 四、新建lua工程
- 命令行输入
```
cd c:/Cocos/quick-3.3/quick/bin/
create_project -o F:/CocosProjects/quickTest
```
等待工程创建完毕
