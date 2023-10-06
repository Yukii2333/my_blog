---
title: map&set的模拟实现
date: 2023-10-06 09:36:16
categories: C++
tags:
 - STL
 - map
 - set
 - RBTree
description:
cover: https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051130659.svg
---

![红黑树](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051130659.svg "红黑树")

# 红黑树的迭代器

## begin与end

STL明确规定，begin()与end()代表的是一段前闭后开的区间，而对红黑树进行中序遍历后，可以得到一个有序的序列，因此：**begin()可以放在红黑树中最小节点(即最左侧节点)的位 置，end()放在最大节点(最右侧节点)的下一个位置，**关键是确定最大节点的下一个位置，此处是将end()设置为nullptr，但其实是有缺陷的，当end()进行--运算时会找不到上一个位置，或者说操作更麻烦。最佳的办法是在红黑树的基础上设立一个头结点，头节点的左指向数的最左节点，右指向最右节点，对于begin和end的操作会变得更简便，但对于红黑树的维护变得更麻烦，此处暂且使用end为nullptr的方案。

## operator++与operator--

++遵循左子树，根，右子树的顺序，而--则相反，即右子树，根，左子树

~~~C++
//暂且先不看函数名，重点是实现
Self& operator++()
{
	if (node_->right_)
	{
		//右树存在时，下一个节点就是右树的最左节点
		Node* nextNode = node_->right_;
		while (nextNode->left_)
		{
			nextNode = nextNode->left_;
		}
		node_ = nextNode;
	}
	else
	{
		//右子树为空的情况下，下一个节点是cur为parent的左孩子时的parent
		Node* cur = node_;
		Node* parent = cur->parent_;
		while (parent)
		{
			if (cur == parent->left_)
			{
				break;
			}

			else
			{
				cur = parent;
				parent = cur->parent_;
			}
		}
		node_ = parent;
	}
	return *this;
}

Self& operator--()
{
	if (node_->left_)
	{
		//左子树存在时，上一个节点就是左子树的最右节点
		Node* nextNode = node_;
		while (nextNode->right_)
		{
			nextNode = nextNode->right_;
		}
		node_ = nextNode;
	}
	else
	{
		//左子树为空的情况下，下一个节点是cur为parent的右孩子时的parent
		Node* cur = node_;
		Node* parent = cur->parent_;
		while (parent)
		{
			if (cur == parent->right_)
			{
				break;
			}
			else
			{
				cur = parent;
				parent = cur->parent_;
			}
		}
		node_ = parent;
	}
	return *this;
}
~~~

# 红黑树的改造

**已知set与map的底层都是红黑树，但是set是k型而map是k&v型，需要进行一定的改造**

~~~C++
//keyofT是用于区分k型以及k&v型数据的仿函数
//简言之就是将k中的k以及k&v中的k取出来的仿函数
//使得map与set能使用同一个红黑树
//该仿函数需要map与set分别传入，而不是用户传入
template<class K, class T, class keyOfT>
class RBTree
{
	typedef RBTreeNode<T> Node;
public:
	typedef _RBTreeIterator<T, T*, T&> iterator;
	typedef _RBTreeIterator<T, const T*, const T&> const_iterator;
    //反向迭代器可以尝试自行实现
	//typedef _RBTreeReverseIterator< T, T*, T&> reverse_iterator;

    //迭代器部分
	iterator begin()
	{
		Node* farLeft = root_;
		while (farLeft && farLeft->left_)
		{
			farLeft = farLeft->left_;
		}
		return iterator(farLeft);
	}
	iterator end()
	{
		return iterator(nullptr);
	}
	const_iterator begin() const
	{
		Node* farLeft = root_;
		while (farLeft && farLeft->left_)
		{
			farLeft = farLeft->left_;
		}
		return const_iterator(farLeft);
	}
	const_iterator end() const
	{
		return const_iterator(nullptr);
	}
    
public:
    //查找
	Node* Find(const K& key) const
	{
		//...
	}

    //插入的内部是没有任何改变的
    //主要是返回类型为了跟库函数一致进行了修改
    //原先只是返回布尔值，现在还需要返回其插入位置的迭代器
	std::pair<iterator, bool> Insert(const T& data)
	{
		//...
	}
    
