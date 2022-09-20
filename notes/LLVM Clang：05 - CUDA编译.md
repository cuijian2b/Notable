---
tags:
  - LLVM
title: LLVM Clang：05 - CUDA编译
created: 2022-08-02T08:30:18.408Z
modified: 2022-09-08T03:05:40.013Z
---

`CUDA` 与其他AOT目标不同，它的中间编译对象代码模块不是通过 `SPIR-V`，而是以bc的形式传递给 `NVPTX` 后端，所有的bc依赖于 `libdevice.bc`（CUDA SDK提供）和 `libspirv-nvptx64-nvidiacl.bc`（libclc项目构建），所以标的编译节点的区别以提供参考。

### 1、预处理阶段

+ `__STDCPP_DEFAULT_NEW_ALIGNMENT__` 展开成 std::size_t 字面量，其值为对不具对齐的 operator new 的调用所保证的对齐：
`CUDA` -> 16
`OPENCL` -> 8

+ `CUDA` 的预编译会定义 `__SYCL_DEVICE_ONLY__` 和 `__NVPTX__` 宏，该宏会在生成AST过程中加载不同的代码，定义不同的数据类型。

### 2、提炼设备代码

SYCL程序代码示例：

```c++
int foo(int x) { return ++x; }
int bar(int x) { throw std::exception{"CPU code only!"}; }
...
using namespace sycl;
queue Q;
buffer<int, 1> a{range<1>{1024}};
Q.submit([&](handler& cgh) {
      auto A = a.get_access<access::mode::write>(cgh);
      cgh.parallel_for<init_a>(range<1>{1024}, [=](id<1> index) {
        A[index] = index[0] * 2 + foo(42);
      });
    }
...
```

编译器需将 lambda 表达式传递给 `sycl::handler::parallel_for`，以及从 lambda 表达式中调用的 foo 函数，切忽略 bar 函数。SYCL 内核属性将标示为 `sycl::handler::parallel_for` 的代码标记为内核函数，编译器遍历内核函数可访问的符号将它们添加到设备代码。  

### 3、文件集群处理

SYCL 编译中的某些操作会输出多个文件集群，稍后会被其他操作使用。例如：每个bc文件会伴随 sym（符号表）和 props（专门化映射），`sycl-post-link` 需对集群进行提取和替换操作。
为了支持这一特性，SYCL 引进了 `file-table-tform` 工具。该工具可根据输入命令参数转换文件表。表中每一列表示一个文件类型，每一行为一组关联数据。

+ `sycl-post-link` 输出两个文件的集群：

```bash
  [Code|Symbols|Properties]
  a_0.bc|a_0.sym|a_0.props
  a_1.bc|a_1.sym|a_1.props
```

+ `file-table-tform` 根据输入命令拆分出bc文件：

```bash
a_0.bc
a_1.bc
```

+ 将文件列表 `llvm-foreach` 与 AOT 编译命令一起传递生成 bin：

```bash
a_0.bin
a_1.bin
```

+ `file-table-tform` 再次调用将 bc 替换为 bin：

```bash
  [Code|Symbols|Properties]
  a_0.bin|a_0.sym|a_0.props
  a_1.bin|a_1.sym|a_1.props
```

### 4、bc链接和拆分

+ SYCL 每个内核生成带有翻译单元 ID 的特殊元数据。
+ 使用 `llvm-link` 链接所有的 bc 模块。
+ 在完全链接的模块上执行拆分。
+ 为每一个 bc 生成 sym，以便运行时正确选择模块。
+ 方便对拆分 bc 执行 SPIR-V 翻译或 AOT 编译。

![Device code splitting](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/DPC++/DeviceCodeSplit.svg)

+ CUDA 因为没有通过 SPIR-V，拆分后 bc 被 clang 以临时文件链接 `libdevice.bc` 和 `libspirv-nvptx64-nvidiacl.bc` 使用 NVPTX 后端编译生成 `PTX`，再使用 `ptxas` 工具将 PTX 文件组装到 `cubin` 中，然后将 PTX 和 cubin 组装生成 CUDA fatbin。

![NVPTX AOT build](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/DPC++/DevicePTXProcessing.svg)

+ CUDA 特定编译宏 `__SYCL_DEVICE_ONLY__` 和 `__NVPTX__`，这个组合宏可以安全地用于在 SYCL 内核中启用 CUDA 的代码路径。

*注意：这些宏只在编译阶段定义。*


**Reference**：

[C++预处理指令](https://blog.csdn.net/wshom/article/details/123034650?spm=1001.2014.3001.5502
"C++预处理指令")
