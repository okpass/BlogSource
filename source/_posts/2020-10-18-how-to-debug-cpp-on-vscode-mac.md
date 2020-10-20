---
title: Mac平台上用VSCode调试C++代码
date: 2020-10-18 14:03:34
tags:
- C++
categories: C++
image: img/category/cpp.png
keywords: [C++ debug, VSCode debug, Mac C++ debug, C++ 调试]
description: 本文描述了如何在MAC平台上用VScode调试C++代码
---

在MAC平台上，如果想直接运行C++代码进行测试，可以直接在控制台运行命令编译cpp文件
``` sh
g++ -o test ./test.cpp
```
其中-o参数是输出的可执行文件的名字，编译后在同一目录会生成test可执行文件，再执行test文件即可运行
``` sh
./test
```

但这样运行是无法进行断点调试的，我们可以借用Xcode的LLDB调试器来在VSCode上调试。

## 1. 建立task配置

打开VSCode，用快捷键cmd+shift+p打开命令框，输入Configure Task，点击弹出的选项
{% asset_img 1.png %}
然后选择 使用模板创建task.json文件
{% asset_img 2.png %}

这样会在./vscode目录下生成task.json文件，再将task.json进行如下配置
``` json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build test",
            "type": "shell",
            "command": "clang++",
            "args": [
                "test.cpp",
                "-o",
                "test.out",
                "-g"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```

讲解一下其中的参数
- label：是任务的用户界面标签，即任务名称
- type：定义任务是被作为进程运行还是在 shell 中作为命令运行。调试C++文件需要用命令行编译，配置为shell，任务执行时会先打开shell执行后续命令
- command：要执行的命令。可以是外部程序或 shell 命令。我们要借助命令行执行clang++ test.cpp -o test.out -g，所以这里填clang++
- args：执行clang++命令的参数列表
- group：定义此任务属于的执行组。它支持 "build" 以将其添加到生成组，也支持 "test" 以将其添加到测试组。这里选择分组到build，为默认分组

添加好任务配置后按cmd+shift+b执行任务，会在项目目录生成test.out和test.out.dSYM文件，其中test.out.dSYM文件包含了代码调试信息的目标文件。

## 2. launch.json
{% asset_img 3.png %}
点击左侧工具栏上的调试按钮，再点击添加配置，选择C/C++ (lldb)启动，在.vscode/lauch.json文件中添加以下配置
``` json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(lldb) 启动",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/test.out",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "lldb"
        }
    ]
}
```

参数讲解：
- name: 配置名称；在启动配置下拉菜单中显示。
- type: 配置类型。默认为cppdbg，即C++调试
- request: 请求配置类型。这里需要启动可执行文件，所以为“lauch”
- program: 程序可执行文件的完整路径。
- args: 传递给程序的命令行参数。
- stopAtEntry: 可选参数。如果为 true，则调试程序应在目标的入口点处停止。如果传递了 processId，则不起任何作用。
- cwd: 目标的工作目录
- environment: 要添加到程序环境的环境变量。示例: [ { "name": "squid", "value": "clam" } ]。
- externalConsole: 如果为 true，则为调试对象启动控制台。如果为 false，它在 Linux 和 Windows 上会显示在集成控制台中。
- MIMode: 指示 MIDebugEngine 要连接到的控制台调试程序。允许的值为 "gdb"、"lldb"。

配置完毕点击开始调试，即可断点调试C++代码
{% asset_img 4.png %}

总结一下调试步骤：写好cpp后将cpp所在的文件夹拖进vscode，vscode会为此文件夹新建工作区窗口，按cmd+shift+b执行编译任务，编译成功后点击开始调试即可