    //旋转
	void RotateL(Node* parent)
	{
		//...
	}
	void RotateR(Node* parent)
	{
		//...
	}
    
    //高度
	int Hight()
	{
		return Hight(root_);
	}
	//验证
	bool IsBalance()
	{
		return IsBalance(root_);
	}

private:
	bool IsBalance(Node* root)
	{
		//...
	}
	bool CheakColour(Node* root, int blackNum, int benchmark)
	{
		//...
	}
	int Hight(Node* root)
	{
		//...
	}
private:
	Node* root_ = nullptr;
public:
	size_t size_ = 0;
};
~~~

# set的模拟实现

主要代码如下：

~~~C++
//命名空间避免与库中的set冲突
namespace MyStd
{
	template<class K>
	class set
	{
        //对应keyofT的仿函数
        //其实set主要是为了跟map达成一致才进行了这一层封装
        //set中value就是k，只不过没有此层封装会显得不统一
		struct SetKeyOfT
		{
			const K& operator()(const K& key)
			{
				return key;
			}
		};
		//不论是iterator还是const_iterator，都是const类型
        //因为set中的value即key是不能修改的
		typedef typename RBTree<K, K, SetKeyOfT>::const_iterator iterator;
		typedef typename RBTree<K, K, SetKeyOfT>::const_iterator const_iterator;

	public:
        //这里的iterator实际上是const_iterator
        //只须定义一种即可，本身都是const_iterator
		iterator begin() const
		{
			return t_.begin();
		}
		iterator end() const
		{
			return t_.end();
		}
		std::pair<iterator, bool> Insert(const K& key)
		{
            //Insert的返回类型是普通类型的iterator，而set中的iterator是const类型，不能直接接收
            //先用一个普通的类型接受后
            //再用普通类型去构建const类型（此处需要迭代器添加一个构造函数）
			std::pair<typename RBTree<K, K, SetKeyOfT>::iterator, bool> ret = t_.Insert(key);
			return std::pair<iterator, bool>(ret.first, ret.second);
		}
	private:
		RBTree<K, K, SetKeyOfT> t_;
	};
}
~~~

# map的模拟实现

主要代码如下：

~~~C++
//命名空间避免与库中的map冲突
namespace MyStd
{
	template<class K, class V>
	class map
	{
        //对应keyofT的仿函数
        //主要是要把用于比较的key从pair中取出来
		struct MapKeyOfT
		{
			const K& operator()(const std::pair<const K, V>& kv)
			{
				return kv.first;
			}
		};
        //迭代器分为普通和const类型
        //但是k都是const类型
		typedef typename RBTree<K, std::pair<const K, V>, MapKeyOfT>::iterator iterator;
		typedef typename RBTree<K, std::pair<const K, V>, MapKeyOfT>::const_iterator const_iterator;
	public:
		iterator begin()
		{
			return t_.begin();
		}
		iterator end()
		{
			return t_.end();
		}
		const_iterator begin() const
		{
			return t_.begin();
		}
		const_iterator end() const
		{
			return t_.end();
		}
        //map的运算符重载
        //[]内是已有元素就返回value的引用
        //[]内是没有的元素就先插入再返回value的引用
		V& operator[](const K& key)
		{
            //红黑树的Insert中就需要一个find来进行查找是否已经存在当前插入的元素
			std::pair<iterator, bool> ret = insert(std::make_pair(key, V()));
            //Insert返回类型为pair<iterator,bool>一方面也是为了此处
			return ret.first->second;
		}
		std::pair<iterator,bool> Insert(const std::pair<K, V>& kv)
		{
			return t_.Insert(kv);
		}
	private:
		

		RBTree<K, std::pair<const K, V>, MapKeyOfT> t_;
	};
}
~~~

*由于红黑树没有添加删除功能，因而map与set中也没有erase功能，可以自行进行一下尝试*

本篇博客的代码参考：[map&set](https://github.com/Yukii2333/Daily-Code/tree/main/Works/Work_cpp/2023_09_13_set%26map)
