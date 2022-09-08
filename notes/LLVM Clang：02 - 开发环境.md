---
tags: 
    - LLVM
title: LLVM Clang：02 - 开发环境
created: '2022-8-02T08:30:18.408Z'
modified: '2022-8-19T10:02:44.927Z'
---

### 1、编译工具

* git - [Download](https://git-scm.com/downloads)
* cmake version 3.14 or later - [Download](https://cmake.org/download/)
* python - [Download](https://www.python.org/downloads/)
* ninja - [Download](https://github.com/ninja-build/ninja/wiki/Pre-built-Ninja-packages)
* C++ compiler
  * Linux: GCC version 7.1.0 or later (including libstdc++) - [Download](https://gcc.gnu.org/install/)
  * Windows: Visual Studio version 15.7 preview 4 or later - [Download](https://visualstudio.microsoft.com/downloads/)

### 2、编译工程

#### 2.1、配置参数

* `-werror` -> treat warnings as errors when compiling LLVM
* `-cuda` -> use the cuda backend (see Nvidia CUDA)
* `-hip` -> use the HIP backend (see HIP)
* `-hip-platform` -> select the platform used by the hip backend, AMD or NVIDIA (see HIP AMD or see HIP NVIDIA)
* `-enable-esimd-emulator` -> enable ESIMD CPU emulation (see ESIMD CPU emulation)
* `-enable-all-llvm-targets` -> build compiler (but not a runtime) with all supported targets
* `-shared-libs` -> Build shared libraries
* `t` -> Build type (Debug or Release)
* `o` -> Path to build directory
* `-cmake-gen` -> Set build system type (e.g. --cmake-gen "Unix Makefiles")

#### 2.2、配置环境

**环境变量:**

```bash
export LD_LIBRARY_PATH=/usr/lib
export LD_LIBRARY_PATH=/usr/lib64:$LD_LIBRARY_PATH
```

**libc++:**

```py
cmake_cmd.extend([
      "-DSYCL_USE_LIBCXX=ON",
      "-DSYCL_LIBCXX_INCLUDE_PATH=/usr/include".format(args.libcxx_include),
      "-DSYCL_LIBCXX_LIBRARY_PATH=/usr/lib".format(args.libcxx_library)])
```

**CUDA:**

```py
cmake_cmd = [
      ...
      "--DCUDA_TOOLKIT_ROOT_DIR==/path/to/cuda".format(args.libcxx_include)
    ]
```

#### 2.3、加载配置

* `Cuda` -> python ./configure.py -cuda -t debug
* `Opencl` -> python ./configure.py -t debug

#### 2.4、compile.py编译

python ./compile.py

### 3、编译示例

#### 3.1、添加环境变量

```bash
export DPCPP_HOME=~/project/llvm-2022-06
export PATH=$DPCPP_HOME/build/bin:PATH
export LD_LIBRARY_PATH=$DPCPP_HOME/build/lib:$LD_LIBRARY_PATH
```

#### 3.2、编译项目

* `Cuda` -> clang++ -fsycl -fsycl-targets=nvptx64-nvidia-cuda sample.cpp --fsycl-device-code-split=auto >&test.log
* `Opencl` -> clang++ -fsycl sample.cpp --fsycl-device-code-split=auto >&test.log

**编译选项:**

* `-fsycl` -> sycl编译链接
* `-fsycl-targets` -> AOT设备目标
* `--fsycl-device-code-split` -> 设备代码模式
  * `per_kernel`
  * `per_source`
  * `off`
  * `auto`
* `-ccc-print-phases` -> 打印编译流程
* `-v` -> 编译日志
* `-g` -> debug信息
* `-save-temps` -> 保留编译中间文件

**参考**：

[LLVM | 指引文档](https://intel.github.io/llvm-docs/GetStartedGuide.html
"LLVM | 指引文档")

[LLVM | 编译选项](https://intel.github.io/llvm-docs/UsersManual.html
"LLVM | 编译选项")

[LLVM | CUDA](https://blog.csdn.net/kunhe0512/category_11774233.html?spm=1001.2014.3001.5515
"LLVM | CUDA")
