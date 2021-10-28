---
tags: [日志]
title: JnaApi开发日志
created: '2021-10-28T11:45:31.959Z'
modified: '2021-10-28T11:47:35.579Z'
---

# JnaApi开发日志
## 一、api接口标准
**1、数据结构标准** [数据类型映射表](http://java-native-access.github.io/jna/5.9.0/javadoc/)
```prettyprint
struct IpStruct
{
    char Ip[32];
    short Port;
};
```
**2、函数格式标准**
```prettyprint
extern "C" 
{
    __attribute__ ((visibility("default"))) void SetQueryIp(IpStruct ipInfo);
}
```
