---
title: 初识Linux
date: 2023-05-14 18:55:17
categories: Linux
tags:
 - Linux
description: Note about Linux
cover:
---

# 基础知识

## 起源

Linux是一个**操作系统**，而且是**开源**的。

于1991年，由林纳斯·托瓦兹首次发布，Linux其名字含义为**Linux is not unix**。因为当时另外几款操作系统都是收费的，因此这样一款免费且开源的操作系统逐渐开始流行，越来越多的专业人员加入完善优化Linux操作系统的行列中，最终演变到现在的情况。

## 应用场景

* 互联网公司的服务器后端
* 智能设备中使用（手机、智能电视...）
* 嵌入式场景

## 版本

**商业化发行版本：**

​	CentOS、Ubuntu、Redhat、Slackware......

**技术版本：**

​	即Linux内核（原代码）



---



# 基本操作

## 搭建与连接

* 购买云服务器、虚拟机或者搭建服务器其中一种都可
* 在选择系统时，选择CentOS 7.x的系统（更符合将来工作环境）
* 然后通过[Xshell](https://www.netsarang.com/en/xshell/)通过云服务器的公网ip进行连接（虚拟机或者服务器也有对应的ip）

~~~
//用户名是购买云服务器时设置的
//公网IP也在购买的服务器那里可以看见


ssh 用户名@公网IP

//然后会输入密码
~~~

![指令](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516092601.png "指令")

输入密码时，Linux处于安全考虑是不会显示密码的，甚至不会显示你已经输入了几个字符，当显示类似如下的语句，说明已经成功连接上了

![](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516092543.png)

## 基本指令

### ls

**语法：** ls [选择] [目录或者文件]

**功能：**对于目录，该命令列出该目录下的所有子目录与文件。对于文件，讲列出文件名以及其他信息

**常用选项：**

* -a 列出目录下的所有文件，包括以 . 开头的隐含文件
* -d 将目录像文件一样显示，而不是显示其下的文件

* -l 列出文件的详细信息

* -F 在每个文件名后附上一个字符以说明该文件的类型，"*"表示可执行文件；"/"表示目录；"@"表示符号链接...

  ...

（只对常用的进行罗列）

![效果图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516092512.png "效果图")



### pwd

**语法：**pwd

**功能：**显示用户当前所在目录

**常用选项：**

​	无

![效果图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516093240.png "效果图")



### cd

**语法：**cd 目录名

**功能：**改变工作目录。将当前工作目录改变到指定的目录下。

**举例：**

~~~
cd ..   			返回上级目录
cd /home/xxx/xxx    进入指定目录（绝对路径）
cd ../xxx/ 			进入指定目录（相对路径）
cd ~				进入用户家目录
cd -				进入最近访问的目录
~~~

因为我们使用的Linux没有像Windows那样的图形化界面（当然也有有图形化界面的Linux和没有图形化界面的Windows），cd就相当于实现了在我们电脑中双击目录进入、返回上一级等功能



### touch

**语法：**touch [选择] 文件

**功能：**touch的命令参数可以更改文档或目录的日期时间，包括存取时间和更改时间，还可以创建一个不存在的文件

**常用选项：**

* -a 或--time=atime或--time=access或--time=use， 只更改存取时间
* -c 或--no--create 不建立任何文档
* -d 使用指定的日期时间，而非现在的时间

​	...

![效果图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516094641.png "效果图")



### mkdir

**语法：**mkdir [选择] dirname

**功能：**在当前目录下创建一个名为 "dirname" 的目录

**常用选项：**

* -p （--parents）可以是一个路径名称，若此时不存在该路径中的某些目录，则加上此选项后会自动创建，即一次创立多个目录

![效果图1](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516095138.png "效果图1")

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305181954013.png" alt="效果图2" title="效果图2" style="zoom:80%;" />

关于tree指令，需要进行安装，在root账户下（或者root权限下）执行

~~~
yum install tree -y
~~~

用法是如上图，后面跟目录名就可以看见目录的树状图



### rmdir && rm

**rmdir是一个与mkdir相对应的命令，mkdir创建目录，rmdir移除目录**

**语法：**rmdir [-p] [dirname]

**适用对象：**具有当前目录操作权限的所有使用者

**功能：**删除空目录

**常用选项：**

* -p 当子目录被删除后，如果父目录也变成空目录的话，连带父目录一起删除

![效果图1](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516193647.png "效果图1")

![删除不为空的目录](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516194013.png "删除不为空的目录")



**rm可以同时删除文件或者目录**

**语法：**rm [-f-i-r-v] [dirName/dir]

**适用对象：**所有使用者

**功能：**删除文件或目录

**常用选项：**

* -f 即使文件的属性为只读（即写保护），亦直接删除
* -i 删除前逐一询问确认
* -r 删除目录以及其下的所有文件

![删除文件](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516194531.png "删除文件")

![删除目录](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516194801.png "删除目录")

![删除不为空的目录](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516195017.png "删除不为空的目录")

**注意：**rmdir仅能删除文件，而rm既可以删除文件还可以删除目录，因此在后面几乎不会用上rmdir，rm最常用的组合就是 **rm -rf [dirName/dir]**



### man

Linxu的命令有很多参数，很难完全记住，可以通过直接查看联机手册获取帮助。访问Linux手册的命令是 ：

**man语法：**man [选择] 命令

**常用选择：**

* -k 根据关键字搜索联机帮助
* num 只在第num章节查找
* -a 将所有章节的都显示出来，比如 man printf 它缺省从第一章开始搜索，直到停止，用a选项后，当按下q退出时，它会继续向后搜索，直到所有章节搜索完毕



man手册分为8章

​	1 是普通命令

​	2 是系统调用，如open，write之类的（通过man手册能轻松查到这个函数该如何调用，需要加什么头文件）

​	3 是库函数，如printf，fread

​	4 是特殊文件，也就是/dev下的各种设备文件

​	5 是指文件的格式，譬如passwd，就会说明这个文件各个字段的含义

​	6 是给游戏留的，由各个游戏自己定义

​	7 是附件还有一些变量，比如environ这种全局变量在这里就有说明

​	8 是系统管理用的命令，这些命令只能由root使用，如ifconfig



### cp指令

**语法：**cp [选择] 源文件或目录 目标文件或目录

**功能：**复制文件或目录

**说明：**cp指令用于复制文件或目录，如同时指定两个以上的文件或目录，且最后的目的地是一个已经存在的目录，则它会把前面指定的所有内容复制到此目录中。若同时指定多个文件或目录，且最后目的地目录不存在，则会出现错误信息

**常用选项：**

* -f 或 --force 强行复制文件或目录，不论目的文件或目录是否存在
* -i 或 --interactive 覆盖文件之前先询问用户
* -r 递归处理，将指定目录下的文件与子目录一并处理。若源文件或目录的形态，不属于目录或符号链接，则一律视为普通文件处理
* -R 或 --recursive 递归处理，将指定目录下的文件以及目录一并处理

![复制文件](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516201856.png "复制文件")

![复制目录](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516204239.png "复制目录")



### mv

mv是move的缩写，可以用来移动文件或者将文件改名，是Linux系统下经常使用的命令，经常用来备份文件或目录

**语法：**mv [选择] 源文件或目录 目标文件或目录

**功能：**

1. 视mv命令中第二个参数类型不同（是目标文件还是目标目录），mv，命令将文件重命名或将其移动至一个新的目录中。
2. 当第二个参数类型是文件时，mv命令完成文件重命名，此时，源文件只能有一个（也可以是源目录名），它将所给的源文件或目录重命名为给定的目标文件名。
3. 当第二个参数的已存在的目录名称时，源文件或目录参数可以有多个，mv命令将各参数指定的源文件均移至目标文件中。

**常用选项：**

* -f （force）强制执行
* -i 若目标文件已存在时，就会询问是否覆盖

![移动文件](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516205409.png "移动文件")

![重命名文件](C:\Users\27156\AppData\Roaming\Typora\typora-user-images\image-20230516205533408.png "重命名文件")

![移动文件并重命名](C:\Users\27156\AppData\Roaming\Typora\typora-user-images\image-20230516210025873.png "移动文件并重命名")



### cat

**用法：**cat [选择] [文件]

**功能：**查看目标文件的内容

**常用选项：**

* -b 对非空输出编号
* -n 对输出的所有编号
* -s 不输出多行空行

![效果图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230516211102.png "效果图")

注意：根据需求，可以组合使用

### more

**语法：**more [选择] [文件]

**功能：**more命令，类似cat

**常用选项：**

* -n (n是数字)定义屏幕大小为n行
* +n （n是数字）从第几行开始显示
* q 退出more
* ...

![效果图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305162131396.png "效果图")

![多内容时使用](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305162137346.png "多内容时")

按q会退出



### less

* less 工具也是对文件或其它输出进行分页显示的工具，应该说是Linux正统查看文件内容的工具，功能及其强大
* less 的用法比起more更加的有弹性。在使用 more 的时候，我们没有办法向前面翻

* 但若使用了less时，就可以使用 [pageup] [pagedown] 等按键的功能来往前往后翻看文件，更容易来查看一个文件的内容
* 除此之外，在 less 里可以拥有更多的功能，不止可以上下翻，还可以搜索

**语法：**less [选项] 文件

**功能：**与 more 相似，但是 less 可以随意浏览文件，而 more 仅能向后移动，而且 less 在查看之前不会加载整个文件

**选项：**

* -i 忽略搜索时是大小写
* -N 显示每行的行号
*  /字符串 向下搜索“字符串”的功能
* ?字符串 向上搜索“字符串”的功能
* n 重复前一个搜索（与 / 或 ? 有关）
* N 反向重复前一个搜索（与 / 或 ? 有关）
* q 退出

![使用less并搜索](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305162147006.png "使用并搜索")



### head && tail

二者如名字一样浅显易懂，是用来显示开头或结尾某个数量的文字区块，head 用来显示档案的开头至标准输出，tail 就是档案的结尾

**head**

**语法：**head [选项] [文件]

**功能：**显示档案开头至标准输出中，默认head命令打印其相应文件的开头10行

**选项：**

* -n （数字）显示的行数

**tail**

**语法：**tai [选项] [文件]

**功能：**显示指定文件的末尾内容，不指定文件时，作为输入信息进行处理。常用于查看日志文件

**选项：**

* -f 循环读取
* -n （数字）显示行数

![效果图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305162156826.png "效果图")
