---
layout: post
title: 每周一荐：差异利器Beyond Compare
category : 每周一荐
tags : [工具, BeyondCompare]
date: 2012-11-01 14:51 +0800
---

前一段时间，介绍过用Total Commander来完成文件夹同步的时候，一位朋友留言推荐了Beyond Compare——一个强大的超越了文件差异比较的工具。Beyond Compare对于提升工作效率，绝对是一个利器。

单独的文件差异工具不胜枚举，如：WinMerge、WinDiff、Diffuse等，还有Unix/Linux下犀利的命令行工具：diff，一般源码的补丁就是靠它搞定的，先用diff差异一下，再patch一下就OK了；版本控制软件一般默认也都提供了自己的差异和合并软件（如：TortoiseSVN的TotoiseMerge）。

![Beyond Compare](/assets/img/2012-11-01-1.jpg)

Beyond Compare正如其名，“超越了比较”，它有三个核心功能：

* 差异比较  
* 文件夹同步  
* 文本合并  

### 1. 差异

支持多种文件格式的差异，对于我们“程序猿”来说，最重要的要数文本文件差异和语法高亮了。对于策划或其它经常使用Excel的来说，数据文件差异是个非常强大的功能。

#### 1.1  文件夹的差异

选择Folder Compare，选择要进行比较的两个目录。蓝色高亮的部分就是比较的另一个目录缺失的，红色高亮的就是两边都存在，但存在差异的。工具栏提供了许多选项，使用的过程中可以慢慢摸索。

![Beyond Compare](/assets/img/2012-11-01-2.jpg)

#### 1.2 文本文件的差异

选择“Text Compare”，选中两个文本文件。如果是代码，同时可以进行语法高亮显示，其它差异软件一般都会有此功能。

![Beyond Compare](/assets/img/2012-11-01-3.jpg)

#### 1.3 数据文件差异（如：Excel文件）

选择“Data Compare”，选择要比较的两个数据文件，可以是Excel文件，也可以CSV等数据文件。这个功能对于经常做数据的朋友来说，是非常有用的。

![Beyond Compare](/assets/img/2012-11-01-4.jpg)

#### 1.4 16进制数据差异

选择“Hex Compare”，选择要比较的两个二进制文件。该功能会对选择的两个文件，每个字节进行比较。差异的地方高亮显示。

#### 1.5 图像的差异

选择“Picture Compare”，选择要比较的两个图像文件，然后中间一个窗口就会把两张图片差异的部分显示出来，可以通过拖动图片或者选择工具栏相关选项做调整。

![Beyond Compare](/assets/img/2012-11-01-5.jpg)

### 2. 文件夹同步

选择“Folder Sync”，选择要同步的两个目录，点击“Sync”即可。Total Commander也有类似的功能，该功能对于经常做文件备份的朋友会非常有用，当备份之间存在差异的时候，文件夹只需要同步一下，万事OK，免得丢失文件或者一个个去对比浪费时间。

![Beyond Compare](/assets/img/2012-11-01-6.jpg)

### 3. 文本合并

文本文件是可编辑的，在差异的基础上对两个文件进行编辑合并，对于代码版本控制、解决冲突非常有用。

### 小结

BeyondCompare是一款非常不错的提高工作效率的利器，推荐给大家使用。（文件差异与合并、目录同步这些功能有相应的开源的可以代替Beyond Compare）。

