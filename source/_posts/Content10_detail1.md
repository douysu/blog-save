---
title: 【高质量C++/C总结10】C++实践细节总结1
date: 2019-01-14 17:36:10
tags:
categories: 高质量C++/C总结
---

## 说在开始

这里总结了我个人在编程过程中注意到的C++细节，在这里进行总结。
<!-- more -->
### 总结篇

1、数据最好使用private作用域，这也是面向对象语言需要注意的特点之一。
```
class RectAngle{
    public:
        RectAngle();
    private:
        int width;//属性，不想暴露给其他类
        int height;//属性不想暴露给其他类
}
```
2.、参数尽量使用引用来传递，如果不想改变参数的值可以加上const。不在像C语言中传递值了，传递value会传递全部Byte，所占资源比较多，推荐使用 const &，传递int，float等基本数据类型就没有必要使用引用了，因为不涉及到对象的拷贝构造。

```
void calByte(const VaAndRef& sample) {//不期望对值进行修改的时候应该使用const
    cout <<sizeof(sample)<< endl;
}
int main() {
    VaAndRef sample(1,2);
    calByte(sample);
    cin.get();
}
```
3、返回值尽量也要传递引用而不是传递值，当然需要注意避免传递返回栈内存。

4、在定义函数的时候，如果对成员变量没有进行修改，应该加上const，如果不加，class的使用者，创建一个const的对象时候就会出现错误，当然有的编译器是提示错误的，有的编译器不会去提示。

```
#ifndef RECTANGLE_H
#define RECTANGLE_H
class Rectangle
{
public:
    Rectangle(int widthIn, int heightIn) :width(widthIn), height(heightIn) {};
    Rectangle();
    int getWidth() const { return width; }
    int getHeight() const { return height; }
private:
    int width;
    int height;
};
#endif
//类的使用者可能会如此定义变量
const Rectangle re;
//不加const无法继续执行getWidth()方法。
```
5、构造函数尽量去使用初始化的方法，省去了赋值等操作阶段，非常快捷。这是体现一个C++程序员的地方，看一个人的C++水平如何，看这里就可以看出。
建议的写法为：

```
class VaAndRef
{
public:
    VaAndRef(int aIn, int bIn) :a(aIn), b(bIn) {}
private:
    int a;
    int b;
};
```
这种写法可以直接将变量初始化，不在进行变量的赋值，提高了程序的效率。

6、能使用内联函数的一定要使用内联函数，当然是否使用内联函数是由编译器决定的， 编译器如果认为此段代码可以定义为内联函数，那么就会将此函数变成内联函数。

经过多次查看源码和测试，我发现内联函数的定义和实现都必须在头文件中。

