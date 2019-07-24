---
title: 【高质量C++/C总结1】高级数据类型枚举Enum
date: 2018-11-24 19:21:21
tags:
categories: 高质量C++/C总结
---
## 说在开始：

我提炼了《C++ Primer》、《侯捷C++》、《高质量程序设计指南——C/C++语言》等资料中的重要部分，并总结成此博文。其中涉及到许多我个人对C++的理解，如若有不合理之处，还请朋友们多多指出，我会虚心接受每一个建议。同时，我将实现代码放到了我的GitHub上<https://github.com/ModestBean/C-Samples>，可供下载参考。
<!-- more -->
## 内容介绍
C++/C枚举是一组特定的符号变量，通常表示一系列相关内容，例如每周Week，Monday，Tuesday，Wednesday等。
```
enum Week{Monday,Tuesday,Wednesday,....,Sunday}
//使用枚举
Week oneDay=Monday;
```
还可表示运动内容，例如Sport，Basketball，Football，Pingpang等。
```
enum Sport{Basketball,Football,Pingpang}
//使用枚举
Sport likeSport=Basketball;
```
 - （1）枚举元素具有默认值，默认情况下为0，1，2.......，以Week为例，Monday=0，Tuesday=1，Wednesday=2。
 - （2）如若不想使用默认值，需在初始化时进行赋值 例如enum Sport{Basketball=2,Football=1,Pingpang=3}，个人认为此种情况很少用到，开发者关心的是符号变量的名称，很少关心其值为多少，在后面的编程例子中有所体现。
 - （3）枚举类型可以转换为整型，但整型不一定可以强制转换为枚举类型。整型数值是连续的，Monday=0，Tuesday=1，也就是无论怎么转换都只是0~6之间的数值，而你要把数字10转换成Week中的成员，想必编译器也不知如何去做吧。
## 枚举enum的使用场合，为什么使用枚举？
此处我列举一个场景。很好理解：学校需要记录每人的信息，那么会有Person类，其中包括一项为报道所在周几，通常我们的编程习惯是这样的。
```
//定义星期
const int Monday = 0;
const int Tuesday = 1;
const int Wednesday = 2;
class Person {
public:
	Person(const int inArriveDate) {
		this->arriveDate = inArriveDate;
	}
	int arriveDate;
};
int main() {
	Person p(Monday);
	printf("%d", p.arriveDate);
	return 0;
}
```
有些人的编程习惯更坏，可能是这样的。这是新手编程时经常范的错误，大家注意。 不要出现神奇数字0,1,2,3,4.要有意义。
```
class Person {
public:
	Person(int inArriveDate) {
		this->arriveDate = inArriveDate;
	}
	int arriveDate;
};
int main() {
	Person p(0);
	if (p.arriveDate == 0) {
		printf("星期一");
	}
	else if (p.arriveDate == 1) {
		printf("星期2");
	}
	
	return 0;
}
```
使用枚举enum后可以大大减少工作量，省去了开发人员赋值的过程，代码简洁，清晰，长度短，使用枚举后。
```
//定义枚举类型
enum  Week { Monday, Tuesday, Wednesday };
class Person {
public:
	Person(Week inArriveDate) {
		this->arriveDate = inArriveDate;
	}
	Week arriveDate;
};
int main() {
	Person p(Monday);
	return 0;
}
```

此处我只是想到了一种使用场景，其他情况，欢迎大家交流，在开发过程中，我们一定要多思考，不能一概而过。
## 其他代码实现
```
#include<iostream>
using namespace std;
enum  GameResult{//定义枚举类型
	WIN,LOSE,CANEL
};
int main() {
	GameResult result;
	for (int i = WIN; i <= CANEL; i++) {
		result = GameResult(i);//强制类型转换
		if (result == WIN) {
			cout << "WIN";
		}
		if (result == LOSE) {
			cout << "LOSE" ;
		}
		if (result == CANEL) {
			cout << "CANEL";
		}
	}
	cout <<  endl;
	return 0;
}

```