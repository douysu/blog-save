---
title: 【高质量C++/C总结4】C++常量总结-const
date: 2018-12-13 16:07:33
tags:
categories: 高质量C++/C总结
---

## 说在开始：

我提炼了《C++ Primer》、《侯捷C++》、《高质量程序设计指南——C/C++语言》等资料中的重要部分，并总结成此博文。其中涉及到许多我个人对C++的理解，如若有不合理之处，还请朋友们多多指出，我会虚心接受每一个建议。同时，我将实现代码放到了我的GitHub上<https://github.com/ModestBean/C-Samples>，可供下载参考。
<!-- more -->
## 内容介绍

C++/C的常量分为两种，分别为字面常量和符号常量。

字面常量：程序中出现的各种进制的字符，数字，地址等等都是字符常量，这在不过多介绍。有一点需要注意，一段程序中可能存在相同的常量，这样就增加的内存的消耗，有的编译器是支持变量合并的，如果编译器支持最好打开，可以减少内存消耗。

符号常量：符号常量分为（1）宏（2）const定义的常量。  宏在程序编译前就进行了代码替换，说到底宏就是一种特殊的字面常量。这里着重介绍const修饰的常量。

## 如何定义常量

```
	const int WIDTH = 20;
	const int HEIGHT = 20;
        #define SKY_LENGTH = 10;
```
在《Google C++编程规范》中，严格规定C++/C中的常量应当全部大写，容易区分。

在C++程序中应尽量使用const修饰常量，在后面的博客中还会介绍const的一些其他用途，只要需要预防数据修改时都可以使用const，例如const修饰函数参数，const修饰成员函数等等。正如大家所说的“Use const whenever you need”。

## const较#define的优势

const的变量相比#define宏具有很多优势。

（1）首先#define宏在程序中往往会造成不可预知的错误，因为宏只是进行字符替换，不会进行类型的安全检查，并且字符替换时可能会产生意料不到的逻辑错误。

（2）其次，#define宏在程序中是不可调试的，也就是无法使用debug中的中断进行调试，但是可以对const常量调试。

## const常量的一些细节

（1）const类型常量内存分配情况

在C中，const定义的常量是不能修改的常量，因此会为其分配内存空间。在C++中，情况会有相应的改变，对于基本数据类型，如int，float。编译器会把其放到符号表中，因此不需要分配内存空间，而对于抽象数据类型和自定义的数据类型是需要分配内存空间的。

（2）const常量在运行时，可通过内存地址修改其值

如何理解这句话呢，看下面这段代码吧。
```
//Const_Changed.cpp
class Data
{
public:
	Data(int dIn):d_int(dIn) {};
	int d_int;
};

int main() {
	const int WIDTH = 20;
	int* p = (int *)&WIDTH;
	cout << WIDTH << endl;//20
	*p = 10;
	cout << WIDTH << endl;//20


	const Data data(20);
	Data* d = (Data*)&data;//获取指针
	cout << data.d_int << endl;//20
	d->d_int = 10;
	cout << data.d_int << endl;//10
	cin.get();
}
```

通过以上代码可以看出：

 - 对于基本数据类型常量，编译器会重新再内存中创建一份拷贝，当通过指针去修改其值时，修改的只是其拷贝，并非是原始数据。

 - 但是，对于自定义类的const对象，可以通过获取指针的方式修改d_int值，也就是迂回修改。如若想防止此种情况产生，很简单，只需要在d_int声明前面加上一个const即可。  如若那么做，还会引出一个新的问题，这个在下面会介绍。

 也就是说const符号只是编译时进行检查，来帮助程序员无意中会修改它的代码，而在运行时无法防止恶意的修改的。C++并没有提供指针的安全检查，这也就是C++指针危险性的，在后面中的内存管理等等地方还会更加多涉及到指针，也就是说，一个好的C++/C程序员一定会严格要求对指针的操作。

## 类中的非静态常量

