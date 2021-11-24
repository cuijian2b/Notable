---
tags: [记录]
title: 华云JnaApi记录
created: '2021-10-29T10:28:33.266Z'
modified: '2021-11-18T02:24:13.137Z'
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
```
cd /opt/redis
./redis-cli -p 6888
quit
```

## 3、数据库
**明文保存密码**
**double类型数据，是否可改成整形**

## 4、服务器
**出现数据头转换错误**
```
ssize_t retCode = ntohl(proHead->RetCode);
uint16_t msgId = ntohs(proHead->FuncNo); 
```

## 5、正则表达式替换
**下划线格式转驼峰格式**
```
(\w*)_(\w*)_(\w*)
\u$1\u$2\u$3
(\w*)_(\w*)
\u$1\u$2
    "(\w)
    "\u$1    
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
pMsg->\u$1\u$2\u$3 =
pMsg->(\w*)_(\w*) =
pMsg->\u$1\u$2 =
```
```
( *)[A-Z]*;
```
**struct.h替换**
```
, "(.*)
;
```
```
   \(\(
    
```
```
char (.*)\[(.*)
std::string $1;    
``` 
json赋值函数
```
(.*);
pRsp->$1 = jsonStruct.$1;    
``` 
```
(.*);
strncpy(pRsp->$1, jsonStruct.$1.c_str(), sizeof(pRsp->$1));    
``` 
**json消息替换**
 ```
    "

``` 

## 6、服务器特性
登录服数据同步一对一发送，数据查询支持多端同时分发

## 6、完整登录流程
```
// 创建用户自定义回调接口
CCustomerQuerySpi *pQuerySpi = new CCustomerQuerySpi();
// 绑定回调并设置Query服ip地址
pManageApi->BindQuerySpi(ips, pQuerySpi)
```

## 7、下单相关
### 7.1、用户信息
 ```
{
    "Id":1
    "UserID":"admin1"
    "Password":"companyid"
    "Token":"c1117a628a54b3ce5c876e3fd2934d8c"
    "SessionID":8
    "ClientType":1
    "AccountId":1
    "PbuId":1
    "OwnerType":1
    "UserPropty":1
    "UserStatus":0
    "FeeGroup":2
    "SubmitPbuId":"pbu"
    "ClearingFirm":"cl"
    "BranchId":"br"
    "OrderRestrictions":"1"
    "CompanyId":"userpasswd"
    "UserAccount:"17695333340"
    "Phone:""
    "HolderAccountId:"abc1"
    "ContractAccountCode:"000001"    
}
``` 
```
"Id":2
"UserID":"admin2"
"Password":"companyid"
"Token":"74faf094329713e0de744711ae966015"
"SessionID":13
"ClientType":1
"AccountId":2
"PbuId":1
"OwnerType":1
"UserPropty":1
"UserStatus":0
"FeeGroup":2
"SubmitPbuId":"pbu"
"ClearingFirm":"cl"
"BranchId":"br"
"OrderRestrictions":"1"
"CompanyId":"userpasswd"
"UserAccount:"17695333340"
"Phone:""
"HolderAccountId:"abc2"
"ContractAccountCode:"000002"
``` 
### 7.2、订单信息
#### 7.2.1、单笔限价买开单
```
PassOrder *pOrder = new PassOrder;
pOrder->OptionId = 1;
strncpy(pOrder->OptionIdSource, "102", sizeof(pOrder->OptionIdSource));
pOrder->SecurityId = 1;                
pOrder->Category = BUY_OPEN;
pOrder->Entrust = ET_LIMIT_PRICE_IDX;
pOrder->OrderQty = 100;
pOrder->Price = 10000;
```
