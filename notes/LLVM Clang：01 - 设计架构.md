---
tags: 
    - LLVM
    - Clang
title: LLVM Clang：01 - 设计架构
created: '2022-8-02T08:30:18.408Z'
modified: '2022-8-19T10:02:44.927Z'
---

## 1、LLVM 背景

LLVM 项目的创始人为 Chris Lattner，亦是Swift之父。据传作者只是想写一个底层的虚拟机，这也是 LLVM 名字的由来，Low Level
Virtual Machine，类似Java的JVM虚拟机。后来，LLVM
从来没有被用作过虚拟机。所以人们决定仍然叫他LLVM，其实它跟虚拟机没有半毛钱关系。官方描述如下 The name "LLVM" itself is not
an acronym; it is the full name of the project. “LLVM”这个名称本身不是首字母缩略词，它是项目的全名。

## 2、传统编译器架构

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/73d76e9640df3cab176e1289f571a046.png)

* **Frontend** ：解析源代码，检查错误，并且把它翻译为特定语言语法抽象树（Abstract Syntax Tree，AST）。AST也可以被进一步优化转换为中间表示（比如，LLVM Intermedaite Representaion），后面的2个阶段都使用这个中间表示。
* **Optimizer** ：优化代码。比如去除无用的变量或计算，进而提高代码运行效率。
* **Backend** ：把优化后的代码转换为目标机器码（Target Instruction Set）。目标是生成充分可以利用目标机器体系结构的native code。

## 3、LLVM 架构

* 不同的前、后端使用统一的中间代码 **LLVM Intermediate Representation (LLVM IR)。**
* 支持一种新的编程语言，只需要实现一个新的前端。
* 支持一种新的硬件设备，只需要实现一个新的后端。
* 优化阶段 Optimizer 是一个通用的阶段，它针对的是统一的 LLVM IR，不论是支持新的编程语言，还是支持新的硬件设备，都不需要对优化阶段做修改。

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/7860514995bc2c134ce9c22859753131.png)

相比之下，GCC 的前端后端耦合在了一起。所以 GCC 若要支持一门新的语言，或者支持一个新的目标平台，就变得特别困难。

LLVM 现在被作为实现各种静态和运行时编译语言的通用基础结构
(GCC家族、Java、.NET、Python、Ruby、Scheme、Haskell、D等)。

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/d7d17e79296f1433ce76db9bcf29fe45.png)

上图是 **Clang/LLVM 的简单架构**
。最初时，LLVM的前端是GCC，后来Apple自己开发了一套Clang出来把GCC取代了，不过现在带有Dragon Egg的GCC还是可以生成LLVM
IR，也同样可以取代Clang的功能，我们也可以开发自己的前端，和LLVM后端配合起来，实现我们自定义的编程语言的编译器。

* LLVM IR 是LLVM的中间表示，大多数的优化都依赖于 LLVM IR 展开。把Opt单独画在一边，是为了简化图的内容，LLVM的设计思想是 **优化可以渗透在整个编译流程** 的各个阶段，比如编译时、链接时、运行时等。

在LLVM中，IR有三种表示：

* 第一种是可读的IR，类似于汇编代码，但其实它介于高等语言和汇编之间，这种表示就是给人看的，磁盘文件后缀为`.ll`；
* 第二种是不可读的二进制IR，被称作位码（bitcode），磁盘文件后缀为`.bc`；
* 第三种是一种内存格式， **只保存在内存** 中，所以谈不上文件格式和文件后缀，这种格式是LLVM 之所以编译更快的一个原因。它不像gcc，每个阶段结束会生成一些中间过程文件，它编译的中间数据都是这第三种表示的IR。

三种格式是完全等价的，我们可以在 Clang/LLVM 工具的参数中指定生成这些文件（默认不生成），可以通过 `llvm-as`和 `llvm-dis`来在前两种文件之间做转换。

**LLVM IR linker**
是IR的链接器，而不是gcc中的那个链接器。为了实现链接时优化，LLVM在前端（Clang）生成单个代码单元的IR后，将整个工程的IR都链接起来，同时做链接时优化。

**LLVM backend** 是LLVM真正的后端，也被称为LLVM核心，包括 **编译、汇编、链接** 这一套，最后生成汇编文件或者目标码。这里的
LLVM compiler 和 gcc compiler 不一样，这里的LLVM compiler只是编译LLVM IR。

## 4、Clang 与 LLVM 区别

Clang 是 LLVM 项目的一个子项目，是基于 LLVM 架构的 C/C++/Objective-C 编译器前端。

LLVM 整体架构，前端用的是 Clang；广义的 LLVM 是指整个 LLVM 架构，一般狭义的LLVM指的是 LLVM
后端（包含代码优化和目标代码生成）。

* LLVM 核心优化器（optimizer）。不依赖于源码和目标机器指令集。这个核心库基于 LLVM 的中间表示（LLVM intermedaite representation）。
* Clang 编译器（compiler）。Clang 目标是实现一个C/C++/Objective-C compiler。Clang可以提供非常有用的错误和警告信息。Clang Static Analyzer是一个自动化发bug的工具。

源代码（C/C++）经过 Clang ---> 中间代码(经过一系列的优化，优化用 Pass) ---> 机器码（目标程序）。

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/7208e36382a75c5cfc07f6067fd4aa15.png)

## 5、Clang 与 GCC 区别

* 编译速度快：在某些平台上，Clang的编译速度显著的快过GCC(Debug模式下编译OC速度比GCC快3倍)。
* 占用内存小：Clang生成的AST所占用的内存是GCC的五分之一左右。
* 模块化设计：Clang采用基于库的模块化设计，易于 IDE 集成及其他用途的重用。
* 诊断信息可读性强：在编译过程中，Clang 创建并保留了大量详细的元数据 (metadata)，有利于调试和错误报告。
* 设计清晰简单，容易理解，易于扩展增强。

**参考**：

[LLVM | 编译器框架](https://blog.csdn.net/qq_38844835/article/details/122553656?spm=1001.2014.3001.5506
"LLVM | 编译器框架")

[LLVM官方文档阅读(一)LLVM介绍](https://blog.csdn.net/weixin_43296779/article/details/121989541?spm=1001.2014.3001.5506
"LLVM官方文档阅读(一)LLVM介绍")

[LLVM官方文档](https://intel.github.io/llvm-docs/
"LLVM官方文档")

[LLVM开源项目](https://github.com/intel/llvm
"LLVM开源项目")
