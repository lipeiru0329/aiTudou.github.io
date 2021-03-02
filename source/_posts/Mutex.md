---
title: Mutex
date: 2020-05-27 17:02:30
tags:
disqusId: aitudou-blog
---

### 互斥锁

互斥锁： SET KEY VALUE [EX seconds] [PX milliseconds] [NX|XX]

EX seconds − 设置指定的到期时间(以秒为单位)。

PX milliseconds - 设置指定的到期时间(以毫秒为单位)。

<!-- More -->

NX - 仅在键不存在时设置键。

XX - 只有在键已存在时才设置。
```
//设置“锁”
if(redis.set("lock", "1", "EX 180", "NX")){
    //业务逻辑
    .......
    //执行完业务逻辑后，释放锁
    redis.delete("lock");
}
```