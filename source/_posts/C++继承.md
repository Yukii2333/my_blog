---
title: C++继承
date: 2023-08-24 16:02:44
categories: C++
tags: 
 - C++
 - STL
 - inheritance
description:
cover:
---

# 继承的概念及定义

## 概念

**继承**(inheritance)机制是面向对象程序设计**使代码可以复用**的最重要的手段，它允许程序员在**保 持原有类特性的基础上进行扩展**，增加功能，这样产生新的类，称派生类。继承**呈现了面向对象程序设计的层次结构**，体现了由简单到复杂的认知过程。以前我们接触的复用都是函数复用，**继承是类设计层次的复用。**

~~~C++
class Person
{
public:
    void Print()
    {
        cout<<"name:"<<name_<<endl;
        cout<<"age"<<age_<<endl;
    }
protected:
    string name_ = "XiaoMing";
    int age_ = 18;
};

//继承后父类的Person的成员（成员函数+成员变量）都会变成子类的一部分

class Student : public Person
{
protected:
    int stuid_;
};

class Teacher : public Person
{
protected:
    int jobid_;
};

~~~

## 继承定义

### 定义格式

~~~C++
//Person是父类，也称作基类。Student是子类，也称作派生类
//    子类      继承方式  基类（父类）
class Student : public Person
{
protected:
    int stuid_;
}
~~~

### 继承方式与访问限定符

![继承方式](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202308241616046.png "继承方式")

![访问限定符](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202308241617965.png "访问限定符")

### 继承基类成员访问方式的变化

| 类成员/继承方式     | public继承             | protected继承          | private继承          |
| ------------------- | ---------------------- | ---------------------- | -------------------- |
| 基类的public成员    | 派生类的public成员     | 派生类的protected 成员 | 派生类的private 成员 |
| 基类的protected成员 | 派生类的protected 成员 | 派生类的protected 成员 | 派生类的private 成员 |
| 基类的private成 员  | 在派生类中不可见       | 在派生类中不可见       | 在派生类中不可 见    |

* 基类private成员在派生类中无论以什么方式继承都是不可见的。**这里的不可见是指基类的私有成员还是被继承到了派生类对象中，但是语法上限制派生类对象不管在类里面还是类外面 都不能去访问它。** 
* 基类private成员在派生类中是不能被访问，如果基类成员不想在类外直接被访问，但需要在派生类中能访问，就定义为protected。**可以看出保护成员限定符是因继承才出现的。 **
* 实际上面的表格我们进行一下总结会发现，基类的私有成员在子类都是不可见。基类的其他 成员在子类的访问方式 == Min(成员在基类的访问限定符，继承方式)，public > protected > private。 
* 使用关键字class时默认的继承方式是private，使用struct时默认的继承方式是public，**不过最好显示的写出继承方式。 **
* **在实际运用中一般使用都是public继承，几乎很少使用protected/private继承**，也不提倡使用protected/private继承，因为protected/private继承下来的成员都只能在派生类的类里 面使用，实际中扩展维护性不强。

# 基类和派生类对象赋值转换

* **派生类对象 可以赋值给 基类的对象 / 基类的指针 / 基类的引用**。有个形象的说法叫切片或者切割。寓意把派生类中父类那部分切来赋值过去。
* 基类对象不能赋值给派生类对象。
* 基类的指针或者引用可以通过强制类型转换赋值给派生类的指针或者引用。但是必须是基类 的指针是指向派生类对象时才是安全的。

~~~C++
class Person
{
public:
    void Print()
    {
        cout << "name:" << name_ << endl;
        cout << "age" << age_ << endl;
    }
protected:
    string name_ = "XiaoMing";
    int age_ = 18;
};

class Student : public Person
{
protected:
    int stuid_;
};


void test()
{
    //子类对象可以赋值给父类对象/指针/引用
    Student st;
    Person ps = st;
    Person* pps = &st;
    Person& pp = st;
    
	//基类对象不能赋值给派生类对象，会报错
    st = ps;

    //基类的指针可以通过强制类型转换赋值给派生类的指针
    //反过来时会存在越界的情况
    pps = &ps;
    Student* pst = (Student*) pps;
}
~~~

# 继承中的作用域

* 在继承体系中**基类和派生类都有独立的作用域**。 
* 子类和父类中有同名成员，**子类成员将屏蔽父类对同名成员的直接访问，这种情况叫隐藏， 也叫重定义。**（在子类成员函数中，可以使用 **基类::基类成员 显示访问**） 
* 如果是成员函数的隐藏，只需要函数名相同就构成隐藏。 
* 在实际中在**继承体系里面最好不要定义同名的成员**。

~~~C++
class A
{
public:
	void func()
	{
		cout << "A::func()" << endl;
	}
};

class B : public A
{
public:
	void func()
	{
		cout << "B::func()" << endl;
	}
};

void test()
{
	A a;
	B b;
	a.func();
	b.func();
	b.A::func();
}
~~~

# 派生类的默认成员函数

