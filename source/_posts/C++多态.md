---
title: C++多态
date: 2023-09-29 20:23:09
categories: C++
tags:
 - C++
 - STL
 - Polymorphism
description:
cover:
---

# 概念

多态如字面意思，即多种形态。具体而言就是**去完成某个行为时，不同的对象去完成时会产生不同的状态。**

简而言之的例子就是买票，都是去买票，但是普通成人、学生、军人以都会有不同的价格，也算是有了多种状态。

# 定义以及实现

## 多态的构成条件

多态是在不同继承关系的类对象，去调用同一函数，产生了不同的行为。比如前面继承中使用的Student类继承了Person，Person对象全价买票，而Student对象半价买票。

在继承中**构成多态还需要两个条件：**

1. 必须通过基类的指针或者引用调用**虚函数**
2. 被调用的函数**必须是虚函数**，且派生类必须**对基类的虚函数进行重写**

~~~C++
class Person
{
public:
	virtual void BuyTicket()
	{
		cout << "成人全价" << endl;
	}
};

class Student : public Person
{
public:
	virtual void BuyTicket()
	{
		cout << "学生半价" << endl;
	}
};

void func(Person& people)
{
	people.BuyTicket();
}

void test()
{
	Person Mike;
	func(Mike);

	Student Jonny;
	func(Jonny);
}
~~~

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202309292102403.png" alt="图示" title="图示" style="zoom:67%;" />

## 虚函数

被**virtual**修饰的类成员函数称为虚函数。

~~~C++
class Person
{
public:
    //虚函数
	virtual void BuyTicket()
	{
		cout << "成人全价" << endl;
	}
};
~~~

## 虚函数的重写

虚函数的重写，也称作虚函数的覆盖。即，**派生类中有一个跟基类完全相同的虚函数（与基类虚函数的函数名、返回类型、参数列表完全相同），**称子类的虚函数重写了基类的虚函数。

~~~C++
class Person
{
public:
	virtual void BuyTicket()
	{
		cout << "成人全价" << endl;
	}
};

class Student : public Person
{
public:
	virtual void BuyTicket()
	{
		cout << "学生半价" << endl;
	}
    //虽然在重写基类虚函数时，派生类的虚函数在不加virtual关键字时，
    //仍能构成重写（原因是继承自基类的函数保留了虚函数属性），但是此写法不规范，不建议使用
    //void BuyTicket(){cout << "学生半价" << endl;}
};
~~~

**虚函数重写的两个例外：**

1. **协变（基类与派生类虚函数的返回值不同）**

派生类重写基类虚函数时，与基类虚函数的返回值不同。即基类虚函数的返回基类对象的指针或者引用，派生类虚函数返回派生类对象的指针或者引用。

~~~C++
class A{};
class B : public A {};

class Person 
{
public:
 virtual A* f() 
 {
     return new A;
 }
};

class Student : public Person 
{
public:
 virtual B* f() 
 {
     return new B;
 }
};
~~~

2. **析构函数的重写（基类与派生类析构函数的名字不同）**

如果基类的虚构函数为虚函数，此时派生类只要定义析构函数，无论是否有virtual关键字，都与基类的析构函数构成重写，虽然基类与派生类析构函数名字不同，看起来违背了重写的跪着，但实际上是编译器对析构函数的名称做了特殊处理，变异后析构函数的名称统一处理成destructor。

~~~C++
class Person 
{
public:
	virtual ~Person() 
 	{
    	cout << "~Person()" << endl;
 	}
};

class Student : public Person 
{
public:
    //名字不同，但仍构成重写，实际上名字也是相同的
    virtual ~Student() 
 	{ 
    	cout << "~Student()" << endl; 
 	}
};
~~~

## C++11 override 和 final

用于解决重写虚函数时由于疏忽导致的函数名不同，因为编译器在对虚函数重写要求严格的同时，由于名字细微差别造成的错误又不能正常报错，此时进行debug就会造成大量时间的浪费。因此就有了`override`和`final`两个关键字。

1. final：**修饰虚函数，表示该虚函数不能再被重写**

~~~C++
//运行如下代码就会进行报错
class Car
{
public:
	virtual void Drive() final {}
};

