---
title: 【3D实践】PLY文件解析、读取
date: 2019-03-06 09:16:31
tags:
categories: 3D图形学
---
PLY文件英文解释地址<https://web.archive.org/web/20081203195143/http://www.cs.princeton.edu/~diego/professional/rply/>

PLY储存的资讯包含颜色、透明度、表面法向量、材质座标与资料可信度，并能对多边形的正反两面设定不同的属性。

<!-- more -->

``` 
ply 
format ascii 1.0
comment this is a simple file
obj_info any data, in one line of free form text
element vertex 3
property float x
property float y
property float z 
element face 1
property list uchar int vertex_indices
end_header
-1 0 0
 0 1 0
 1 0 0 
3 0 1 2
```

 - 第1行检测是否为PLY文件

 - 第2行为数据编号以及存储方式（ascii, binary_big_endian or binary_little_endian）

 - 第3行为描述，和注释意义相同

 - 第4行为模型信息

 - 第5~8行为顶点数据

 - 第9~10行为面片mesh数据
