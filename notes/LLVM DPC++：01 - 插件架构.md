---
tags: 
    - LLVM
title: LLVM DPC++：01 - 插件架构
created: '2022-8-02T08:30:18.408Z'
modified: '2022-8-19T10:02:44.927Z'
---

## 1、概述

`DPC++` 运行时Plugin接口(PI)是 `DPC++` 运行时设备无关部分与执行特定设备之间的接口。

*DPC++ 运行时堆栈路径图：*

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/DPC++/RuntimeArchitecture.svg)

## 2、插件发现

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/DPC++/PluginDiscovery.svg)

`DPC++` 运行时在Linux上 `LD_LIBRARY_PATH` 环境变量下搜索设备代码的so库(libpi_cuda.so等)，当前搜索的库名称为硬编码。
设置 `SYCL_PI_TRACE` 环境变量的值可以实现不同级别的跟踪。0X01 提供发现和绑定插件的基础跟踪，选定设备信息；0x02 提供 `DPC++` 运行时使用参数和返回值的插件跟踪；-1 列出所有的调试信息。

```cpp
// Definition at line 271 of file pi.cpp
// Find the plugin at the appropriate location and return the location.
std::vector<std::pair<std::string, backend>> findPlugins() {
   std::vector<std::pair<std::string, backend>> PluginNames;
   device_filter_list *FilterList = SYCLConfig<SYCL_DEVICE_FILTER>::get();

   if (!FilterList) {
     PluginNames.emplace_back(__SYCL_OPENCL_PLUGIN_NAME, backend::opencl);
     PluginNames.emplace_back(__SYCL_LEVEL_ZERO_PLUGIN_NAME, backend::ext_oneapi_level_zero);
     PluginNames.emplace_back(__SYCL_CUDA_PLUGIN_NAME, backend::ext_oneapi_cuda);
     PluginNames.emplace_back(__SYCL_HIP_PLUGIN_NAME, backend::ext_oneapi_hip);
     PluginNames.emplace_back(__SYCL_ESIMD_EMULATOR_PLUGIN_NAME, backend::ext_intel_esimd_emulator);
   }
} 
```

+ 插件应实现其支持 `PI` 版本所需的API，`pi.def`/`pi.h` 文件列出所有 `PI` 的API接口，`piPluginInit` 方法返回插件详细信息和API接口实现的函数指针列表 `_pi_plugin.PiFunctionTable`。

+ `DPC++` 运行 `initializePlugins` 时通过 `findPlugins` 方法获取设备库列表，`loadPlugin` 加载设备so库，`bindPlugin` 完成设备库的绑定。`SYCLConfig<SYCL_BE>` 的值确定 `GlobalPlugin` 为哪个特定设备插件，插件与 `SYCL_BE` 之间的对应关系硬编码的。

+ 当不在需要插件时，通过调用 `piTearDown` 执行插件的卸载和内存释放，之后调用 `unload` 安全的卸载插件。

## 3、代码流程

+ `pi.h` 插件初始化接口：

```cpp
 // PI Plugin Initialise.
 // Plugin will check the PI version of Plugin Interface,
 // populate the PI Version it supports, update targets field and populate
 // PiFunctionTable with Supported APIs. The pointers are in a predetermined
 // order in pi.def file.
 __SYCL_EXPORT pi_result piPluginInit(pi_plugin *plugin_info);
```

+ `pi.h` 定义插件结构体：

```cpp
 struct _pi_plugin {
   // PI version supported by host passed to the plugin. The Plugin
   // checks and writes the appropriate Function Pointers in
   // PiFunctionTable.
   // TODO: Work on version fields and their handshaking mechanism.
   // Some choices are:
   // - Use of integers to keep major and minor version.
   // - Keeping char* Versions.
   char PiVersion[20];
   // Plugin edits this.
   char PluginVersion[20];
   char *Targets;
   struct FunctionPointers {
 #define _PI_API(api) decltype(::api) *api;
 #include <sycl/detail/pi.def>
   } PiFunctionTable;
 };
```

+ `pi_cuda.cpp` API接口映射：

```cpp
pi_result piPluginInit(pi_plugin *PluginInit) {
// Forward calls to CUDA RT.
#define _PI_CL(pi_api, cuda_api)                                   \
  (PluginInit->PiFunctionTable).pi_api = (decltype(&::pi_api))(&cuda_api);

  // Platform
  _PI_CL(piPlatformsGet, cuda_piPlatformsGet)
  _PI_CL(piPlatformGetInfo, cuda_piPlatformGetInfo)
```

**参考**：

[DPC++ PI](https://intel.github.io/llvm-docs/design/PluginInterface.html
"DPC++ PI")
