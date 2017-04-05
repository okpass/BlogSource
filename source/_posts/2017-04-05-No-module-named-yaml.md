---
title: Lua绑定C++报错:ImportError:No module named yaml
date: 2017-04-05 17:26:47
tags: 
- Lua
- cocos2d-x-Lua
---

使用Lua绑定C++，执行tools/tolua/genbindings.py时报错
{% codeblock lang:cmd %}
C:\Cocos\Cocos2d-x\cocos2d-x-3.10\tools\tolua>genbindings.py
PYTHON_BIN not defined, use current python.
generating userconf.ini...
Generating bindings for cocos2dx_hello...
Traceback (most recent call last):
  File "C:\Cocos\Cocos2d-x\cocos2d-x-3.10\tools\bindings-generator/generator.py", line 11, in <module>
    import yaml
ImportError: No module named yaml
---------------------------------
Generating lua bindings fails.
---------------------------------
{% endcodeblock %}

##解决办法
下载yaml模块:
http://pyyaml.org/download/pyyaml/PyYAML-3.10.win32-py2.7.exe

#安装时出现
python version 2.7 required,which was not found in the registry
##解决办法
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

运行成功则成功恢复注册表信息，此时再运行genbindings.py