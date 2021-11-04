---
tags: [记录]
title: 华云JnaApi记录
created: '2021-10-29T10:28:33.266Z'
modified: '2021-11-02T07:32:14.354Z'
---

# 华云JnaApi记录
## 起服地址
**1、交易所**
jianglp@192.168.1.82:/home/jianglp/work/exchange_c
cj@192.168.1.82:/home/cj/exchange 5984
**2、access**
root@192.168.1.84:/home/jianglipeng/backend_cj
cj@192.168.1.83/home/cj/accessTags/option 6367
**3、redise**
cj@192.168.1.82:/home/cj/redis 6367

## 2、redis相关
**进入redis根目录，执行命令:**
```
nohup redis-server &
```

## 3、数据库错误
**明文保存密码**

## 4、服务器错误
**出现数据头转换错误**
```
ssize_t retCode = ntohl(proHead->RetCode);
uint16_t msgId = ntohs(proHead->FuncNo); 
```

## 5、正则表达式替换
**下划线格式转驼峰格式**
```
(\w*)_(\w*)_(\w*)
\l$1\u$2\u$3
(\w*)_(\w*)
\l$1\u$2
```
**变量类型替换**
```
uint32
unsigned int
int32
int
int64
long long
bytes
char
```
```
(\d\d) 
$1_t 
```
**等号替换**
```
 = (\d+);
;
```
**数值转换替换**

 ```
    (\w*) 

```   
```
(\w*) = (\d+);
    pMsg->$1 = info.$1();
```
```
pMsg->(\w*)_(\w*)_(\w*) =
pMsg->\l$1\u$2\u$3 =
pMsg->(\w*)_(\w*) =
pMsg->\l$1\u$2 =
```

## 6、服务器特性
登录服数据同步一对一发送，数据查询支持多端同时分发






