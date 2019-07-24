---
title: 【高质量C++/C总结9】复数类complex解析
date: 2019-01-04 19:08:27
tags:
categories: 高质量C++/C总结
---

## 说在开始：

我提炼了《C++ Primer》、《侯捷C++》、《高质量程序设计指南——C/C++语言》等资料中的重要部分，并总结成此博文。其中涉及到许多我个人对C++的理解，如若有不合理之处，还请朋友们多多指出，我会虚心接受每一个建议。同时，我将实现代码放到了我的GitHub上<https://github.com/ModestBean/C-Samples>，可供下载参考。
<!-- more -->
## complex代码

complex.h

```
#ifndef COMPLEX_H
#define COMPLEX_H
#include <iostream>
using namespace std;
class complex
{
public:
	complex(double r = 0, double i = 0) :re(r), im(i) {};
	complex& operator += (const complex&);
	double real() const { return re; }
	double imag() const { return im; }
private:
	double re, im;
	friend complex&  _doapl(complex*, const complex&);
};
inline complex&
_doapl(complex* ths, const complex& r) {
	ths->re += r.re;
	ths->im += r.im;
	return *ths;
}
inline complex&
complex::operator += (const complex& r) {
	return _doapl(this, r);
}
inline double
imag(const complex& x)
{
	return x.imag();
}
inline double
real(const complex& x)
{
	return x.real();
}
ostream&
operator << (ostream& os, const complex& x)
{
	return os << '(' << real(x) << ',' << imag(x) << ')';
}
#endif
```

main.cpp

```
#include "complex.h"
int main() {
	complex c1(2, 1);
	complex c2(5);
	printf("%d", sizeof(c1));
	c2 += c1;
	cout << c2;
	cin.get();
}
```

## 涉及到的细节问题

定义部分

	1. 在头文件中防御式常数定义
	2. class的head，在body里面开始做动作
	3. 数据应当放在private里面，实部和虚部
	4. 对外发表的函数应当放在public当中，构造函数要不要默认值，参数值考虑是pass by value还是pass by reference
	5. 构造函数的初始列，将实部虚部设置为初始值
	6. 考量构造函数还需要做什么事情。也就是{}括号中的内容
	7. 设计复数类应该支持什么样的能力，这里设计了+=的能力。函数有两种选择：成员函数和非成员函数。
	8. 设计一个取得其private成员的public函数。函数的类型需要考虑要不要加const，如果函数当中不会改变data的时候应该使用const，这里只是取出实部和虚部，所以这里加上了const
	9. 定义一个friend函数，因为要直接取得复数类的私有成员。

函数的实现

	10. 因为属于class里面的，所以操作符的重载需要写成complex:: operator +=类型的。
	11. 然后思考这个函数的参数是什么，成员函数中的参数都会有一个隐藏的this指针，这个this指针会优先传递成员函数中。对于不在修改的参数应该加上const。
	12. 考虑成员函数的返回内容，最好传递reference，如果传递出去的东西不是local object，尽量去传递reference
	13. 考虑函数是不是可以变成inline函数，能不能成为内联函数不知道，只有编译器才可以做出最终的决定。
	14. 加的动作不是本身成员函数中去做的，而是交给另一个函数去做的。
	15. 另外附属一些动作都可以设计为非成员函数，也就是全局函数。为什么不把+这个动作设计成成员函数呢 ？需要注意的是，复数不仅可以+复数，也可以加float，int，如果只把+这个动作放到class中，只能完成复数与复数的相加
	16. 左边的参数+右边的参数   会生成一个local的对象，这时候函数不能在传递引用。当函数执行完毕后，对应的对象也会销毁，这里应当提起重视。应该return by value。
	17. 这里有一个新的语法。class的名称加上小括号。例如：complex();此就是创建了一个临时的object。
	特殊的操作符重载 <<
	18. 因为要输出复数的形式，简单的<<操作符无法达到要求了，在C++标准库当中对此操作符<<进行重载。
	19. 操作符重载有两种方式：成员函数和非成员函数 。
	20. 使用者可能使用连串的输出cout<<c1<<endl，如果return by value 就会出现错误，此时应该使用return by reference