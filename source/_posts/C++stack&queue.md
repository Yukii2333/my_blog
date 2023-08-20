---
title: C++stack&queue
date: 2023-08-20 15:48:47
categories: C++
tags: 
 - C++
 - STL 
 - stack
 - queue
description:
cover:
---

# 初步了解stack

## stack的介绍

[stack文档](https://legacy.cplusplus.com/reference/stack/stack/)

* stack是一种容器适配器，专门用在具有后进先出操作的上下文环境中，其删除只能从容器的一端进行元素的插入与提取操作。 
* stack是作为容器适配器被实现的，容器适配器即是对特定类封装作为其底层的容器，并提供一组特定 的成员函数来访问其元素，将特定类作为其底层的，元素特定容器的尾部(即栈顶)被压入和弹出。 
* stack的底层容器可以是任何标准的容器类模板或者一些其他特定的容器类，这些容器类应该支持以下操作： 
  * empty：判空操作 back：
  * 获取尾部元素操作 push_back：
  * 尾部插入元素操作 pop_back：
  * 尾部删除元素操作 
* 标准容器vector、deque、list均符合这些需求，默认情况下，如果没有为stack指定特定的底层容器， 默认情况下使用deque。

## stack的使用

参考文档也不难发现，此前用C语言模拟实现过的stack从封装的接口开看，其实是差不多的，实际上底层额也是类似，只不过比起自己完全的构造，底层使用了vector，因而不会再如前面几种那般介绍得详细，queue也是如此，此处就直接罗列常用接口了。

| 接口名称                                                     | 接口描述                |
| ------------------------------------------------------------ | ----------------------- |
| [stack](https://legacy.cplusplus.com/reference/stack/stack/stack/) | 构造空的栈              |
| [empty](https://legacy.cplusplus.com/reference/stack/stack/empty/) | 检测stack是否为空       |
| [size](https://legacy.cplusplus.com/reference/stack/stack/size/) | 返回stack中元素的个数   |
| [top](https://legacy.cplusplus.com/reference/stack/stack/top/) | 返回栈顶元素的引用      |
| [push](https://legacy.cplusplus.com/reference/stack/stack/push/) | 将元素val压入stack中    |
| [pop](https://legacy.cplusplus.com/reference/stack/stack/pop/) | 将stack中尾部的元素弹出 |

当然，并不是stack与queue不重要，只是先前实现的内容已经足够底层了，因而没有必要再深入底层去探讨，但是可以尝试用C++的stack来解决先前C语言也解决过的问题：[用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

# stack的模拟实现

如上所说，虽然要模拟实现stack，但是也不需要再在内部搞一些动态数组什么的了，并不是不行，而是已经有vector的前提下，可以直接内部使用vector，一方面是省事，另一方面也能提高对vector的使用。

详细代码参考：[vector的模拟实现](https://github.com/Yukii2333/Daily-Code/blob/main/Works/Work_cpp/2023_07_20_stack%26queue/Stack.h)

相比于C语言时实现的stack，虽说此前说是用`typedef int QDataType`之类的方式提高程序的泛用性，但就事实上来说，当程序一运行起来也不存在一点泛用性，此处就可以体现出来模板的巨大优势了（文章结尾会贴上先前的代码供参考）。

# 初步了解queue

## queue的介绍

[queue文档](https://legacy.cplusplus.com/reference/queue/queue/)

* 队列是一种容器适配器，专门用于在FIFO上下文(先进先出)中操作，其中从容器一端插入元素，另一端提取元素。 
* 队列作为容器适配器实现，容器适配器即将特定容器类封装作为其底层容器类，queue提供一组特定的成员函数来访问其元素。元素从队尾入队列，从队头出队列。 
* 底层容器可以是标准容器类模板之一，也可以是其他专门设计的容器类。该底层容器应至少支持以下操作: 
  * empty：检测队列是否为空 size：
  * 返回队列中有效元素的个数 front：
  * 返回队头元素的引用 back：
  * 返回队尾元素的引用 push_back：
  * 在队列尾部入队列 pop_front：在队列头部出队列 
* 标准容器类deque和list满足了这些要求。默认情况下，如果没有为queue实例化指定容器类，则使用标准容器deque。

## queue的使用

仍然是已经底层实现过的数据结构，就不再过多赘述，如下为常用接口：

| 接口名称                                                     | 接口描述                 |
| ------------------------------------------------------------ | ------------------------ |
| [queue](https://legacy.cplusplus.com/reference/queue/queue/queue/) | 构造空的队列             |
| [empty](https://legacy.cplusplus.com/reference/queue/queue/empty/) | 检测队列是否为空         |
| [size](https://legacy.cplusplus.com/reference/queue/queue/size/) | 返回队列中有效元素的个数 |
| [front](https://legacy.cplusplus.com/reference/queue/queue/front/) | 返回队头元素的引用       |
| [back](https://legacy.cplusplus.com/reference/queue/queue/back/) | 返回队尾元素的引用       |
| [push](https://legacy.cplusplus.com/reference/queue/queue/push/) | 在队尾将元素val入队列    |
| [pop](https://legacy.cplusplus.com/reference/queue/queue/pop/) | 将队头元素出队列         |

不过相比于stack，队列既可以用链表（list）也可以用数组（vector）来实现，C语言中不推荐使用数组的原因是操作比较繁琐，而且效率更低，不过C++可以在内部使用vector或者list，效率上确实有差别，但是繁琐程度上是大差不差的，当然还是要尝试一下以前解决过的问题：[用队列实现栈](https://leetcode.cn/problems/implement-stack-using-queues/)

# queue的模拟实现

详细代码参考：[queue的模拟实现](https://github.com/Yukii2333/Daily-Code/blob/main/Works/Work_cpp/2023_07_20_stack%26queue/Stack.h)

至于是不是非得要用数组队列和链表队列两种，当然是不用，底层默认使用一种就行，但建议还是动手尝试，至于模板使用有问题那就是下一篇的内容了。

# 结尾

最后是先前实现的栈与队列的相关文章以及代码：[CSDN](https://blog.csdn.net/Tohka2333/article/details/129762965?spm=1001.2014.3001.5501)
