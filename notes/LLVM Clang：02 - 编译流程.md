---
tags: 
    - LLVM
    - Clang
title: LLVM Clang：02 - 编译流程
created: '2022-8-02T08:30:18.408Z'
modified: '2022-8-19T10:02:44.927Z'
---

## 概况

我们通过终端中输入，得到如下打印：

```bash
clang++ -ccc-print-phases test0.cpp -v --save-temps -g -o test0 >&test.log
```

得到编译流程：

```prettyprint
                +- 0: input, "test0.cpp", c++
            +- 1: preprocessor, {0}, c++-cpp-output
        +- 2: compiler, {1}, ir
    +- 3: backend, {2}, assembler
+- 4: assembler, {3}, object
5: linker, {4}, image
```

得到了6步操作的简要说明，分别是：

* 0：导入文件：是为了找到源文件。
* 1：预处理阶段：这个过程处理包括宏的替换，头文件的导入，会生成 test0.ii 文件，其中 cpp 不是指 C++ 语言，而是 c preprocessor 的缩写。
* 2：编译阶段：进行词法分析、语法分析、检测语法是否正确，生成 test0.bc 字节码文件IR。
* 3：后端：这里`LLVM`会通过一个一个的`Pass`去优化，每个`Pass`做一些事情，最终生成汇编代码，生成一个 main.s 文件。
* 4：汇编：生成目标文件，即为 test0.o。
* 5：链接：链接需要的动态库和静态库，生成可执行文件，最终生成 image。
* 下面我们一步一步拆解：

## 1、预处理阶段

是通过预处理器执行一系列的文本转换与文本处理。预处理器是在真正的编译开始之前由编译器调用的独立程序。

```bash
    clang++ -E test0.cpp -o preprocessed.ii
    cat preprocessed.ii
    cat test0.ii
    diff test0.ii preprocessed.ii
```

[参考文档](https://blog.csdn.net/develop_csdn/article/details/124539436#SEC41
"参考文档")

## 2、汇编文本

```bash
    cat test0.s
    clang++ -S preprocessed.ii -o compiled.s
    diff test0.s compiled.s
```

## 3、机器码

```bash
    cat test0.o
    clang++ -c compiled.s -o assembled.o
```

## 4、链接阶段

```bash
    clang++ assembled.o -o hello_manual
    ./hello_manual
```
