---
tags: 
    - Redis
title: Redis常用操作
created: '2021-11-03T09:35:15.213Z'
modified: '2021-11-04T05:52:46.622Z'
---

# Redis常用操作
## 一、常规操作
**redis-cli常用命令**
```prettyprint
cd /opt/redis
quit
./redis-cli -p 6888             // 通过port连接redis服务
select 6                        // 选择第5号数据库
keys *                          // 显示所有数据
Flushall                        // 清空数据

HGETALL tb_new_order            // 获取表数据
```
