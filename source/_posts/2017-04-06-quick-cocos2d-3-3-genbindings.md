---
title: 教你用quick-cocos2d-x-3.3 genbindings生成lua绑定
date: 2017-04-06 16:40:59
tags:
- Lua
- Cocos2d-x
categories: Cocos2dx
image: img/category/cocos2dx.png
keywords: [genbindings.py, quick-cocos2d-x]
descprition: quick-cocos2d-x genbindings生成lua绑定的方法介绍
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
{% codeblock lang:python %} 
python setup.py
{% endcodeblock %}
配置相应SDK、NDK目录即可

# 四、新建lua工程
- 命令行输入
{% codeblock lang:cmd %} 
cd c:/Cocos/quick-3.3/quick/bin/
create_project -o F:/CocosProjects/quickTest
{% endcodeblock %}
等待工程创建完毕
- 打开工程目录quickTest\frameworks\cocos2d-x\cocos，新建文件夹my
- 打开文件夹my，新建文件Hello.h
- 编辑Hello.h，写入以下内容
{% codeblock lang:cpp %}
#ifndef __HELLO__
#define __HELLO__

#include "cocos2d.h"
USING_NS_CC;

namespace cocos2d{
	class Hello : public Ref
	{
		Hello(){};
		~Hello(){};
		virtual bool init(){
			return true;
		};
		void sayHello(){
			CCLOG("Hello cocos2d Lua");
		};
	};
}
#endif
{% endcodeblock %}
我们新建了一个Hello类，一个sayHello()方法输出"Hello cocos2d Lua"，这个C++类将会在Lua里调用。
- 打开工程目录quickTest\frameworks\cocos2d-x\tools\tolua
- 复制一份cocos2dx.ini，命名为cocos2dx_hello.ini，按以下内容修改cocos2dx_hello.ini
{% codeblock lang:ini %}
[cocos2dx_hello]
# the prefix to be added to the generated functions. You might or might not use this in your own
# templates
prefix = cocos2dx_hello

# create a target namespace (in javascript, this would create some code like the equiv. to `ns = ns || {}`)
# all classes will be embedded in that namespace
target_namespace = cc

android_headers = -I%(androidndkdir)s/platforms/android-14/arch-arm/usr/include -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.7/libs/armeabi-v7a/include -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.7/include -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.8/libs/armeabi-v7a/include -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.8/include
android_flags = -D_SIZE_T_DEFINED_ 

clang_headers = -I%(clangllvmdir)s/lib/clang/3.3/include 
clang_flags = -nostdinc -x c++ -std=c++11 -U __SSE__

cocos_headers = -I%(cocosdir)s/cocos -I%(cocosdir)s/cocos/platform/android -I%(cocosdir)s/cocos/editor-support -I%(cocosdir)s/external
cocos_flags = -DANDROID

cxxgenerator_headers = 

# extra arguments for clang
extra_arguments = %(android_headers)s %(clang_headers)s %(cxxgenerator_headers)s %(cocos_headers)s %(android_flags)s %(clang_flags)s %(cocos_flags)s %(extra_flags)s 

# what headers to parse
headers = %(cocosdir)s/cocos/my/Hello.h

# what classes to produce code for. You can use regular expressions here. When testing the regular
# expression, it will be enclosed in "^$", like this: "^Menu*$".
classes = Hello

# what should we skip? in the format ClassName::[function function]
# ClassName is a regular expression, but will be used like this: "^ClassName$" functions are also
# regular expressions, they will not be surrounded by "^$". If you want to skip a whole class, just
# add a single "*" as functions. See bellow for several examples. A special class name is "*", which
# will apply to all class names. This is a convenience wildcard to be able to skip similar named
# functions from all classes.

skip = 

rename_functions = 

rename_classes = 

# for all class names, should we remove something when registering in the target VM?
remove_prefix = 

# classes for which there will be no "parent" lookup
classes_have_no_parents = 

# base classes which will be skipped when their sub-classes found them.
base_classes_to_skip = Ref

# classes that create no constructor
# Set is special and we will use a hand-written constructor
abstract_classes = 

# Determining whether to use script object(js object) to control the lifecycle of native(cpp) object or the other way around. Supported values are 'yes' or 'no'.
script_control_cpp = no
{% endcodeblock %}
- 复制一份genbindings.py，命名为genbindings_hello.py，修改以下部分：
{% codeblock lang:python %}
cmd_args = {
            'cocos2dx_hello.ini' : ('cocos2dx_hello', 'lua_cocos2dx_hello_auto')
            }
{% endcodeblock %}
生成的cpp和hpp文件在以下目录
quickTest\frameworks\cocos2d-x\cocos\scripting\lua-bindings\auto
{% asset_img 1.png %}