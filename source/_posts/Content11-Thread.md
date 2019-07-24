---
title: 【高质量C++/C总结11】C++线程的基本使用方式
date: 2019-05-16 22:15:37
tags:
categories: 高质量C++/C总结
---

## 说在开头
我提炼了《C++ Primer》、《侯捷C++》、《高质量程序设计指南——C/C++语言》等资料中的重要部分，并总结成此博文。其中涉及到许多我个人对C++的理解，如若有不合理之处，还请朋友们多多指出，我会虚心接受每一个建议。同时，我将实现代码放到了我的GitHub上<https://github.com/ModestBean/C-Samples>，可供下载参考。

<!--more-->
## 代码部分
ThreadJoin.cpp
这一个案例解释了线程中join方法的实例，简单的来说就是将t1线程加在主线程之前，先让t1线程执行。
```
#include <iostream>
#include <thread>
#include <chrono>
#include <mutex>   
#include <string>
/*
C++的线程案例，参考C++ Reference
1、添加join后可以保证线程的内容执行完毕，不会在主线程结束后，自定义线程中的打印内容还没有执行完毕
*/
using namespace std;
void printThread(string s) {//线程打印方法
	std::this_thread::sleep_for(std::chrono::milliseconds(30));
	for (int i = 0; i < 50; i++) {
		cout << s;
	}
	cout << "\n";
}
int main() {
	cout << "主线程开始" << endl;
	thread t1(printThread, "*");//创建线程t1
	t1.join();
	cout << "主线程结束" << endl;
	std::cin.get();
}

```

![这里写图片描述](//img-blog.csdn.net/20180317145825882?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L01vZGVzdEJlYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
ThreadMutex.cpp

```
#include <iostream>
#include <thread>
#include <chrono>
#include <mutex>   
#include <string>
/*
C++的线程案例，参考C++ Reference

1、打印方法结束后，如果不解锁，那么第二个线程的打印是无法进行的。
2、加锁后出现的结果为
*****************************
&&&&&&&&&&&&&&&&&&&&&&&&&&&&&

不加锁的结果可能为
************&&&&&&&&&&&&********
******&&&&&&&&&
不加锁不会保持线程的同步
*/
std::mutex myLock;//资源锁
using namespace std;
void printThread(string s) {//线程打印方法
	myLock.lock();
	std::this_thread::sleep_for(std::chrono::milliseconds(30));
	for (int i = 0; i < 50; i++) {
		cout << s;
	}
	cout << "\n";
	myLock.unlock();
}
void printThread2(string s) {//线程打印方法
	myLock.lock();
	for (int i = 0; i < 50; i++) {
		cout << s;
	}
	cout << "\n";
	myLock.unlock();
}
int main() {
	cout << "主线程开始" << endl;
	thread t1(printThread,"*");//创建线程t1
	thread t2(printThread2, "&");//创建线程t2
	t1.join();
	t2.join();
	cout << "主线程结束" << endl;
	std::cin.get();
}

```

![这里写图片描述](//img-blog.csdn.net/20180317145959829?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L01vZGVzdEJlYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果您喜欢C++，喜欢3D，并且想了解一点深度学习的知识，加入我们的QQ群，群里面全都是年轻的开发者，欢迎大家的加入讨论。就说是CSDN过来的就行。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190421221222141.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01vZGVzdEJlYW4=,size_16,color_FFFFFF,t_70)