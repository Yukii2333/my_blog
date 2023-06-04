---
title: Linux--软件包
date: 2023-06-01 10:05:30
categories: Linux
tags:
 - Linux
description:
cover:
---

# 软件包管理器--yum

## 软件包

* 在Linux下安装软件，一个通常的方法是下载到程序的源代码，并进行编译，得到可执行程序。
  * 但是该操作较为繁琐，且门槛过高，于是有一些人把常用软件提前编译好，做成软件包（类似Windows下的安装程序），放在一个服务器上，通过软件包管理器可以很方便的获取到这个编译好的软件包，直接安装。
* 软件包和软件包管理器就好比“App”和“应用商店”这样的关系。
* `yum`(Yellow dog Updater, Modified)是Linux下非常常用的一种软件包管理器，主要应用在Fedora、RedHat、Centos等发行版本上。

## rzsz

### 功能

这个工具用于Windows机器与远端的Linux机器通过Xshell传输文件

### 安装

~~~
yum install lrzsz
~~~

不论是此处还是后续，都要保证yum所操作的主机（虚拟机）网络畅通，可以通过ping指令验证

~~~
ping www.baidu.com
~~~

### 查看软件包

通过`yum list`命令可以罗列出当前一共有哪些软件包，由于软件包数目非常多，可以使用`grep`命令筛选出所需要的包

![直接使用](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306021043567.png "直接使用")

![grep筛选](C:\Users\27156\AppData\Roaming\Typora\typora-user-images\image-20230602104522258.png "grep筛选")

#### 信息内容

* 软件包名称：主板本号.次版本号.源程序发型号-软件包发型号.主机平台.cpu架构
* x86_64  表示64位系统的安装包，i686 表示32位系统的安装包，选择软件包时要和系统匹配
* el7  表示操作系统发行版的版本，即 “centos7/redhat7
* 最后一列，base表示“软件源”名称，类似于“xx应用商店”

### 注意

* 安装软件时由于要向系统目录写入一些内容，一般需要`sudo`或切换到root账户下才能完成
* yum安装软件只能一个一个依次安装，在安装过程中再次安装其他软件会报错
* yum报错时，一般百度就能查到原因

### 如何卸载软件

依然是yum指令

~~~
yum remove xxx(软件包名)
~~~

使用该指令也需要root权限

# Linux软件开发工具

## Linux编辑器--vim

### 基本概念

vim拥有三种模式，即**命令模式、插入模式、底行模式**

**命令模式(Normal mode)**

控制屏幕光标的移动，字符、文字、或行的删除、移动、复制等操作

**插入模式(Insert mode)**

只有在该模式下，才能做文字的输入，也就是使用最为频繁的编辑模式

**底行模式(Last line mode)**

文件退出或者保存都在次模式下进行

**模式切换**

初始进入文件都是处于命令模式，在命令模式下**进入插入模式按下`i`**，**进入底行模式按下 `:`(即`sheft`+`;`)**

在插入模式或底行模式下，**退回命令模式按下`ESC`**

### 基本操作

* **进入vim**

~~~
vim filename
~~~

![进入vim](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306021120962.png "进入vim")

![命令模式](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306021122752.png "命令模式")

* 切换**插入模式**

~~~
i
~~~

![插入模式](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306021123128.png "插入模式")

* 切换**底行模式**

**首先要处于命令模式**

~~~
:(shift + ;)
~~~

该模式下输入**wq**保存并退出

![底行模式](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306021126763.png "底行模式")



**注意：**vim可以直接进入文件，也可以后面跟一个不存在的文件名，在底行模式下保存并退出后就会将改文件创建至当前目录

### 命令模式命令集

#### 移动光标

* 可以直接使用键盘上的方向键，但是正规的使用方式应该是使用`H J K L`四个键，其依次表示**左、下、上、右**，控制光标移动一格
* 按`G`：移动光标至内容最后
* 按`g g`：移动光标至内容最前
* 按`n(数字)`+`G`：定位光标至内容第n行开头
* 按`$`即`shift`+`4`：移动光标至光标所在行的末尾
* 按`^`即`shift`+`6`：移动光标至光标所在行的行首
* 按`w`：光标跳至下个单词（字）的开头
* 按`b`：光标跳至上个单词（字）的开头
* 按下`y y`：复制光标所在行(`n(数字)`+`y y`复制光标行在内的向下n行)
* 按下`p`：粘贴到光标所在行的下一行(`n(数字)`+`p`粘贴n行)
* 按下`u`：撤销上一条操作
* 按下`ctrl`+`r`：撤销`u(撤销)`操作
* 按下`d d`：剪切光标所在行（也算是一种删除光标所在行，`n(数字)`+`d d`剪切（删除）光标在内的向下n行）
* 按下`~`：快速大小写转换
* 按下`r`：对光标字符进行替换（`n(数字)`+`r`连续替换）
* 按下`R`：进行批量替换（进入**替换模式，按`ESC`退出**）
* 按下`x`：对光标字符进行删除（`n(数字)`+`x`批量删除）

