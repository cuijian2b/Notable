---
tags: 
    - LLVM
title: LLVM DPC++：02 - CUDA插件
created: '2022-8-02T08:30:18.408Z'
modified: '2022-8-19T10:02:44.927Z'
---

## 1、Plugin Interface

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/DPC++/group__sycl__pi.png)

+ Files -> pi.hpp pi.cpp
+ Class -> sycl::_V1::detail::plugin

## CUDA Plugin

### Platform

#### cuda_piPlatformsGet

+ [PI Api References Info][piPlatformsGet]
+ `cuda_piPlatformsGet` 获取CUDA Platform，包含系统中所有的 Devices。因为会调用CUDA驱动的`cuInit`接口，所以这接口做完第一个PI Api被调用。
+ `CUDA APi` 调用的接口：
  + [cuInit][cuInit]
  + [cuDeviceGetCount][cuDeviceGetCount]
  + [cuDeviceGet][cuDeviceGet]

#### cuda_piPlatformGetInfo

+ [PI Api References Info][piPlatformGetInfo]
+ `cuda_piPlatformGetInfo` 根据参数 `param_name` 不同的值获取平台 `platform` 的相关信息。

#### NO CUDA Platform PI  

+ [piextPlatformGetNativeHandle][piextPlatformGetNativeHandle] 
+ [piextPlatformCreateWithNativeHandle][piextPlatformCreateWithNativeHandle] 

### Device

#### cuda_piDevicesGet

+ [PI Api References Info][piDevicesGet] 
+ `cuda_piDevicesGet` 从`pi_platform`获取指定数量的的 Devices。

#### cuda_piDeviceGetInfo

+ [PI Api References Info][piDeviceGetInfo]
+ `cuda_piDeviceGetInfo` 根据参数 `param_name` 不同的值获取设备 `device` 的相关信息。
+ `CUDA APi` 调用的接口：
  + [cuDeviceGetAttribute][cuDeviceGetAttribute]

#### cuda_piDevicePartition

+ [PI Api References Info][piDevicePartition]
+ `cuda_piDevicePartition` 空函数没实现功能，因为CUDA的device不能做分区。

#### cuda_piDeviceRetain

+ [PI Api References Info][piDeviceRetain]
+ `cuda_piDeviceRetain` 因为CUDA的device都是`root device`，所以都返回成功。

#### cuda_piDeviceRelease

+ [PI Api References Info][piDeviceRelease]
+ `cuda_piDeviceRelease` 因为CUDA的device都是`root device`，所以都返回成功。

#### cuda_piextDeviceSelectBinary

+ [PI Api References Info][piextDeviceSelectBinary]
+ `cuda_piextDeviceSelectBinary` 在`binary images`数组中查找为`PTX`格式的目标，如果找到则将该目标所在数组索引赋值给参数`selected_binary`，并且返回成功。

#### cuda_piextGetDeviceFunctionPointer

+ [PI Api References Info][piextGetDeviceFunctionPointer]
+ `cuda_piextGetDeviceFunctionPointer` 在`program`中查找名称为`func_name`的函数，如果找到则赋值给参数`func_pointer_ret`，并且返回成功。
+ `CUDA APi` 调用的接口：
  + [cuModuleGetFunction][cuModuleGetFunction]

#### cuda_piextDeviceGetNativeHandle

+ [PI Api References Info][piextDeviceGetNativeHandle]
+ `cuda_piextDeviceGetNativeHandle` 将`pi_device`转换为`pi_native_handle`格式，如并赋值给参数`nativeHandle`，返回成功。

#### cuda_piextDeviceCreateWithNativeHandle

+ [PI Api References Info][piextDeviceCreateWithNativeHandle]
+ `cuda_piextDeviceCreateWithNativeHandle` 将`pi_native_handle`转换为`pi_device`格式，如参数`platform`非空则判断转换的`device`是否属于`platform`。

### Context

#### cuda_piContextCreate

+ [PI Api References Info][piContextCreate] 
+ `cuda_piContextCreate` 创建CUDA的context上下文。
+ `CUDA APi` 调用的接口：
  + [cuDevicePrimaryCtxRetain][cuDevicePrimaryCtxRetain]
  + [cuCtxPushCurrent][cuCtxPushCurrent]
  + [cuCtxGetCurrent][cuCtxGetCurrent]
  + [cuCtxCreate][cuCtxCreate]
  + [cuEventCreate][cuEventCreate]
  + [cuEventRecord][cuEventRecord]
  + [cuCtxSetCurrent][cuCtxSetCurrent]

#### cuda_piContextGetInfo

