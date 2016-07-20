---
layout: post
title: "Iterator Pattern"
description: ""
category: ['Engineer']
tags: [design pattern, software engineer]
date: 2016-02-17T00:03:08+08:00
---

I decide not take essay on Adaptor, Strategy, Template and Compound Pattern, which I think is quite easy to understand, or even, should be a base knowledge when learn OO programming. Iterator pattern is a form of pattern which is best for traverse the data structure inside the object. One may recall that in C++, each standard template library, STL, has a iterator. The iterators are divided into random iterator, input iterator, forward iterator and bidirectional iterator. For structures like **list, vector, tree**, have iterator structure, implemented for users to access the inner data. You don't need to think Iterator as a very complex concept. Just have a look at the follow example.

```cpp
class iterator{
public:
	virtual iterator& next() = 0;
	virtual bool has_next() = 0;
private:

}
```

This is an iterator base class. You need to implement **next** and **has_next** methods. For example you want to write a class called context in a 3D game. In every epoch, you will need to render all the object. You can write an iterator and encapsulate all the draw routine into a new interface **draw** in the parent class **iterator**. Then with a vector or list structure, you can use only one line of code to traverse all the object in context.

In STL, the iterator use template. In the implementation file of STL, you can find the iterator defined in each structure. It's quite effecient as you may find the code clean and easy to understand using iterator.

Iterator can be regarded as designed for Sequence Model in which the data unit can be lined up as a sequence. However, you may have a tree structure waiting for abstraction. Here you need Composition Pattern. This is a principle that **better to composite**, not inheritance. With composition, you can easily put all the sub tree into the structure. This make the view that each object can have inner object with the same abstraction like a tree traverse recursively.