---
title: 【高质量C++/C总结3】泛型编程——Template模板
date: 2018-12-12 20:38:04
tags:
categories: 高质量C++/C总结
---
## 说在开始：

我提炼了《C++ Primer》、《侯捷C++》、《高质量程序设计指南——C/C++语言》等资料中的重要部分，并总结成此博文。其中涉及到许多我个人对C++的理解，如若有不合理之处，还请朋友们多多指出，我会虚心接受每一个建议。同时，我将实现代码放到了我的GitHub上<https://github.com/ModestBean/C-Samples>，可供下载参考。
<!-- more -->
## 内容介绍

Template是C++泛型编程中非常重要的一环，Template常用在泛型类和函数中。在C++标准库中也经常看到Template的身影。可以说，Template是一个优秀的C++框架中重要的一环，例如那些优秀的C++框架，OpenGL，OpenCV，STL等等。Template是一非常大的话题，该篇博文只会让您对Template产生基本的认识，要想熟练掌握，需要修读更多的书籍和资料。

那么Template为什么会这么重要？首先是一个具体的案例，接着在分析一下algorithm中的max源码，这样可以对Template有更深的体会。

在开始之前我们思考一个我们经常遇到的情况，看一下以下代码。

```
int a = 2, b = 3;
int c = max(a, b);
float af = 2.0f, bf = 3.0f;
float cf = max(af, bf);

stack(int a) s1;//存放int类型数据的栈
stack(float b) s2;//存放float类型数据的栈
```
1、要想实现以上功能需要写几个max函数？

2、要想实现以上功能需要写几个类？

在没有函数模板时，想必只能通过此种方式完成：
```
int max(int a, int b)
{
	if (a > b) { return a; }else { return b; }
}
float max(float a, float b)
{
	if (a > b) { return a; }else { return b; }
}
class stack
{
    public:
        stack(int a)
}
class stack2
{
    public:
        stack(float a)
}
```

在写程序时，一定要注意，当频繁使用ctrl+c和ctrl+v时往往都不是啥好事。上面的代码会有以下几种问题：（1）代码较长，抒写时耗费时间。（2）需求改变时，修改不方便，这里不在多提怎么不方便了。每个函数都需要修改一遍，傻子都不喜欢做吧。

模板就是为了解决此种情况提出的。

## 使用方法

以以下此段代码为例。

```
template <typename T>
T  add(T  a, T  b) {
	return a + b;
}
```

T可以是任何名称，不过我观察大部分C++开源库中都使用的是T。这里T你可以想象成是一个类型，与int相似。

## 函数模板

```
//FunctionTemplate.cpp
template <typename T>
T const& add(T const& a, T const& b) {//创建函数模板
	return a + b;
}
int main() {
	int i1 = add(1, 2);
	float f2 = add(3.3f, 4.2f);
	double d3 = add(2,2);
	std::cout << i1 << std::endl;
	std::cout << f2 << std::endl;
	std::cout << d3 << std::endl;
	std::cin.get();
}
```
通过此种方式，add函数可接受任何类型的参数。

## 类模板

```
//ClassTemplate.h
#ifndef CLASSTEMPLATE
#define CLASSTEMPLATE
template <class T>
class ClassTemplate
{
public:
	ClassTemplate(T const& widthIn, T const& heightIn)
		: width(widthIn), height(heightIn)
	{
	}
	T const& CalculateArea();
private:
	T width;
	T height;
};

template <class T>
T const&  ClassTemplate<T>::CalculateArea() {
	return width * height;
}
#endif // !CLASSTEMPLATE
```
```
//main.cpp
#include <iostream>
#include "ClassTemplate.h"
using namespace std;
int main() {
	ClassTemplate<int> s1(1,2);
	ClassTemplate<float> s2(1.0f, 2.0f);
	int area=s1.CalculateArea();
	float areaf = s2.CalculateArea();
	cout << "计算出的int面积是" << area<< "计算出的float面积是" << areaf << endl;
	std::cin.get();
}
```

在main.cpp代码ClassTemplate<int> s1(1,2);ClassTemplate<float> s2(1.0f, 2.0f);中可以看出，接收了不同的参数。当然你可以使用此种方式构造栈。