class Benz : public Car
{
public:
	virtual void Drive() 
    {
        cout << "Benz" << endl;
    }
};
~~~

2. override：**检查派生类虚函数是否重写了基类某个虚函数，如果没有重写编译器会报错**

~~~C++
class Car
{
public:
	virtual void Drive(){}
};

class Benz : public Car 
{
public:
	virtual void Drive() override 
    {
        cout << "Benz" << endl;
    }
};
~~~

## 重载、覆盖（重写）、隐藏（重定义）的对比

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202309292152979.png" alt="概念图" title="概念图" style="zoom:80%;" />

# 抽象类

## 概念

在虚函数的后面写上 `=0`，则这个虚函数为纯虚函数。**包含纯虚函数的类叫做抽象类（也叫接口类），抽象类不能实例化出对象。**派生类继承后也不能实例化出对象，只有重写纯虚函数，派生类才能实例化出对象。纯虚函数规范了派生类必须重写，另外纯虚函数更体现出了接口继承。

~~~C++
//不能用Car抽象类去实例化出对象
class Car
{
public:
	virtual void Drive() = 0;
};

class Benz :public Car
{
public:
	virtual void Drive()
	{
		cout << "Benz-舒适" << endl;
	}
};

class BYD :public Car
{
public:
	virtual void Drive()
	{
		cout << "BYD" << endl;
	}
};
~~~

## 接口继承和实现继承

**普通函数的继承是一种实现继承**，派生类继承了基类函数，可以使用函数，继承的是函数的实现。**虚函数的继承是一种接口继承**，派生类继承的是基类虚函数的接口，**目的是为了重写，达成多态，继承的是接口**。所以如果不实现多态，不要把函数定义成虚函数。

# 多态的原理

## 虚函数表

~~~C++
class Base
{
public:
	virtual void Func1()
	{
		cout << "Func1()" << endl;
	}
private:
	int _b = 1;
};

void test()
{
    Base b;
    cout<<sizeof(b)<<endl;
}
~~~

运行该程序会发现输出的结果是16而不是4（vs2022 x64），观察内存发现，**除了_b成员，还多一个__vfptr放在对象的前面(有些平台可能会放到对象的最后面，这个跟平台有关)，对象中的这个指针叫做虚函数表指针(v代表virtual，f代表function)**。

![结构示图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202309292217612.png "结构示图")

一个含有虚函数的类中都至少有一个虚函数表的指针，因为虚函数的地址要被放在虚函数表中，虚函数表也称虚表。

对上面的代码进行改造：

~~~C++
class Base
{
public:
	virtual void Func1()
	{
		cout << "Base::Func1()" << endl;
	}
	virtual void Func2()
	{
		cout << "Base::Func2()" << endl;
	}
	void Func3()
	{
		cout << "Base::Func3()" << endl;
	}
private:
	int _b = 1;
};
class Derive : public Base
{
public:
	virtual void Func1()
	{
		cout << "Derive::Func1()" << endl;
	}
private:
	int _d = 2;
};
int main()
{
	Base b;
	Derive d;
	return 0;
}
~~~

通过监控窗口发现：

1. 派生类对象d中也有一个虚表指针，d对象由两部分构成，一部分是父类继承下来的成员，虚表指针也就是存在该部分；另一部分是自己的成员。
2. 基类b对象和派生类d对象虚表是不一样的，由于**Func1完成了重写，所以d的虚表中存的是重写的Derive::Func1，所以虚函数的重写也叫作覆盖**，覆盖就是指虚表中虚函数的覆盖。重写是语法的叫法，覆盖是原理层的叫法。
3. Func2继承下来也是虚函数，因此也放入了虚表，但Func2未进行重写因而地址还是与b中的地址相同；Func3也继承了下来，但是不是虚函数，因而不会放入虚表。
4. 虚函数表本质是一个存虚函数指针的指针数组，一般情况这个数组最后面放了一个nullptr。
5. 总结一下派生类的虚表生成：a.先将基类中的虚表内容拷贝一份到派生类虚表中 b.如果派生类重写了基类中某个虚函数，用派生类自己的虚函数覆盖虚表中基类的虚函数 c.派生类自己新增加的虚函数按其在派生类中的声明次序增加到派生类虚表的最后。
6. **虚函数表存的是虚函数的指针，而不是虚函数**，虚函数和普通函数一样，存在于代码段，只是虚表内存有指向虚函数的指针。此外，对象中没有存虚表，而是存了虚表的指针（vs下虚表的存储位置在代码段）。

