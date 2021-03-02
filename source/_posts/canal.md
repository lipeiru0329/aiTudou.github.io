---
title: canal
date: 2020-06-11 15:51:34
tags:
---

## Canal

Canal是一个基于MySQL二进制日志的高性能数据同步系统,以提供可靠的低延迟增量数据管道.

https://github.com/alibaba/canal

<!-- More -->
Canal Server能够解析MySQL binlog并订阅数据更改，而Canal Client可以实现将更改广播到任何地方，例如数据库和Apache Kafka。

它具有以下功能：

- 支持所有平台。
- 支持由Prometheus提供支持的细粒度系统监控。
- 支持通过不同方式解析和订阅MySQL binlog，例如通过GTID。
- 支持高性能，实时数据同步。（详见Performance）
- Canal Server和Canal Client都支持HA / Scalability，由Apache ZooKeeper提供支持
- Docker支持。

缺点：

- 不支持全量更新，只支持增量更新。

Wiki: https://github.com/alibaba/canal/wiki

### 运作原理

原理很简单：

Canal模拟MySQL的slave的交互协议，伪装成mysql slave，并将转发协议发送到MySQL Master服务器。
MySQL Master接收到转储请求并开始将二进制日志推送到slave（即canal）。
Canal将二进制日志对象解析为自己的数据类型（原始字节流）

![](https://user-gold-cdn.xitu.io/2019/6/22/16b7eab30d66e07d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
