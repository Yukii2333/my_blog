---
title: C++入门——Ⅰ
date: 2023-04-15 21:33:37
categories: Notes
tags:
 - C++
description: Notes about C++
cover:
---

# 什么是C++

C语言是结构化和模块化的语言，适合处理较小的程序。对于复杂的问题，规模较大的程序，需要高度的抽象和建模时，C语言则不合适。为了解决软件危机，20世纪80年代，计算机界提出了**OPP(object oriented programing：面向对象)思想**，支持面向对象的程序设计语言应运而生。

1982年，Bjarne Stroustrup（本贾尼·斯特劳斯特卢普）博士在C语言的基础上引入并扩充了面向对象的概念，发明了一种新的程序语言。为了表达语言与C语言的渊源关系，命名为C++。因此：C++是基于C语言产生的，它既可以进行C语言的过程化程序设计，又可以进行**以抽象数据类型为特点的基于对象的程序设计，还可以进行面向对象的程序设计。**



# C++入门

## 关键字

C语言中的关键字有32个，而C++总计63个，如图（图片来自[百度百科](https://baike.baidu.com/item/C%2B%2B%E5%85%B3%E9%94%AE%E5%AD%97/5773813#reference-[1]-3111818-wrap)）

![C++关键字](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/keyword.png "C++关键字")

有很多是未曾见过的，初识暂时不用深入了解

## 命名空间

在C/C++中，变量、函数和后面要学到的类都是大量存在的，这些变量、函数和类的名称将都存在于全局作用域中，可能会导致很多冲突。使用命名空间的目的是**对标识符的名称进行本地化**，以**避免命名冲突或名字污染**，namespace关键字的出现就是针对这种问题的。

如下，在C语言中会遇到命名冲突

~~~C
#include <stdio.h>
#include <stdlib.h>

int rand = 10;

int main()
{
    printf("%d\n",rand);
    return 0;
}
//运行后会编译报错：error C2365: "rand"：重定义；以前的定义是“函数“
~~~

### 命名空间定义

对命名空间的定义，需要用到**namespace关键字**，后面跟**命名空间的名字**，然后**接一对{}**，{}中为命名空间的成员。如下

~~~c++
//创建命名空间，命名空间名字为yuki
namespace yuki
{
    //命名空间内可以定义变量/函数/结构体
	int rand = 10;
	  
    int Add(int a,int b)
    {
    	return a+b;  
	}
    
    struct Node
    {
        struct Node* next;
        int data;
    };
    
    //命名空间内还可以嵌套命名空间，嵌套的命名空间名字为yukii
    namespace yukii
    {
        int rand = 9;
    }
}
~~~

#### 补充

* 同一个工程中允许多个相同名称的命名空间，编译器最后会合并成同一个命名空间

* 一个命名空间就定义了一个新的作用域，命名空间中的所有内容都局限于该命名空间中

### 命名空间的使用

#### 使用作用域限定符

拿上面的命名空间举一个例子，下面有命名空间里的a，命名空间内嵌套的命名空间内的a，一个全局变量a，以及主函数内部的一个a，运行函数会发现打印结果为7，说明优先访问主函数

~~~C++
namespace yuki
{
    int a = 10;

    int Add(int a, int b)
    {
        return a + b;
    }

    struct Node
    {
        struct Node* next;
        int data;
    };

    namespace yukii
    {
        int a = 9;
    }
}

int a = 8;

int main()
{
    int a = 7;
    printf("%d\n", a);
	return 0;
}
~~~

将主函数内的a屏蔽掉，会发现输出结果为8，说明其次访问全局变量

~~~C++
int main()
{
    //int a = 7;
    printf("%d\n", a);
	return 0;
}
~~~

不过此时再将全局的a给屏蔽掉，会发现会报错，这是因为在没有指定的情况下是不会去访问命名空间的，因此需要用上作用域限定符 "::"，举个例子，假如想访问yuki命名空间内的a，可以这样写

~~~C++
int main()
{
    int rand = 7;
    //::符号左边是指定域，右边是指定需要访问的内容
    printf("%d\n", yuki::a);
	return 0;
}
~~~

若要访问yuki下的yukii内的a，则

~~~C++
int main()
{
    int rand = 7;
    //可以理解成先找命名空间yuki，接着在命名空间内找命名空间yuki
    //最后在命名空间yukii内找变量a
    printf("%d\n", yuki::yukii::a);
	return 0;
}
~~~

那如何在局部变量存在的情况下，访问全局变量呢？很简单，将符号左边留空即可，留空即默认情况下就是访问全局

#### 使用using将命名空间或者命名空间内的某个成员引入

##### 引入命名空间

譬如要引入yuki这个命名空间

~~~C++
//理解成将命名空间的作用域展开，使其暴露在全局
using namespace yuki
~~~

既然会暴露在全局，那么，与全局变量相同的变量会发生冲突，不过此时就可以直接类似全局变量一样对其进行访问

##### 引入命名空间内某个成员

为了避免引发命名冲突的可能性，并且并不是命名空间内的所有成员都需要进行访问，那么对某个成员进行引入即可

~~~C++
//需要用到作用域限定符
using namespace yuki::Node
~~~

此时，只会有Node这一结构体暴露在全局之下，减少了命名冲突的几率

#### 补充

C++的标准库都在命名空间std内，建议在项目内不去展开，在大型项目内直接展开可能会有大量的命名冲突问题，需要使用时直接指定即可，实在常用的可以对其进行指定展开

## C++的输出&输入

#### 输出

类似C语言的第一节课学习，会认识一个输出函数 printf，而在C++的第一节课中，也会认识一个输出函数 cout

~~~C++
//使用cout必须包含的头文件
#include<iostream>

int main()
{
    //std是命名空间，平时练习为了简便可以直接展开使用，此处先不展开
	std::cout << "Hello Word!" << std::endl;
	return 0;
}
~~~

**<< 是流插入运算符**，形象点理解就是，将<<后的内容流入cout流中，文本的输出需要加上引号，endl 相对于C语言中的 '\n'，即上面代码还可以写作

~~~C++
#include<iostream>

int main()
{
    //用双引号也行
	std::cout << "Hello Word!" << '\n';
	return 0;
}
~~~

对于变量的输出，会发现其相较于C语言，显得格外方便

~~~C++
int main()
{
	int a = 10;
	float b = 3.1415;
    //输出a
	std::cout << a << std::endl;
    //输出b
	std::cout << b << std::endl;
	return 0;
}
~~~

最大的优势在于，cout会自动识别变量的格式，并对应输出，即，不用像C语言那样指定格式输出。

#### 输入

有输出也就会有输入，与scanf 函数相对应，用法如下

~~~C++
//使用cin必须包含的头文件
#include<iostream>

int main()
{
	int a = 10;
	float b = 3.1415;
    //输入a值
	std::cin >> a;
    //输入b值
	std::cin >> b;
	std::cout << a << std::endl;
	std::cout << b << std::endl;
	return 0;
}
~~~

同样的，cin也和cout一样可以自动识别变量类型，原理是函数重载（后面会提到，此处不展开）

#### 补充

虽然cout和cin很便利，但是建议还是搭配printf和scanf一起来使用，在某些地方还是比较麻烦（比如浮点数的精确度），而且C++是包含C语言的，在实际项目中也会二者混合来使用。