#### 多文件模式

底行模式下，输入`vs filename`打开多个文件编辑窗口

按下`ctrl`+`w w`切换光标所在文件

![效果图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306021201049.png "效果图")

## vim配置

vim的默认配置会和图中看起来不同，网上有很多配置教程甚至是一键安装配置的内容，就不多赘述了

# 关于sudo

`sudo`命令用于权限提升，但是在之前的使用中会发现并不能使用该命令，原因是新创建的用户是不在信任名单内的，也从一方面反映了为什么用该命令输入的是用户的密码，而不是root账户密码

## 配置方法

在root账户下且位于图示目录下，执行

```
vim etc/sudoers
```

![目录](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306040948953.png "目录")

在打开的文件的百来行的位置（底行模式下输入`set nu`显示行号），会有图示内容（默认只有一个root）

![允许sudo用户](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306040952248.png "允许sudo用户")

将root那一行复制到下一行然后将root改为需要的用户名再保存并退出即可（不能保存在`wq`后加一个`!`，表示强制执行），之后就可以在已添加的用户中使用`sudo`命令

# gcc/g++编译器

gcc用于编译C语言，g++用于编译C++（由于C++兼容C，因此也可以用g++编译C）

## 语法

**gcc [选项] filename [选项] [目标文件]**

## 编译过程

*由于已经提过，此处就略提一下*

**预处理**

* 去注释
* 头文件展开
* 条件编译
* 宏替换

**编译**

* 编译器查看代码规范性
* 将代码翻译为汇编语言

**汇编**

* 将汇编语言转换为二进制代码

**连接**

* 连接所需的库

## 分步执行

在Linux下，可以通过选项来分步进行

**预处理**

~~~
gcc -E xxx.c -o xxx.i
~~~

`-E`后跟需要编译的文件名，该选项指仅进行预处理

`-o`后跟目标文件名，该选项表示自定义文件名

![预处理](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306041037077.png "预处理")

**编译**

~~~
gcc -S xxx.i -o xxx.s
~~~

`-S`后跟源文件名，表示仅对预处理后的文件进行编译

![编译](C:\Users\27156\AppData\Roaming\Typora\typora-user-images\image-20230604104157027.png "编译")



**汇编**

~~~
gcc -c xxx.s -o xxx.o
~~~

`-c`后跟源文件，表示对编译后的文件仅进行汇编

![汇编](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306041044162.png "汇编")

**连接**

~~~
gcc xxx.o -o xxx.out
~~~

最后一步，目标文件名字不自定义默认是`xxx.out`，后缀可以随意更改

![连接](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306041048984.png "连接")



## 运行程序

如图

![运行](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306041050583.png "运行")



## 函数库

在C程序中，虽然我们并没有定义`printf`函数，并且在预编译包含的头文件中也只有函数的声名。但是，系统将头文件的函数实现放入了名为`lib.so.6`的库文件中了，在没有特殊指定时，gcc会到系统默认的搜索路径`/user/lib`下进行查找，也就是链接到`lib.so.6`函数库中，这样函数的声名与定义就都有了。

### 分类

#### 静态库

* 静态库是指编译链接时，把库文件的代码全部加入可执行文件中，因此生成的文件比较大，但在运行时就不再需要库文件了，后缀名一般为`.a`

#### 动态库

* 动态库与之相反，在编译链接时，并没有把库文件的代码加入可执行文件内，而是在程序运行时由运行时链接文件加载库，这样可以节省系统的开销。动态库后缀名一般为`.so`

### 注意

gcc默认生成的可执行文件使用的是动态库，可以在生成可执行文件时，在命令后接`-static`使用静态库链接

![使用静态库](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306041106333.png "使用静态库")

前面说到静态库的开销相比于动态库更大，是因为静态库将所需要的内容全部加入了可执行文件中，特别是大量重复的调用

![开销](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202306041108089.png "开销")

如果静态库缺失，使用改选项会报错；但是如果是动态库缺失，使用gcc生成可执行文件，只要动态库没问题，依然能生成。是因为默认只是优先使用动态库，动态库不能使用就使用静态库













