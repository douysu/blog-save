---
title: 【3D实践】3D曲率原理及计算（3D-Mesh）
date: 2019-04-21 15:17:13
tags:
categories: 3D图形学
---

查了一下中文文章，几乎没有一篇介绍曲率原理并且计算曲率的文章。首先总结了一下曲率的原理，参考的内容有：图形学书籍、维基，和GitHub的项目。

参考内容:
[1]<https://libigl.github.io/>

[2]<https://zh.wikipedia.org/wiki/%E6%9B%B2%E7%8E%87>

[3]<https://zh.wikipedia.org/wiki/PLY>

<!-- more -->

[4]<https://github.com/alecjacobson/geometry-processing-curvature>

[5]<http://meshlabstuff.blogspot.com/2010/03/mean-curvature-cavity-map-zbrush-and.html>

[6]<https://web.archive.org/web/20081203195143/http://www.cs.princeton.edu/~diego/professional/rply/>

[7]章毓晋.图像处理.北京:清华大学出版社,2018


## 曲率原理

什么是曲率？

曲率是描述几何体弯曲程度的量，例如三维曲面偏离平面的程度，或者二维曲线偏离直线的程度，也可确定曲面类型。常应用于几何分析，地理测绘等领域。

例如在材料学中，材料的催化活性主要与表面活性位点有关，而曲率影响活性位点的数量。

曲线P点切线：
曲线任意一点Q，PQ两点无限接近，所连直线为切线。

<img width=250 src="prac1-curvature/pic1.gif " >

曲线P点曲率：曲线任意一点𝑄_1, 𝑄_2  ，三点确定一圆, 𝑄_1, 𝑄_2  无限接近P点，密切圆半径r的倒数为曲率。

<img width=250 src="prac1-curvature/pic2.gif " >

<img width=250 src="prac1-curvature/pic3.png " >

平面结论：圆上弯曲程度相同，任意一点曲率相等，越弯曲曲率越大，直线曲率为0。

曲面曲率：
在曲面上取一点P，曲面在P点的法线为n，过n可以有无限多个剖切平面，每个剖切平面与曲面相交，交线为一条平面曲线。
不同的剖切平面上的平面曲线在P点的曲率半径一般是不相等的。

<img width=250 src="prac1-curvature/pic4.png " >

三维空间中的曲率：
主曲率：曲面上有无数个不同方向的曲线，曲面上的点不同方向具有不同曲率，其中最大值和最小值为称为主曲率 k1 和k2，极值方向称为主方向。数学上可证名k1和k2互相垂直。

高斯曲率：两主曲率乘积，反映曲面在不同方向弯曲程度是否相同。高斯曲率为正，为球面。高斯曲率为负双曲面。

平均曲率：两主曲率算数平均数(k1+k2)/2，反映曲面凹凸程度。平均曲率为正，局部凹。平均曲率为负，局部凸。

<img width=250 src="prac1-curvature/pic5.png " >

第一个图形的高斯曲率为负值，第二个为0，第三个为正数。

## 高斯曲率（G）和均值曲率（H）所确定表面类型：
通过高斯曲率和均值曲率的正负判断曲面类型。

 | H<0 | H=0 | H>0 
-|-|-|-
G<0 | 鞍脊 | 最小/迷向 | 鞍谷 
G=0 | 山脊 | 平面 | 山谷 
G>0 | 山峰 |  | 凹坑 

<img width=500 src="prac1-curvature/pic6.png " >

## 曲率的计算

通过一段时间的调研，我发现使用此两种方式计算曲率比较多。具体链接就不给出了，google一下就可以看到教程了案例了。

（1）C++图形学几何处理库libigl 

<img width=250 src="prac1-curvature/pic7.png " >

（2）三维几何处理系统MeshLab

<img width=250 src="prac1-curvature/pic8.png " >

## 本人使用的方式

我采用的第二种方式，使用MeshLab进行处理，得到曲率数据，保存到.ply文件中，在从.ply文件中提取曲率的值即可。这里我使用了Rply的框架进行ply的文件读取。完整的项目代码在这里<https://github.com/ModestBean/C-Samples/tree/master/3D-PLY>

这里给出main.cpp的代码
```
#include <stdio.h> 
#include "rply.h"
#include <fstream>
#include <iostream>
using namespace std;
ofstream outfile;
static int vertex_cb(p_ply_argument argument) {
	long eol;
	ply_get_argument_user_data(argument, NULL, &eol);
	float temp = ply_get_argument_value(argument);
	outfile << temp  << endl;
	printf("%g", ply_get_argument_value(argument));
	if (eol) printf("\n");
	else printf(" ");
	return 1;
}

static int face_cb(p_ply_argument argument) {
	long length, value_index;
	ply_get_argument_property(argument, NULL, &length, &value_index);
	switch (value_index) {
	case 0:
	case 1:
		printf("%g ", ply_get_argument_value(argument));
		break;
	case 2:
		printf("%g\n", ply_get_argument_value(argument));
		break;
	default:
		break;
	}
	return 1;
}

int main(void) {
	outfile.open("mean_curvature_vertex.txt");

	long nvertices;
	p_ply ply = ply_open("Mean_01.ply", NULL);
	if (!ply) return 1;
	if (!ply_read_header(ply)) return 1; //运行检查
	nvertices = ply_set_read_cb(ply, "vertex", "x", vertex_cb, NULL, 0);
	ply_set_read_cb(ply, "vertex", "y", vertex_cb, NULL, 0);
	ply_set_read_cb(ply, "vertex", "z", vertex_cb, NULL, 0);
	ply_set_read_cb(ply, "vertex", "quality", vertex_cb, NULL, 1);
	//printf("%ld\n", nvertices);
	if (!ply_read(ply)) return 1;
	ply_close(ply);

	outfile.close();//关闭文件流
	getchar();
	return 0;
}
```