* 派生类的构造函数必须调用基类的构造函数初始化基类的那一部分成员。如果基类没有默认的构造函数，则必须在派生类构造函数的初始化列表阶段显示调用。
* 派生类的拷贝构造函数必须调用基类的拷贝构造完成基类的拷贝初始化。
* 派生类的operator=必须要调用基类的operator=完成基类的复制。
* 派生类的析构函数会在被调用完成后自动调用基类的析构函数清理基类成员。因为这样才能保证派生类对象先清理派生类成员再清理基类成员的顺序。
* 派生类对象初始化先调用基类构造再调派生类构造。
* 派生类对象析构清理先调用派生类析构再调基类的析构。
* 后续一些场景析构函数需要构成重写，重写的条件之一是函数名相同。编译器会对析构函数名进行特殊处理，处理成destructor()，所以父类析构函数不加virtual的情况下，子类析构函数和父类析构函数构成隐藏关系。

# 继承与友元

**友元关系不能继承**，也就是说基类友元不能访问子类私有和保护成员

# 继承与静态成员

**基类定义了static静态成员，则整个继承体系里面只有一个这样的成员。**无论派生出多少个子类，都只有一个static成员实例 。

~~~C++
class Person
{
public:
	Person()
	{
		++count_;
	}
protected:
	string name_;
public:
	static int count_;
};

int Person::count_ = 0;

class Student : public Person
{
protected:
	int stuid_;
};

void test()
{
	Person p1;
	Person p2;
	Student s1;
	Student s2;
	cout << "人数：" << Person::count_ << endl;
}
~~~

# 菱形继承及菱形虚拟继承

* 单继承：一个子类只有一个直接父类时称这个继承关系为单继承

~~~C++
class Person
{
public:
	Person()
	{
		++count_;
	}
protected:
	string name_;
};

class Student : public Person
{
protected:
	int stuid_;
};

class Graduate : public Student
{
protected:
    string seminarCourse_;
};
~~~

* 多继承：一个子类有两个或以上直接父类时称这个继承关系为多继承

~~~C++
class Student
{
protected:
    string name_;
	int stuid_;
};

class Teacher
{
protected:
    string name_;
	int jobid_;
};

class Assistant : public  Student, public  Teacher
{
protected:
	//......    
}
~~~

* 菱形继承：菱形继承是多继承的一种特殊情况。

~~~C++
class Person
{
public:
    
	//......
    
protected:
	string name_;
};

class Student : public Person
{
protected:
	int stuid_;
};

class Teacher : public Person
{
protected:
	int jobid_;
};

class Assistant : public  Student, public  Teacher
{
protected:
	//......    
};
~~~

**菱形继承的问题：菱形继承有数据冗余和二义性的问题。 在Assistant的对象中Person成员会有两份。**

**虚拟继承可以解决菱形继承的二义性和数据冗余的问题。如上面的继承关系，在Student和Teacher的继承Person时使用虚拟继承，即可解决问题。需要注意的是，虚拟继承不要在其他地方去使用。**

~~~C++
class Person
{
public:
    
	//......
    
protected:
	string name_;
};

class Student : virtual public Person
{
protected:
	int stuid_;
};

class Teacher : virtual public Person
{
protected:
	int jobid_;
};

class Assistant : public  Student, public  Teacher
{
protected:
	//......    
};
~~~

**虚拟继承解决数据冗余和二义性的原理**：

根据内存窗口观察对象成员的模型可以发现，其中通过两个指针，指向一张表。这两个指针叫虚基表指针，这两个表叫虚基表。虚基表中存的偏移量。通过偏移量可以找到下面对应的基类。

# 总结和反思

* **有了多继承，就存在菱形继承**，有了菱形继承就有菱形虚拟继承，底层实现就很复杂。所以一般不建议设计出多继承，一定不要设计出菱形继承。否则在复杂度及性能上都有问题。

* 多继承可以认为是C++的缺陷之一，很多后来的OO语言都没有多继承，如Java。
*  继承和组合
  * public继承是一种is-a的关系。也就是说每个派生类对象都是一个基类对象。
  * 组合是一种has-a的关系。假设B组合了A，每个B对象中都有一个A对象。
  * 优先使用对象组合，而不是类继承 。
  * 继承允许用户根据基类的实现来定义派生类的实现。这种通过生成派生类的复用通常被称为白箱复用(white-box reuse)。术语“白箱”是相对可视性而言：在继承方式中，基类的内部细节对子类可见 。继承一定程度破坏了基类的封装，基类的改变，对派生类有很大的影响。派生类和基类间的依赖关系很强，耦合度高。
  * 对象组合是类继承之外的另一种复用选择。新的更复杂的功能可以通过组装或组合对象来获得。对象组合要求被组合的对象具有良好定义的接口。这种复用风格被称为黑箱复用(black-box reuse)，因为对象的内部细节是不可见的。对象只以“黑箱”的形式出现。组合类之间没有很强的依赖关系，耦合度低。优先使用对象组合有助于你保持每个类被封装。
  * 实际尽量多去用组合。组合的耦合度低，代码维护性好。不过继承也有用武之地的，有些关系就适合继承那就用继承，另外要实现多态，也必须要继承。类之间的关系可以用继承，可以用组合，就用组合。
