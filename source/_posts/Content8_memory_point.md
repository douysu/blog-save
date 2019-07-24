---
title: 【高质量C++/C总结8】内存管理——指针参数传递内存
date: 2018-12-30 10:24:58
tags:
categories: 高质量C++/C总结
---

## 说在开始：

我提炼了《C++ Primer》、《侯捷C++》、《高质量程序设计指南——C/C++语言》等资料中的重要部分，并总结成此博文。其中涉及到许多我个人对C++的理解，如若有不合理之处，还请朋友们多多指出，我会虚心接受每一个建议。同时，我将实现代码放到了我的GitHub上<https://github.com/ModestBean/C-Samples>，可供下载参考。
<!-- more -->
## 使用函数参数动态申请内存时易出现的问题

这里讲解一下网上常出现的GetMemory()方法，经常被作为考察C++功力的面试题。

```
//错误代码
void GetMemory(char *p) {
	p = (char*)malloc(sizeof(char) * 100);
}
int main() 
{
	char *str = NULL;
	GetMemory(str);
	strcpy_s(str,100, "Hello world ");//运行错误
	cout << str << endl;
	cin.get();
}
```

如上面的错误代码，在函数的参数为指针时，千万别使用该指针去申请动态内存。为什么会出现这个问题呢？

这就形参_p和实参p的问题了。编译器会对函数每个参数拷贝一个副本，也就是_p。这里是形参_p指向了新申请的动态内存，而实参p并没有指向内存，在方法GetMemory执行结束后，_p自动销毁，那么申请的内存就变成了垃圾内存，每运行一次GetMemory，就会泄漏一块内存。所以上面的代码是错误的。

可以改写成指针的指针来动态申请，如下。

```
//正确代码方式1——指针的指针
void GetMemory2(char **p) {
	*p = (char*)malloc(sizeof(char) * 100);
}
int main() 
{
	char *str = NULL;
	GetMemory2(&str);
	strcpy_s(str,100, "Hello world ");
	cout << str << endl;
	cin.get();
}
```

指针的指针有时理解起来会有困难，建议大家使用此种方案。 

```
//正确方式2——返回值
char* GetMemory3() {
	char* p = (char*)malloc(sizeof(char) * 100);
	return p;
}
int main()
{
	char *str = NULL;
	str=GetMemory3();
	strcpy_s(str, 100, "Hello world ");
	cout << str << endl;
	cin.get();
}                                                                                                  ```   

这里需要注意，千万不要在GetMemory方法中返回栈内存的内容。例如：

```
//错误示例
char* GetMemory() {
	char[] p="Hello";
	return p;
}
//存在问题的示例
char* GetMemory() {
	char* p="Hello";
	return p;
}
```

在第一个方法中字符数组在栈中，方法执行完后，自动释放，返回指针和引用都是无意义的。

第二个方法可以正确运行，但是不建议这样做。因为这里的Hello是一个常量字符串，在静态存储区，在程序生命期内始终存在，无论什么时候，返回的都是同一块内存的内容，最好不要修改该内存的内容，可以使用const进行修饰防止出现修改错误。



