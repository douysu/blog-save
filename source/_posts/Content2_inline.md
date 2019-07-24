---
title: '【高质量C++/C总结2】函数内联inline'
date: 2018-11-27 22:09:42
tags:
categories: 高质量C++/C总结
---
## 说在开始：

我提炼了《C++ Primer》、《侯捷C++》、《高质量程序设计指南——C/C++语言》等资料中的重要部分，并总结成此博文。其中涉及到许多我个人对C++的理解，如若有不合理之处，还请朋友们多多指出，我会虚心接受每一个建议。同时，我将实现代码放到了我的GitHub上<https://github.com/ModestBean/C-Samples>，可供下载参考。
<!-- more -->
## 内联函数inline
内联函数的实际目的是为了提高函数的执行效率，在条件允许的情况下，编译器会自动将内联函数中的代码直接替换函数调用语句，可以帮助减少函数调用开销：参数压栈、跳转、退栈、返回值等，提高了代码的执行速度。
此案例说明了这个问题。
```
int sumFunc(int x,int y);
inline int sumFunc(int x,int y)
{
	return (x+y);
}
int main()
{
	int sumV=sumFunc(2,3);
	return 0;
}
//被扩展成代码
int main()
{
	int sumV = 2+3;
	return 0;
}
```
## 内联函数inline的使用频率
为了查看inline在实际开发中的使用频率，我查阅了一些C++开源项目，发现inline的使用频率是非常高的，例如C++ STL algorithm中的max函数的源码,可以非常清楚的看到设计algorithm的人使用了inline。
```
ntemplate<typename _Tp>
_GLIBCXX14_CONSTEXPR
inline const _Tp&
max(const _Tp& __a, const _Tp& __b)
{
  // concept requirements
  __glibcxx_function_requires(_LessThanComparableConcept<_Tp>)
  //return  __a < __b ? __b : __a;
  if (__a < __b)
return __b;
  return __a;
}
```
## 使用内联函数inline的注意事项
（）内联函数的代码不应过长。内联函数是以代码拷贝为代价的，仅仅省去的函数调用的开销。试想，如果你的方法中的代码执行时间比函数调用过程花费的时间还长，那就没有必要在使用内联函数了。当然，函数体内出现多次循环或者其他复杂的结构：递归、排序、遍历等等，那内联函数意义也是微乎其微的。例如，这样的方法。
```
void funC(){
    for(int i=0;i<=20;i++)
        for(......)
            for(......)
}
```

（2）计算函数（代码长度较短）更加适合内联。例如计算圆的面积，圆的半径，min，max，sum，average等等，当在项目封装库的过程中，记得使用内联。当然C++设计师也是这样做的，例如min，max。

(3)inline必须与函数定义放在一起才会起作用。在C++中，有一非常重要的理念，函数的声明和定义是两大部分。通常函数的声明会放在头文件.h中，而函数的定义会放在可编译单元.cpp中。  在使用其他人写好的类时，只需要include相应的头文件，使用者无需关心该函数是否为内联函数，例如使用algorithm中的min函数一样，我们无需知道此函数是否为内联函数。

（4）在哪个cpp中定义内联，就只能在该cpp中使用。例如
```
//InlineSample.h
#ifndef NEILIANSAMPLE_H
#define NEILIANSAMPLE_H
class InlineSample
{
public:
	InlineSample();
	void setValue(float inValue);
	float value;
};
#endif

//InlineSample.cpp
#include "InlineSample.h"
InlineSample::InlineSample() {
}
inline void InlineSample::setValue(float inValue) {
	value = inValue;
} 

//main.cpp
#include "InlineSample.h"
#include<iostream>
using namespace std;
int main() {
	InlineSample ns;
	ns.setValue(2.0f);
	printf("当前的值%f", ns.value);
	cin.get();
}
```
此种情况会编译错误，因为在InlineSample.cpp中定义的inline，却想要在main.cpp中使用，显然是错误的。这也是我自己在开发中遇到的问题，需要注意。此时应将setValue方法的定义放到.h文件中，即可完成这种需求。
```
//InlineSample.h
#ifndef NEILIANSAMPLE_H
#define NEILIANSAMPLE_H
class InlineSample
{
public:
	InlineSample();
	void setValue(float inValue);
	float value;
};
inline void InlineSample::setValue(float inValue) {
	value = inValue;
}
#endif 
```
这种方式的实现原理：当include头文件InlineSample.h时，就将inline的定义代码拷贝到了相应的cpp，这样当然不会出现问题了，但是这与（3）中的说法好像有点相违背，看大家怎么选择了。


（5）inline在实现过程中并不是绝对的，其只是对编译器提出一个请求，编译器有完全的权利决定该函数是否被内联。如果此函数被内联对性能没有提升，那么就不会被编译器采纳。