+ [PI Api References Info][piContextGetInfo] 
+ `cuda_piContextGetInfo` 创建CUDA的context上下文。
+ `CUDA APi` 调用的接口：
  + [cuDevicePrimaryCtxRetain][cuDevicePrimaryCtxRetain]
  + [cuCtxPushCurrent][cuCtxPushCurrent]
  + [cuCtxGetCurrent][cuCtxGetCurrent]
  + [cuCtxCreate][cuCtxCreate]
  + [cuEventCreate][cuEventCreate]
  + [cuEventRecord][cuEventRecord]
  + [cuCtxSetCurrent][cuCtxSetCurrent]




// 
_PI_API()
_PI_API()
_PI_API(piContextRetain)
_PI_API(piContextRelease)
_PI_API(piextContextSetExtendedDeleter)
_PI_API(piextContextGetNativeHandle)
_PI_API(piextContextCreateWithNativeHandle)

[cuInit]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__INITIALIZE.html#group__CUDA__INITIALIZE
[cuDeviceGetCount]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__DEVICE.html#group__CUDA__DEVICE_1g52b5ce05cb8c5fb6831b2c0ff2887c74
[cuDeviceGet]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__DEVICE.html#group__CUDA__DEVICE_1g52b5ce05cb8c5fb6831b2c0ff2887c74
[cuDeviceGetAttribute]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__DEVICE.html#group__CUDA__DEVICE_1g9c3e1414f0ad901d3278a4d6645fc266
[cuModuleGetFunction]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__MODULE.html#group__CUDA__MODULE_1ga52be009b0d4045811b30c965e1cb2cf

[cuDevicePrimaryCtxRetain]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__PRIMARY__CTX.html#group__CUDA__PRIMARY__CTX_1g9051f2d5c31501997a6cb0530290a300
[cuCtxPushCurrent]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__CTX.html#group__CUDA__CTX_1gb02d4c850eb16f861fe5a29682cc90ba
[cuCtxGetCurrent]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__CTX.html#group__CUDA__CTX_1g8f13165846b73750693640fb3e8380d0
[cuCtxCreate]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__CTX.html#group__CUDA__CTX_1g65dc0012348bc84810e2103a40d8e2cf
[cuEventCreate]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__EVENT.html#group__CUDA__EVENT_1g450687e75f3ff992fe01662a43d9d3db
[cuEventRecord]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__EVENT.html#group__CUDA__EVENT_1g95424d3be52c4eb95d83861b70fb89d1
[cuCtxSetCurrent]: https://docs.nvidia.com/cuda/archive/10.2/cuda-driver-api/group__CUDA__CTX.html#group__CUDA__CTX_1gbe562ee6258b4fcc272ca6478ca2a2f7

[piPlatformsGet]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#a4c358eae6f8f0527d7ca1a90fce2ab12
[piPlatformGetInfo]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#ae86e28bd45bf105e1d9f0b7bc7f11324
[piextPlatformGetNativeHandle]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#a3ee5238db6e2fa6bd6d7a40aff3c8326
[piextPlatformCreateWithNativeHandle]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#a60a901e32f2bbcbaa6f8c8e8b2bf7029

[piDevicesGet]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#a69b24df8aa852caaedbbb5f3f31d9fd5
[piDeviceGetInfo]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#a4c04cf52d4de11e8706f60d51a953d60
[piDevicePartition]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#afda707ee95931de5209a0cae8b900732
[piDeviceRetain]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#aad0bd5367fb2c07f2adf685baea87471
[piDeviceRelease]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#aad7d6f4031adcabbc76ffef71cef36ec
[piextDeviceSelectBinary]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#aee54668a6fdc276d1bcd3ebcff05a4d5
[piextGetDeviceFunctionPointer]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#aeac83a44281d25a7780138c4312bfea4
[piextDeviceGetNativeHandle]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#a120d5faa8d24b9e34fe0d9f7a1a18e92
[piextDeviceCreateWithNativeHandle]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#aac9aa7c4ce7b26e19db9d214d1991235

[piContextCreate]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#a8c7db6243282e69199e76bada8607488
[piContextGetInfo]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#aee320401a1ebf46e946b23f2783187a6
[piContextRetain]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#a5c74a28b8f3b908c2808d68e9309dba6
[piContextRelease]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#ad537a9f474da4162708412aa1aa2b5d7
[piextContextSetExtendedDeleter]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#af778e984ded33ef185b3fae854e51ebf
[piextContextGetNativeHandle]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#aa3fb8ea4d33b7f0656afa0fdfedee5d4
[piextContextCreateWithNativeHandle]: https://intel.github.io/llvm-docs/doxygen/pi_8h.html#a71601e5c887b89497163d6abf241a995
