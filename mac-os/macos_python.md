# MacOs里的python

## 起因
某天，脑子被门夹了，然后，我......  
```zsh
brew upgrade
```
再然后打开了一个陈年屎坑

忆往昔懵懂岁月，入手这台MacBook是2016年年中的时候，因此这里存在着一个使我迷惑的开发环境。
这波升级，让我的python直接升到了3.9，pip也出了点问题

[**もうやるしかない**](https://fanyi.baidu.com/#jp/zh/%E3%82%82%E3%81%86%E3%82%84%E3%82%8B%E3%81%97%E3%81%8B%E3%81%AA%E3%81%84)

## 开坑
> 动手删除多余python，开始整理开发环境

mac上安装python的方式有很多，常见的如下：
* 官网下载python安装包
* brew install
* anaconda

其实一开始我真不知道竟然有如此多的安装方法的，我只熟悉前两者。
由于我没有装过anaconda，所以我检查的范围主要是前两者的

对自己有一点的了解，相信自己不会骚操作，所以就没用```locate```和```find```，然后执行如下操作：
```zsh
$ which -a python
/usr/bin/python  # macos自带的，不能删的，系统依赖它，将来会被python3取代
$ which -a python3
/usr/local/bin/python3  # brew安装的
/usr/bin/python3 # 这是什么鬼？
```
运行这个/usr/bin/python3
```zsh
$ python3  # 这是用brew装的python3
Python 3.9.1 (default, Dec 16 2020, 01:57:40)
[Clang 12.0.0 (clang-1200.0.32.27)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
$ ./python3
Python 3.8.2 (default, Nov  4 2020, 21:23:28)
[Clang 12.0.0 (clang-1200.0.32.28)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys.path
['', '/Applications/Xcode.app/Contents/Developer/Library/Frameworks/Python3.framework/Versions/3.8/lib/python38.zip', '/Applications/Xcode.app/Contents/Developer/Library/Frameworks/Python3.framework/Versions/3.8/lib/python3.8', '/Applications/Xcode.app/Contents/Developer/Library/Frameworks/Python3.framework/Versions/3.8/lib/python3.8/lib-dynload', '/Applications/Xcode.app/Contents/Developer/Library/Frameworks/Python3.framework/Versions/3.8/lib/python3.8/site-packages']
>>>
```
很神奇的发现，这是xcode里面的python。
好了，顺手把xcode给删了吧，好久没用而且估计也很难再用它了，要的时候再装就好了。
再删东西的时候，建议用**AppClean**去删除，免得一堆残留。

虽然在网上检索时，也知道Xcode会引入python，但是这个位置还是和[网上说的][1]不同的。
[这篇文章][2]也提供了类似的思路。

## 填坑
>重新安装Command Lines Tool

由于删掉了Xcode，在我印象中好像Xcode里面有一些东西是被依赖的。
所幸在查阅网上资料时，已经知道了这个东西是**Command Lines Tool**了。
参考[教程][3]进行对应的处理，如下：
```zsh
$ gcc  # 安装之间运行gcc
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun

$ tree /Library/Developer/CommandLineTools  # 安装之前的文件目录
/Library/Developer/CommandLineTools
└── usr
    └── share
        └── man
            └── whatis

3 directories, 1 file

# 目录里肉眼可见的空虚

$ xcode-select --install  # 执行安装
xcode-select: note: install requested for command line developer tools
# 此处省略一波操作，下一步下一步就OK了 #

$ ll /Library/Developer/CommandLineTools  # 完成安装之后的文件目录
total 0
drwxr-xr-x  5 root  wheel   160B 12  4 06:12 Library
drwxr-xr-x  5 root  wheel   160B  1  4 18:09 SDKs
drwxr-xr-x  7 root  admin   224B  1  4 18:10 usr
$ ll /Library/Developer/CommandLineTools/usr  # 完成安装之后的文件目录
total 0
drwxr-xr-x  110 root  wheel   3.4K  1  4 18:07 bin
drwxr-xr-x    5 root  wheel   160B 12  4 06:15 include
drwxr-xr-x   16 root  wheel   512B 12  4 06:24 lib
drwxr-xr-x    6 root  admin   192B 12  4 06:23 libexec
drwxr-xr-x    8 root  admin   256B  1  4 18:10 share
$ gcc  # 安装完成之后，可以运行gcc命令了
clang: error: no input files
```
缺少bin文件夹，应该是需要重新安装Command Lines Tool了。
有兴趣的话，可以[深入浅出一下Command Lines Tool][4]。


## 进坑
> 偶然间发现我之前的理解有问题

安装完Command Lines Tool之后，我突然想看看之前在Xcode里面的python3怎样了。
万万没想到，惊喜就来了。
```zsh
$ python3  # brew安装的python3
Python 3.9.1 (default, Dec 16 2020, 01:57:40)
[Clang 12.0.0 (clang-1200.0.32.27)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import pip
>>> pip.__file__
'/usr/local/lib/python3.9/site-packages/pip/__init__.py'
>>>
$ /usr/bin/python3  # Command Lines Tool里面的python3
Python 3.8.2 (default, Nov  4 2020, 21:23:28)
[Clang 12.0.0 (clang-1200.0.32.28)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import pip
>>> pip.__file__
'/Library/Developer/CommandLineTools/Library/Frameworks/Python3.framework/Versions/3.8/lib/python3.8/site-packages/pip/__init__.py'
>>>

$ which -a pip3
/usr/local/bin/pip3  # brew
/usr/bin/pip3  # Command Lines Tool
```
好吧，看来python3已经成功加入macOS大礼包了。

之前我认为的"xcode里面的python3，其实就是现在Command Lines Tool里面的"，只是我把Command Lines Tool单独拿出来用，没有放在Xcode里面。

## 总结
目前在我的macOS里面存在着两个系统级别python程序（即给系统的其他程序提供依赖），python2就快被取代了。
```shell
$ ll /usr/bin/python*
lrwxr-xr-x  1 root  wheel    75B  1  1  2020 /usr/bin/python -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7
lrwxr-xr-x  1 root  wheel    82B  1  1  2020 /usr/bin/python-config -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7-config
lrwxr-xr-x  1 root  wheel    75B  1  1  2020 /usr/bin/python2 -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7
lrwxr-xr-x  1 root  wheel    75B  1  1  2020 /usr/bin/python2.7 -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7
lrwxr-xr-x  1 root  wheel    82B  1  1  2020 /usr/bin/python2.7-config -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7-config
-rwxr-xr-x  1 root  wheel   134K  1  1  2020 /usr/bin/python3
lrwxr-xr-x  1 root  wheel    76B  1  1  2020 /usr/bin/pythonw -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/pythonw2.7
lrwxr-xr-x  1 root  wheel    76B  1  1  2020 /usr/bin/pythonw2.7 -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/pythonw2.7

$ python2

WARNING: Python 2.7 is not recommended.
This version is included in macOS for compatibility with legacy software.
Future versions of macOS will not include Python 2.7.
Instead, it is recommended that you transition to using 'python3' from within Terminal.

Python 2.7.16 (default, Nov 23 2020, 08:01:20)
[GCC Apple LLVM 12.0.0 (clang-1200.0.30.4) [+internal-os, ptrauth-isa=sign+stri on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys.path
['', '/Library/Python/2.7/site-packages/beautifulsoup4-4.5.1-py2.7.egg', '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python27.zip', '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7', '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-darwin', '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-mac', '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-mac/lib-scriptpackages', '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-tk', '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-old', '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-dynload', '/Users/loli0con/Library/Python/2.7/lib/python/site-packages', '/Library/Python/2.7/site-packages', '/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python', '/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/PyObjC']
>>> import pip
>>> pip.__file__
'/Library/Python/2.7/site-packages/pip/__init__.pyc'
```

# 附录
设备里面存在多个python，用途各不相同，我的个人策略是封存好系统自带的，确保系统的正常运行。
将开发所需要的python使用HomeBrew来管理，此python可以作为一个基准模版，再构建一个需环境，供项目使用。

还有一个可能要注意的问题，假设某个文件执行的时候需要需要用到python解释器，那么究竟会调用哪个？
如果在命令行里制定了，那么情况就好说了。如果是用**shebang**来指定的，就需要考虑了。
常见的两种分写法```#!/usr/bin/python3```和```#!/usr/bin/env python3```，前者会直接调用macOS自带的python执行，
后者会从**PATH环境变量**中去寻找，这具备灵活性，但因此也有可能调用的不是系统自带的python。
有关shebang的介绍可以参考[这篇文章][5]

[1]: https://segmentfault.com/q/1010000022576249
[2]: https://www.cnblogs.com/jing99/p/13963048.html
[3]: https://segmentfault.com/a/1190000018045211
[4]: https://juejin.cn/post/6844904052271087624
[5]: https://www.jianshu.com/p/400c612381dd