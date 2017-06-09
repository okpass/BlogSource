---
title: Lua绑定C++报错:ImportError:No module named yaml
date: 2017-04-05 17:26:47
tags: 
- Lua
- cocos2d-x
categories: cocos2dx
---

# 一、使用Lua绑定C++，执行tools/tolua/genbindings.py时报错ImportError: No module named yaml
## 解决办法
下载安装yaml模块:
http://pyyaml.org/download/pyyaml/PyYAML-3.10.win32-py2.7.exe

# 二、安装yaml时出现python version 2.7 required,which was not found in the registry
## 解决办法
新建register.py文件，复制以下内容，保存并运行
{% codeblock lang:py %}
#
# script to register Python 2.0 or later for use with win32all
# and other extensions that require Python registry settings
#
# written by Joakim Loew for Secret Labs AB / PythonWare
#
# source:
# http://www.pythonware.com/products/works/articles/regpy20.htm
#
# modified by Valentine Gogichashvili as described in http://www.mail-archive.com/distutils-sig@python.org/msg10512.html
 
import sys
 
from _winreg import *
 
# tweak as necessary
version = sys.version[:3]
installpath = sys.prefix
 
regpath = "SOFTWARE\\Python\\Pythoncore\\%s\\" % (version)
installkey = "InstallPath"
pythonkey = "PythonPath"
pythonpath = "%s;%s\\Lib\\;%s\\DLLs\\" % (
    installpath, installpath, installpath
)
 
def RegisterPy():
    try:
        reg = OpenKey(HKEY_CURRENT_USER, regpath)
    except EnvironmentError as e:
        try:
            reg = CreateKey(HKEY_CURRENT_USER, regpath)
            SetValue(reg, installkey, REG_SZ, installpath)
            SetValue(reg, pythonkey, REG_SZ, pythonpath)
            CloseKey(reg)
        except:
            print "*** Unable to register!"
            return
        print "--- Python", version, "is now registered!"
        return
    if (QueryValue(reg, installkey) == installpath and
        QueryValue(reg, pythonkey) == pythonpath):
        CloseKey(reg)
        print "=== Python", version, "is already registered!"
        return
    CloseKey(reg)
    print "*** Unable to register!"
    print "*** You probably have another Python installation!"
 
if __name__ == "__main__":
    RegisterPy()
{% endcodeblock %}
(代码来自[http://www.cnblogs.com/thinksasa/archive/2013/08/26/3283695.html](http://www.cnblogs.com/thinksasa/archive/2013/08/26/3283695.html))

运行成功则成功恢复注册表信息

# 三、安装yaml后再运行genbindings.py，报错ImportError: No module named Cheetah.Template
## 解决办法
下载Cheetah库：[http://github.com/cheetahtemplate/cheetah/zipball/v2.4.0](http://github.com/cheetahtemplate/cheetah/zipball/v2.4.0)
- 将下载完的zip文件解压到任意目录，例如C:\Python27\Lib\cheetahtemplate-cheetah-7b1c2ad\
- 命令行依次执行以下命令
```
C:
cd \Python27\Lib\cheetahtemplate-cheetah-7b1c2ad>
python setup.py build
python setup.py install
```

# 四、执行genbindings.py报错LibclangError: [Error 193] %1 不是有效的 Win32. To provide e Config.set_library_path() or Config.
## 解决办法
按照前面两步安装的yaml和Cheetah库都是win32版本的，出现这个错误是因为python不是win32版本，全部都用win32版本即可
下载python win32版本[http://www.python.org/ftp/python/2.7.3/python-2.7.3.msi](http://www.python.org/ftp/python/2.7.3/python-2.7.3.msi)
安装后重新运行即可

PS.以上下载地址和不同平台的安装方法在Python根目录下的README有说明。