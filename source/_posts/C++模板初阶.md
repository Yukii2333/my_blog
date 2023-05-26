---
title: C++模板初阶
date: 2023-05-19 11:16:19
categories: C++
tags:
 - C++
 - template
description:
cover:
---

# 函数模板

## 函数模板的概念

**函数模板代表了一个函数家族，该函数模板与类型无关，在使用时被参数化，根据实参类型产生函数的特定类型版本**



譬如，在之前C语言学习前期，写过一个`Add`函数，如下

~~~C
int Add(int a, int b)
{
    return a+b;
}
~~~

但是在实际使用时发现并不是很有用（因为只能计算整形），而C++中又有了函数重载，可以写更多的同名不同类型的函数如

~~~C++
//整形加法
int Add(int a, int b)
{
    return a+b;
}
//小数加法
double Add(double a, double b)
{
    return a+b;
}
//字符类加法（实际char类型也是整形，此处为举例）
char Add(char a, char b)
{
    return a+b;
}
// ...
~~~

不难发现，当类型足够多时，就会写下来一大堆函数，且每个函数的内容几乎一致，而模板的作用就是解决此类问题

## 函数模板的格式

**template<typyname T1, typename T2,..., typename Tn>**

**返回类型 函数名(参数列表){}**

~~~C++
//typename是用来定义模板的关键字，可以用class代替，但不能是struct
//模板类型可以多个
template<typename T>
T Add(T a, T b)
{
    return a+b;
}
~~~

模板调用的话也简单，如

~~~C++
template<typename T>
T Add(T a, T b)
{
    return a+b;
}

int main()
{
    //编译器识别实参类型，然后根据模板构建一个所需类型的函数
    int a1 = 10;
    int b1 = 20;
    int c1 = Add(a1,b1);
    
    double a2 = 10.1;
    double b2 = 20.2;
    double c2 = Add(a2,b2);
    
    return 0;
}
~~~

## 函数模板的原理

**函数模板是一个蓝图，本身并不是函数，是编译器用于产生特点类型函数的模具。所以模板的实质就是把本应该我们做的重复事情交给了编译器**

譬如上面，调用了两次`Add`函数，但是两次调用实参类型不同，因此编译器根据模板生成了如下两个`Add`函数

~~~C++
int Add(int a, int b)
{
    return a+b;
}

double Add(double a, double b)
{
    return a+b;
}
~~~

## 函数模板的实例化

**用不同类型的参数使用模板函数时，**称为函数模板**实例化**。模板函数的实例化分为：**隐式实例化和显示实例化**。

### 隐式实例化

正如上面所写的，是**让编译器根据实参类型推演模板参数的实际类型**

~~~C++
//隐式实例化
template<typename T>
T Add(T a, T b)
{
    return a+b;
}

int main()
{
    int a1 = 10;
    int b1 = 20;
    Add(a1,b1);
    
    double a2 = 10.1;
    double b2 = 20.2;
    Add(a2,b2);
    
    
    //由于是隐式实例化，编译器是依据实参类型进行推演
    //但是下面的两个实参类型不同，编译器会不知道使用哪一种类型
    
    int a3 = 10;
    double b3 = 20.2;
    Add(a3,b3);
    
    //解决方法：
    //强制类型转换
    Add(a3,(int)b3);
    
    //以及显示实例化
    
    return 0;
}
~~~

### 显示实例化

方法：**在函数名后的`<>`中指定模板参数的实际类型**

~~~C++
//隐式实例化
template<typename T>
T Add(T a, T b)
{
    return a+b;
}

int main()
{
    int a = 10;
    double b = 20.2;
	Add<int>(a,b);
    return 0;
}
~~~

如果类型不匹配，编译器会尝试进行隐式类型转换，如果无法成功转换编译器就会报错（譬如将自定义类型转换为内置类型）

## 模板参数的匹配原则

* **一个非模板函数可以和一个同名的模板函数同时存在，而且该函数模板还可以被实例化为这个非模板函数**

~~~C++
//专门处理int的加法
int Add(int a, int b)
{
	return a + b;
}

//通用加法函数
template<typename T>
T Add(T a, T b)
{
	return a + b;
}

int main()
{
    //与非模板函数匹配，编译器不需要特化
	Add(1, 2);
    //调用特化的Add版本
	Add<int>(1, 2);
	
	return 0;
}
~~~

* **对于非模板函数和同名函数模板，如果其它条件相同，在调用时会优先调用非模板函数，而不会从该模板产生出一个实例。如果模板能够产生一个具有更好匹配的函数，那么就选择模板**

~~~C++
int Add(int a, int b)
{
	return a + b;
}

template<typename T>
T Add(T a, T b)
{
	return a + b;
}

int main()
{
	Add(1, 2);
	Add<int>(1, 2);

    //...
    // 存在类型转换，因而会调用非模板函数
	Add(1, 1.1);
    //两个实参都是同类型，因此模板能够产生一个不需要转换的函数
    //因此此处调用靠模板函数生成的函数
	Add(1.1, 2.2);
	return 0;
}
~~~

* **模板函数不允许自动类型转换，但是普通函数可以进行自动类型转换**

# 类模板

## 类模板的定义格式

~~~C++
template<class T1, class T2, ..., class Tn>
class 类模板名
{
    //类的成员定义
}
~~~

## 类模板的实例化

类模板的实例化与函数模板不同，**类模板实例化需要在类模板名字后跟`<>`,然后将实例化的类型放在其中，类模板名字不是真正的类，而实例化的结果才是真正的类。**

~~~C++
template<class T>
class A
{
public:
	//...
private:
	T info_;
};

int main()
{
    //类模板的实例化
    //A是类名，A<int>才是类型
	A<int> num;
	A<char> name;
	return 0;
}
~~~

*类模板在其他方面就与函数模板差不多了，就不赘述了*
