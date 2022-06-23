---
title: Introducing CMake
toc: true
date: 2022-05-18 10:36:22
updated:
tags:
- Modern CMake for C++
- CMake
categories:
---
<!--more-->

# Introducing CMake

## Chapter 1, First Steps with CMake

### 简单介绍
相比较于其它一些自动构建代码的工具，CMake有以下优势：
- 持续聚焦和支持现代编译器和工具链
- 很好的跨平台支持，包括Windows，Linux，macOS以及Cygwin
- 适配主流IDE，Visual Studio，XCode，Eclipse CDT，Clion等
- CMake工作的抽象级别比较合适，允许我们将可重用的库和项目分组
- 有许多使用CMake构建的项目，然后也可以很方便的被我们的项目调用
- 弃用老旧无用特性，使得CMake相对精简

CMake构建项目分为三步配置、生成和构建

- 配置阶段： 在资源树中读取项目细节，为生成阶段准备输出目录or 构建树
    - CMake开始会创建一个空的构建树并收集工作环境的信息，包括计算机架构，可用编译器类型，链接器，同时还会检查一个简单的测试程序是否能正确编译。
    - 检查CMakeLists.txt文件（用CMake语言写的一个配置文件），查找CMake版本、项目结构，链接库、依赖习惯等信息。
    - 创建CMakeCache.txt存储更多其它变脸，比如编译器和一些工具路径
- 生成阶段：读取配置信息后，CMake为当前系统生成一个构建系统（Makefiles），构建系统实质上是特定构建工具（例如GNU）的一个配置文件，这个阶段，CMake还能通过生成器表达式对构建系统作最后的修改
- 构建阶段，包括编译、连接、测试以及打包

### 一些命令
CMake包含五个最主要的执行命令：
- cmake：最主要的可执行文件，用于配置，生成和构建项目
- ctest：测试驱动程序，用于运行和报告测试结果
- cpack：打包程序用于生成安装器和资源包
- cmake-gui:cmake的图形化包装器
- ccmake：基于控制台的cmake图形化包装器

#### 生成项目构建系统
```
cmake [<options>] -S <path-source> -B <path-to-build>
cmake [<options>] <path-to-source>
cmake [<options>] <path-to-existing-build>
```
最基本最朴实用法就是-S后面跟资源目录， -B后面跟build tree的目录比如：
`cmake -S ./ -B ./build`意思就是从当前目录生成一个构建系统到./build目录
我们可以跳过一些参数，CMake会猜测我们想使用当前目录，比如`cmake`不加任何参数，cmake会认为我们想要从当前目录生成一个构建系统到当前目录（这样子很混乱）,通常的做法可以是在项目的根目录使用`cmake -B build`会直接从根目录生成一个构建系统到build目录。

### 生成器选项
如果我们同时安装了多种生成器（例如MinGW MSVC等等），CMake可能会选错，此时可以通过-G选项指定生成器
`cmake -G <generator-name> <path-to-source>`

同时也可以通过`-T`选项指定特定工具集（编译器）或者平台（编译器orSDK）,这些东西都存储在CMake的环境变量中，可以通过添加选项暂时覆盖
`cmake -G <generator-name> -T <toolset-spec> -A <platform-name> <path-to-source>`

可以通过`cmake --help`命令查看当前系统可用的生成器（前面带*号的就是默认的）

### 缓存选项






## Chapter 2, The CMake Language

## Chapter 3, Setting Up Your First CMake Project