![结构图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202309301028811.png "结构图")

## 多态的原理

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202309292102403.png" alt="图示" title="图示" style="zoom:67%;" />

~~~C++
class Person
{
public:
	virtual void BuyTicket()
	{
		cout << "成人全价" << endl;
	}
};

class Student : public Person
{
public:
	virtual void BuyTicket()
	{
		cout << "学生半价" << endl;
	}
};

void func(Person& people)
{
	people.BuyTicket();
}

void test()
{
	Person Mike;
	func(Mike);

	Student Jonny;
	func(Jonny);
}
~~~

当`func`中的`people`指向`Mike`对象时，`people.BuyTicket`在`Mike`的虚表中找到的虚函数是`Student::BuyTicket`；而指向`Jonny`对象时，`people.BuyTicket`在`Jonny`的虚表中找到的虚函数是`Person::BuyTicket`。这样就实现了不同对象去完成同一行为时，展现出不同的形态。

## 动态绑定与静态绑定

* 静态绑定也称为前期绑定（早绑定），**在程序编译期间确实了程序的行为，也称为静态多态，**函数重载就是一种静态多态。

* 动态绑定又称为后期绑定（晚绑定），**是在程序运行期间，根据具体拿到的类型确定程序的具体行为，调用具体的函数，也称为动态多态。**

# 单继承与多继承的虚表函数

## 单继承中的虚函数表

~~~C++
class Base 
{
public:
	virtual void func1() 
	{ 
		cout << "Base::func1" << endl;
	}
	virtual void func2() 
	{ 
		cout << "Base::func2" << endl; 
	}
private:
	int a = 0;
};

class Derive : public Base 
{
public:
	virtual void func1() 
	{
		cout << "Derive::func1" << endl; 
	}
	virtual void func3() 
	{ 
		cout << "Derive::func3" << endl; 
	}
	virtual void func4() 
	{ 
		cout << "Derive::func4" << endl; 
	}
private:
	int b = 0;
};

void test()
{
	Base b;
	Derive d;
}
~~~

从图中很清晰地看见从`Base`中继承来的虚函数，但却**看不见`Derive`中定义的虚函数，实际上并不是没有，而是因为编译器的监视窗口进行了隐藏**，不论是直接查看内存还是一些特殊的处理方式都是能看见其存在于虚表之中。

![示意图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202309301051854.png "示意图")

## 多继承中的虚函数表

~~~C++
class Base1 
{
public:
	virtual void func1() 
	{ 
		cout << "Base1::func1" << endl; 
	}
	virtual void func2() 
	{ 
		cout << "Base1::func2" << endl;
	}
private:
	int b1 = 0;
};
class Base2 
{
public:
	virtual void func1() 
	{ 
		cout << "Base2::func1" << endl;
	}
	virtual void func2() 
	{
		cout << "Base2::func2" << endl; 
	}
private:
	int b2 = 0;
};

class Derive : public Base1, public Base2 
{
public:
	virtual void func1() 
	{ 
		cout << "Derive::func1" << endl; 
	}
	virtual void func3() 
	{ 
		cout << "Derive::func3" << endl; 
	}
private:
	int d1 = 0;
};

void test()
{
	Derive d;
}
~~~

从图中能看见多继承的派生类中，**继承了几个基类就有几张虚函数表。**

![示意图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202309301111880.png "示意图")

单继承中未重写的虚函数也是放在虚表之中，多继承也是如此，且存放于第一个基类的虚表之中，大致图为：

![概念图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202309301123348.png "概念图")

## 菱形虚拟继承

*菱形继承以及菱形虚拟继承是一种不提倡的方式，一方面是太复杂，另一方面是访问基类成员有一定性能损耗，实际中也极少使用，因而就不过多介绍。*

















