类的静态常量在最后一部分提到。在上面提到，如果不想通过指针修改d_int变量，可以在其前面加上一个const，这里的const int d_int就是类中的常量，这里有几点需要注意。

（1）非静态的const数据成员是属于每一个类对象的。

（2）不能再类中初始化非静态常量，只能在类的构造函数的初始化列表中进行。只能通过此种方式初始化。

```
//Const_Class.h
class Const_Class
{
public:
	Const_Class(int input) :c_int(input) {};
	const int c_int;
};
```
如若想初始化，只能使用枚举enum了。

## 实际编程中的应用

常量在项目中经常被使用，但是其值几乎不变，通常是一些常用的属性，例如在吃鸡中，地图的大小，一局游戏的时间等等。

在开始之前，有一点需要介绍，C++中const符号常量定义的默认连接类型为static，所以在头文件有无static都无所谓，但在C中不同。这里参考的为高质量程序设计一书。各种方法如下所列：

（1）直接在头文件中定义常量并初始化。如：

```
//ConstantDef.h
const int WIDTH = 12;

//main.cpp
#include <iostream>
#include "ConstantDef.h"
using namespace std;
int main()
{
	cout << WIDTH << endl;
	cin.get();
}
```

使用时只需要在相应编译单元include即可。

（2）在头文件中声明中添加关键字extern，在某个编译单元（cpp）中定义并初始化

在C++中声明和定义是完全两件不同的事情。C++语言支持分离式编译机制，该机制允许将程序分割为若干个文件，每个文件可被独立编译。为了将程序分为许多文件，则需要在文件中共享代码，例如一个文件的代码可能需要另一个文件中中定义的变量。

为了支持分离式编译，C++允许将声明和定义分离开来。变量的声明规定了变量的类型和名字，即使一个名字为程序所知，一个文件如果想使用别处定义的名字则必须包含对那个名字的声明。定义则负责创建与名字关联的实体，定义还申请存储空间。

参考：extern关键字的作用<http://www.cnblogs.com/broglie/p/5524932.html>

```
//ConstantDef.h
extern const int WIDTH;//仅声明变量

//ConstantDef.cpp
#include "ConstantDef.h"
extern const int WIDTH=12;//声明并定义

//main.cpp
#include <iostream>
#include "ConstantDef.h"
using namespace std;
int main()
{
	cout << WIDTH << endl;
	cin.get();
}
```
其实无论在哪个编译单元中声明并定义都是可以的，我这里方便，就只在头文件对应的编译单元定义实现了。

（3）如果是整型变量直接使用enum进行初始化即可。

（4）定义一个公用类，其中包括static const数据成员并初始化，或者定义枚举类型。

```
//Constant.h
class Constant
{
public:
	static const int ALL_TIME;
	enum
	{
		TIME = 20
	};
};

//Constant.cpp
#include "Constant.h"
const int Constant::ALL_TIME=20;

//main.cpp
#include <iostream>
#include "Constant.h"
using namespace std;
int main()
{
	cout << Constant::ALL_TIME << endl;
	cin.get();
}
```

## 几种常用方法的优缺点

### 方法二、四

 - 优点：
 
 （1）每一个单元访问的都是唯一的。怎么理解这句话呢？以方法一为例，在需要的地方include对应的头文件，这样相当于在每个编译单元上方又重新声明定义了一次常量，也就是说整个程序中存在多个同名的常量，只是在不同的编译单元罢了，而方法二和方法四在整个程序中只存在一个常量。
 
 （2）修改初值后只会影响自己的头文件和编译单元。  这也很好理解，方法一和三都是重新再头部声明了，也就是include的头文件的编译单元都会重新编译。

  - 缺点：
  维护不方面。当需要修改值时或者变量的名字时，不仅要修改头文件而且还要修改编译单元，需要修改两次。

### 方法一、三

 - 优点

 维护方便，只需要修改一处。

 - 缺点

 （1）修改值时会影响到许多编译单元，需要重新编译。

 （2）也就是上面所讲的情况，每一个编译单元中都会有拷贝的内容，浪费存储空间。
