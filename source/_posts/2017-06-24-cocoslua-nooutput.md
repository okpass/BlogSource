---
title: Cocos2dx-3.10 Lua工程输出控制台
date: 2017-06-24 09:49:39
tags: 
- Cocos2d-x
- Lua
categories: Cocos2dx
image: img/category/cocos2dx.png
---
Cocos2dx-3.10框架下建立的Lua工程分为预编译版和非预编译版，在Windows上调试时，预编译版默认带win32控制台输出，但禁用了调试输出，也就是调用print()函数没有输出；非预编译版默认不带win32控制台输出，要看输出只能开VS调试，比较麻烦。建议生成非预编译版工程，不然连语法错误都无法定位。
# 建立工程
```console
cocos new 工程名 -l lua
```
# 修改main.cpp
- 编辑工程目录下frameworks\runtime-src\proj.win32\main.cpp，加上打开控制台输出的代码
```c++
int APIENTRY _tWinMain(HINSTANCE hInstance,
	HINSTANCE hPrevInstance,
	LPTSTR    lpCmdLine,
	int       nCmdShow)
{
	UNREFERENCED_PARAMETER(hPrevInstance);
	UNREFERENCED_PARAMETER(lpCmdLine);

	//加入这段代码
	#ifdef USE_WIN32_CONSOLE  
	    AllocConsole();  
	    freopen("CONIN$", "r", stdin);  
	    freopen("CONOUT$", "w", stdout);  
	    freopen("CONOUT$", "w", stderr);  
	#endif 
	
    auto simulator = SimulatorWin::getInstance();
    return simulator->run();
}
```
# 修改VS工程配置
- 用VS打开工程，右击工程->配置属性->C/C++->预处理器，编辑预处理器定义
- 加入以下定义
{% asset_img 1.png %}
```
USE_WIN32_CONSOLE
```
- 编译运行工程，即可看到输出控制台
{% asset_img 2.png %}

# 打开F5重启功能
若创建的工程没有了F5重启功能，打开CC_CODE_IDE_DEBUG_SUPPORT开关即可
- 编辑工程目录下
frameworks\runtime-src\Classes\ide-support\CodeIDESupport.h，修改设置
```c
#define CC_CODE_IDE_DEBUG_SUPPORT 1
```
- 重新编译运行工程即可

