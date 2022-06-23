---
title: cmake practice
toc: true
date: 2022-05-18 13:26:49
updated:
tags:
- CMake
categories:
---
<!--more-->

# CMake Practice
## 初识CMake
CMake特点
1. 开放源代码
2. 跨平台
3. 能够管理大型项目
4. 简化编译构建过程好编译过程，CMake工具链简单，cmake + make
5. 高效率
6. 可扩展，可以为CMake编写特定功能的模块，扩充其功能

## 初试CMake
### CMake基本语法规则
1. 变量使用${}方法取值，但IF控制语句中直接使用变量名
2. 指令（参数1 参数2 ....）
3. 指令大小写无关，但参数和变量是大小写相关的

cmake语法相对灵活,例如`SET(SRC_LIST main.cc)`也可以写成`SET(SRC_LIST "main.cc")`（感觉类似shell语法）但是如果假设源文件名是fu nc.cc此时就必须包含双引号界定范围`SET(SRC_LISt "fu nc.c")` 否则会报找不到文件的错。

参数间也可以使用分号分割,例如：
`ADD_EXECUTABLE(t1 main.c t1.c)`也可以写成`ADD_EXECUTABLE(t1 main.c; t1.c)`，只需要编写`CMakeLists.txt`时统一风格即可。

### 清理工程
`make clean` 可以对`make`命令产生的构建结果进行清理

### 内部构建与外部构建
内部构建就是在项目文件夹直接构建，这样会导致大量临时文件污染代码文件，破坏间接性，一般是建议弄一个`build`目录运行，可以在项目根目录使用`cmake -B build`执行。

