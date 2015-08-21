---
layout: post
title: 将Python脚本打包成可执行文件
category : 工具 
tags : []
date: 2015/08/21  18:00  +0800
--- 

Python是一个脚本语言，被解释器解释执行。它的发布方式：

- .py文件：对于开源项目或者源码没那么重要的，直接提供源码，需要使用者自行安装Python并且安装依赖的各种库。（Python官方的各种安装包就是这样做的）

- .pyc文件：有些公司或个人因为机密或者各种原因，不愿意源码被运行者看到，可以使用pyc文件发布，pyc文件是Python解释器可以识别的二进制码，故发布后也是跨平台的，需要使用者安装相应版本的Python和依赖库。

- 可执行文件：对于非码农用户或者一些小白用户，你让他装个Python同时还要折腾一堆依赖库，那简直是个灾难。对于此类用户，最简单的方式就是提供一个可执行文件，只需要把用法告诉Ta即可。

本文主要就是介绍最后一种方式，.py和.pyc都比较简单，Python本身就可以搞定。将Python脚本打包成可执行文件有多种方式，本文重点介绍PyInstaller，其它仅作比较和参考。

<!--more-->

### Freezing Your Code


| Solution | Windows | Linux | OS X | Python 3 | License | One-file mode | Zipfile import | Eggs | pkg_resources support |
| -------- | ------- | ----- | ---- | -------- | ------- | ------------- | ------------   | ---- | --------------------- |
| bbFreeze |  yes    |  yes  |  yes |  no      |  MIT    |   no          | yes            |  yes |  yes                  |
| py2exe   |  yes    |	no   |  no  |  yes     |  MIT    |   yes         | yes            |  no  |  no                   |
| pyInstaller | yes  |  yes  |  yes |  no      |  GPL    |	yes|	no|	yes|	no|
| cx_Freeze|	yes|	yes|	yes|	yes|	PSF|	no|	yes|	yes|	no|
| py2app|	no|	no|	yes|	yes|	MIT|	no|	yes|	yes|	yes|


### 安装PyInstaller

### 使用PyInstaller

### PyInstaller的原理简介

### 参考资料

![](http://docs.python-guide.org/en/latest/shipping/freezing/)
![](http://pythonhosted.org/PyInstaller)