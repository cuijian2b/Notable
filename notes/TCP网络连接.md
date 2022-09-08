---
tags: [C++]
title: TCP网络连接
created: '2021-12-22T08:10:08.829Z'
modified: '2021-12-22T08:10:41.058Z'
---

# TCP网络连接

### 1、回调函数注册

```c++
// 回调映射结构
std::unordered_map<int, std::function<void(ProtoHeader *, char *, int)>>;
// 注册回调
std::function<void(ProtoHeader *, char *, int)> callBack =
    std::bind(&COptionQueryServer::OnParseUserLogin,
                this, std::placeholders::_1, std::placeholders::_2, std::placeholders::_3);
RspFuncs[cmd] = callBack;
```

### 2、网络配置

```c++
#define NTOHLL(value) ({                                                                                   \
    int64_t val = value;                                                                                   \
    if (__BYTE_ORDER == __LITTLE_ENDIAN)                                                                   \
    {                                                                                                      \
        val = (((uint64_t)htonl((int)((val << 32) >> 32))) << 32) | (unsigned int)htonl((int)(val >> 32)); \
    }                                                                                                      \
    else if (__BYTE_ORDER == __BIG_ENDIAN)                                                                 \
    {                                                                                                      \
        val = val;                                                                                         \
    }                                                                                                      \
    val;                                                                                                   \
})

#define HTONLL(value) (                                                                                        \
    {                                                                                                          \
        int64_t val = value;                                                                                   \
        if (__BYTE_ORDER == __LITTLE_ENDIAN)                                                                   \
        {                                                                                                      \
            val = (((uint64_t)htonl((int)((val << 32) >> 32))) << 32) | (unsigned int)htonl((int)(val >> 32)); \
        }                                                                                                      \
        else if (__BYTE_ORDER == __BIG_ENDIAN)                                                                 \
        {                                                                                                      \
            val = val;                                                                                         \
        }                                                                                                      \
        val;                                                                                                   \
    })
```

### 3、读数据线程

```c++
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

### 4、关闭连接

```c++
void CClientOnloadSocket::Close()
{
    RspConnect(2, 0);
    // IsReadMsg = false;
    ServerIps.Reset();
    RealseTcp();

    Heart.Reset();
}
```

### 5、关闭线程

```c++
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
