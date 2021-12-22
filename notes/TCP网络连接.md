---
tags: [C++]
title: TCP网络连接
created: '2021-12-22T08:10:08.829Z'
modified: '2021-12-22T08:10:41.058Z'
---

# TCP网络连接
### 1、回调函数注册
```prettyprint
// 回调映射结构
std::unordered_map<int, std::function<void(ProtoHeader *, char *, int)>>;
// 注册回调
std::function<void(ProtoHeader *, char *, int)> callBack =
    std::bind(&COptionQueryServer::OnParseUserLogin,
                this, std::placeholders::_1, std::placeholders::_2, std::placeholders::_3);
RspFuncs[cmd] = callBack;
```
### 2、读数据线程
```prettyprint
// 读数据线程函数
void CClientOnloadSocket::ReadThread()
{
    IsReadMsg  = true;
    IsReadOver = false;
    while (IsReadMsg)
    {
        if (pTcpClient)
        {
            if (GetStatus() >= STATUS_CONNECTED && RecvMsg() >= PROTO_HEAD_LEN)
            {
                ParseMsg();
            }
        }
        else
        {
            std::this_thread::sleep_for(std::chrono::milliseconds(READ_INTERVAL_TIME));
        }
    }

    IsReadOver = true;
}
// 创建读线程
if (!readThread)
{
    readThread = std::shared_ptr<std::thread>(new std::thread(&CClientOnloadSocket::ReadThread, this));
}
else
{
    IsReadMsg  = true;
    IsReadOver = false;
}
```
### 3、关闭连接
```prettyprint
void CClientOnloadSocket::Close()
{
    RspConnect(2, 0);
    // IsReadMsg = false;
    ServerIps.Reset();
    RealseTcp();

    Heart.Reset();
}
```
### 4、关闭线程
```prettyprint
CClientOnloadSocket::~CClientOnloadSocket()
{
    if (readThread)
    {
        pthread_cancel(readThread->native_handle())
        if (readThread->joinable())
        {
            readThread->join();
        }
    }
}
```