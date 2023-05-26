---
title: C++内存管理
date: 2023-05-18 20:13:13
categories: C++
tags: 
 - C++
description: 
cover:
---

# C/C++内存分布

根据以前的学习，我们曾了解过出过如下表

![内存区域划分](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305182045863.png "内存区域划分")

~~~C
//可以判断一下该段代码内的各属于内存区域中的哪一个部分

int Var1 		= 1;
static int Var2 = 1;

int main()
{
    static int Var3 = 1;
    int Var4 		= 1;
    int num[10] 	= {1,2,3};
    char Char[] 	= "abcd";
    char *pChar 	= "abcd";
    int* ptr1 		= (int*) malloc(sizeof(int)*4);
    int* ptr2 		= (int*) calloc(4,sizeof(int));
    free(ptr1);
    free(ptr2);
    return 0;
}
~~~

其分部如图所示

![分布情况](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305182054925.png "分布情况")

**说明：**

* **栈**又叫栈堆--非静态局部变量/函数参数/返回值等等，栈是向下增长的（地址由大到小）
* **内存映射段**是高效的 I/O 映射方式，用于装载一个共享的动态内存库。用户可使用系统接口创建共享内存，做进程通信。
* **堆**用于程序运行时动态内存分配，堆是可以向上增长的（地址由小到大）
* **数据段**--存储全局和静态数据
* **代码段**--可执行的代码/只读常量

（之前我们把栈称为栈区，堆称为堆区，数据段称为静态区）



# C语言中动态内存管理方式

即**`malloc`、`calloc`、`realloc`、`free`**

~~~C
int main()
{
    int* p1 = (int*)malloc(sizeof(int));
    free(p1);
    
    int* p2 = (int*)calloc(4,sizeof(int));
    int* p3 = (int*)realloc(p2,sizeof(int)*10);
    
    free(p3);
    return 0;
}
~~~

## 函数功能

### malloc

**函数原型：**

~~~C
void* malloc(size_t size);
~~~

**功能：**

在堆中分配一块长度为`size`字节的连续区域，参数`size`为需要分配的空间长度，单位是字节

### calloc

**函数原型：**

~~~C
void* calloc(size_t num, size_t size) 
~~~

**功能：**

在堆中分配一块`size*num`字节的连续区域，参数`num`为需要分配的数量；`size`为需要分配的单个长度，单位是字节

### realloc

**函数原型：**

~~~C
void* realloc(void* ptr, size_t size)
~~~

**功能：**

在堆中为`ptr`重新分配大小为`size`字节的一块内存：

* 若`ptr`为空，则该函数相对于`malloc`
* 若`ptr`不为空，但是`size`为`0`，则该函数相对于`free`
* 重新申请空间时会优先就地进行扩容，当原位置后面空余空间不够时，会在另一个位置开辟一块目标大小的空间，再将原内容拷贝过去，最后释放掉原内容

### free

**函数原型：**

~~~C
void free(void* ptr)
~~~

**功能：**

释放通过`clloc`、`malloc`或是`realloc`在堆上申请的空间（也就是将使用权限返还给操作系统）

# C++内存管理方式

由于C++兼容C语言，因此C语言的内存管理方式在C++中依然可以使用，但是在某一些地方会显得无能为力或者繁琐，因此C++提出了自己的内存管理方式：**通过`new`和`delete`操作符进行动态内存管理**

## new/delete操作内置类型

~~~C++
int main()
{
    //动态申请一个int类型的空间
    int* ptr1 = new int;
    //动态申请一个int类型的空间并初始化为10
    int* ptr2 = new int(10);
    //动态申请3个int类型的空间
    int* ptr3 = new int[3];
    
    //释放申请的空间
    delete ptr1;
    delete ptr2;
    //申请了多个空间释放时要带上[]
    delete[] ptr3;
    return 0;
}
~~~

**申请和释放单个元素的空间，使用new和delete操作符，申请和释放连续空间，使用new[]和delete[]**，要注意匹配使用，不能new出来的元素用free释放，也不能malloc出来的元素用delete释放。

## new/delete操作自定义类型

~~~C++
//类声名
class A
{
public:
    A(int a = 0)
        :a_(a)
    {
        cout<<"A()"<<this<<endl;
    }
    ~A()
    {
        cout<<"~A()"<<endl;
    }
private:
    int a_;
};


