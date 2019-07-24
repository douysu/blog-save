---
title: 【高质量C++/C总结5】const常用用法-提高程序的健壮性
date: 2018-12-14 16:23:09
tags:
categories: 高质量C++/C总结
---

## 说在开始：

我提炼了《C++ Primer》、《侯捷C++》、《高质量程序设计指南——C/C++语言》等资料中的重要部分，并总结成此博文。其中涉及到许多我个人对C++的理解，如若有不合理之处，还请朋友们多多指出，我会虚心接受每一个建议。同时，我将实现代码放到了我的GitHub上<https://github.com/ModestBean/C-Samples>，可供下载参考。
<!-- more -->
## 内容介绍

本篇博客需要对C++的指针传递、引用传递、值传递，字符串，字符数组有一些认识。

const除了const常量的用法以外，其还有其他三方面的作用（1）保护函数入口参数（2）保护函数返回值（3）保护类数据成员不被修改。

## 使用const修饰函数参数

### 指针传递：

共三种用法，注意观察const的位置。

```
void pointer_print(const char* src);//const在char*之前，无法修改指针指向的内存单元内容
void pointer_print(char* const src);//const在char*之后，无法修改指针的指向
void pointer_print(const char* const src);////前后都有，两者综合
```

### 值传递：

实参传递时，函数自动拷贝一份实参放在堆栈，为形参。在函数内改变值改变的都是形参，不会对实参产生影响，一般认为不需要使用const。当然程序中需要避免值的修改也可以使用const。

### 引用传递：

值传递时如果传递的为对象，会设计到对象的构造函数，拷贝等等一系列复杂的操作，因此传递引用，同时为了防止引用改变，添加const。

```
//以下两种方式都是相同的。
void object_reference(const Object& in) {
	Object b;
	//in = b; 错误
}
void object_reference(Object const& in) {
	Object b;
	//in = b; 错误
}
```

基本数据类型就不用使用&了，因为不涉及构造，析构等。

## const修饰函数返回值

### 指针传递

```
const char*  getCharPointer  (char* in) {
	 return in;
}
int main()
{
	char c[] = "Hello";
	const char* newc = getCharPointer(c);
	cout << newc << endl;
	std::cin.get();
}
```
必须在变量newc前面加上const才可以编译通过。

### 值传递

一般情况下，编译器会把返回值放到临时的存储单元中，通常加const没有实际意义。

### 引用传递


```
const Object& getObjectReference(Object& ob)
{
	return ob;
}
```

如若没有加const，那么以下此种方式是正常运行的，当添加const后，提示错误，因为返回值是不允许重新赋值的。

```
	Object a;
	Object b = getObjectReference(a) = a;
```

其现实编程中很少见返回值使用引用传递，因为往往会涉及到一个容易忽视的问题。例如：

```
Object& returnObject()
{
    Object ob;
    return ob;
}
```
这段代码得到的将会垃圾值，因为在函数returnObject执行完以后，ob对象在栈中，编译器自动销毁，想必引用也是不存在的。

## 保护类数据成员不被修改

观察以下代码的getSquare方法，在其内部是无法修改width和height的，提高了程序的健壮性。

```
//Const_Function_Return.cpp
class Rect
{
public:
	Rect(int inWidth, int inHeight) :width(inWidth), height(inHeight) {};
	int getSquare() const 
	{
		//width = 2; 无法编译，试图修改成员变量
		return width * height;
	}
private:
	int width, height;
};

int main()
{
	Rect r(3, 4);
	int square = r.getSquare();
	cout << square <<  endl;
	cin.get();
	return 0;
}
```

