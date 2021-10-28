---
tags: [C++]
title: 现代C教程
created: '2021-10-28T11:45:24.721Z'
modified: '2021-10-28T11:47:16.719Z'
---

# 现代C教程
## 一、创建对象
### 1、构造函数、赋值函数、析构函数等
```prettyprint
// 消息返回结构体
struct MsgInfo
{
    int Cmd;            // 命令码
    int Error;          // 错误码
    char *pMsg;         // 消息体
    int msgLen;         // 消息长度

    MsgInfo()
    {
        Cmd = 0;
        Error = 0;
        msgLen = 0; 
        pMsg = nullptr;       
    }
    
    MsgInfo & operator=(const MsgInfo &msg)
    {
        if(this != &msg)
        {
            Cmd = msg.Cmd;
            Error = msg.Error;
            msgLen = msg.msgLen;
            // 释放原有
            if (pMsg)
            {
                delete []pMsg;
                pMsg = nullptr;
            }
            
            // 拷贝信息
            pMsg = new char[msgLen];        
            memcpy(this->pMsg, msg.pMsg, msgLen);               
        } 

        return *this;
    }

    MsgInfo(int cmd, int error, char *pMsg, int msgLen) : Cmd(cmd), Error(error), msgLen(msgLen)
    {
        // 拷贝信息
        this->pMsg = new char[msgLen];        
        memcpy(this->pMsg, pMsg, msgLen);
    }

    MsgInfo(const MsgInfo &msg)
    {
        Cmd = msg.Cmd;
        Error = msg.Error;
        msgLen = msg.msgLen;
        // 拷贝信息
        this->pMsg = new char[msgLen];        
        memcpy(this->pMsg, msg.pMsg, msgLen);        
    }

    ~MsgInfo()
    {
        if (pMsg)
        {
            delete []pMsg;
            pMsg = nullptr;
        }        
    }
};
```