int main()
{	
    //动态申请一个A类的空间
    A* a1 = new A;
    //动态申请一个A类的空间并初始化为10
    A* a2 = new A(10);
    //动态申请3个A类的空间
    A* a3 = new A[3];
	
    //释放申请的空间
    delete a1;
    delete a2;
    delete[] a3;
    return 0;
}
~~~

看输出窗口可以很明了的知道，上面创建了5个类，分别调用了5次构造函数和5次析构函数，我们当然也可以用`malloc/free`之类的来申请自定义类型的空间，而二者的区别就是**`malloc/free`之类的并不会去调用类的析构和构造函数，但是`new/delete`是可以的**

![输出窗口](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305182149624.png "输出窗口")

# operator new && operator delete函数

`new`和`delete`是用户进行**动态内存申请和释放的操作符**，而`operator new` 和`operator delete`是系统提供的**全局函数，new在底层调用operator new**全局函数来申请空间，**delete在底层调用operator delete**全局函数来释放空间

## 函数功能

### operator new：

​	该函数实际通过`malloc`来申请空间，当`malloc`申请成功时直接返回，申请空间失败时尝试执行空间不足的措施，如果改用应对措施用户设置了则继续申请，否则抛异常。

### operator delete：

​	该函数实际通过`free`来释放空间，与`operator new`类似，成功就继续执行，失败就抛异常

**关于抛异常**

​	在C语言中，像申请空间失败会返回`NULL`，但是不符合C++的需求，面向对象的语言失败后不建议用返回值，更倾向抛异常，直接将错误显示出来而不是显示`错误内容+编号`

**捕获异常**

​	譬如下面的代码

~~~C++
int main() 
{
    int* p1;
    //死循环，必然会崩溃
    do
    {
        p1 = new int[1024*1024];
    }while(p1);
    return 0;
}
~~~

要知道错误信息的话就需要捕获空间开辟失败抛出的异常，方法如下

~~~C++
int main() 
{
    int* p1;
    //将要进行捕获的内容放入try的里面
    try
    {
        do
        {
            p1 = new int[1024 * 1024];
        } while (p1);
    }
    //再通过catch进行输出
    catch (const exception& e)
    {
        cout << e.what() << endl;
    }

    return 0;
}
~~~

最终在命令行窗口输出了如下的信息，即申请空间失败

![](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230519103834.png "")

正因如此，C++不再需要像C语言那样对申请空间后的内容进行检查，而是捕获异常

# new/delete 的实现原理

## 内置类型

如果申请的是内置类型的空间，`new`和`malloc`，`delete`和`free`基本类似，不同的地方是：

​	`new/delete`申请和释放是单个元素的空间，`new[]/delete[]`申请和释放的是连续空间，而且`new`在申请空间失败时会抛异常，`malloc`会返回`NULL`

## 自定义类型

### new

* 调用`operator new`函数申请空间
* 再在申请的空间上执行构造函数，完成对象的构造

### delete

* 先在要释放的空间上执行析构函数，完成对象中资源清理工作
* 调用`operator delete`函数释放对象的空间

### new T[N]

* 调用` operator new[]`函数，在其中实际调用`operator new`函数完成N个对象的空间申请
* 再在申请的空间上执行N次构造函数

### delete []

* 先在要释放的空间上执行N次析构函数，完成N个对象中资源清理工作

* 调用`operator delete[]`函数，在其中实际调用`operator delete`函数释放N个对象的空间

# 定位new表达式(placement-new)

定位new表达式是在**已经分配的原始内存空间中调用构造函数初始化一个对象**

**使用格式：**

`new(place_address)type`或者`new(place_address)type(initializer-list)`，`place_address`必须是一个指针，`initializer-list`是类型的初始化列表

**使用场景：**

定位new表达式一般是配合内存池来进行使用。因为内存池分配的内存没有初始化，所以如果是自定义类型对象，需要使用new的定义表达式进行显示调用构造函数进行初始化。

内存池通俗理解就是，从山顶到山下河里取水的几户人，在山腰修了水池，需要水时就在水池取水，水池没有水以后就从河里抽水到水池，提高了取水效率。

*在后面学习内存池时还会提到，此处暂且作为了解*
