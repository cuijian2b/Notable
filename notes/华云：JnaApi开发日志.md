---
tags: 
    - Jop
title: 华云：JnaApi开发日志
created: '2021-10-28T11:45:31.959Z'
modified: '2021-10-29T02:57:22.167Z'
---

# JnaApi开发日志
## 一、结构定义标准
**1、数据结构标准** [数据类型映射表](http://java-native-access.github.io/jna/5.9.0/javadoc/)
```prettyprint
struct IpStruct
{
    char Ip[32];
    short Port;
};
struct QueryLoginMsg
{
    char token[32];
    unsigned int sessionID;
    unsigned int userID;
};
```
**2、请求函数格式标准**
```prettyprint
extern "C" 
{
    __attribute__ ((visibility("default"))) void SetQueryIp(const IpStructArray *pIps, int logLevel);
}
```
**3、回调函数格式标准**
```prettyprint
extern "C" 
{
    __attribute__ ((visibility("default"))) void OnRspUserLogin(int retCode, QueryLoginMsg *pMsg);
}
```
## 二、CTP模式接口设计

## 三、redis相关
### 1、redis配置
```prettyprint
{
    "ip": "192.168.1.82",
    "port": 6379,
    ip=192.168.1.82
    port=6379
    db_index=5
    channel1=position1
}
```