---
title: docker-1
date: 2020-07-23 15:58:13
tags:
---

## Docker
这次讲讲Docker

<!-- More -->

### Docker 启动过程

1. 配置启动container需要的环境
此部分为启动container进程做准备。包括一下几个方面：
1. 构建container的DNS
使用container的setupContainerDns方法构建container的DNS。 –dns
2. 挂在文件系统
使用Mount方法配置container的basefs路径。
3. 初始化网络
使用initialize方法配置container网络信息，详细分析见docker如果构建bridge网络 。–net
4. 更新parent hosts
使用container的updateParentHosts方法更新与当前container相关container的etc/hosts。
5. 验证container资源设置
使用container的verifyDaemonSetting方法校验container的Me moryLimit、SwapLimit和IPv4ForwardingDisable设置。
6. 卷准备
使用container的prepareVolumes方法为container启动配置挂载数据卷。 –volume
7. 链接其他container
使用container的setupLinkedContainers方法配置container需要连接的其他container。 –link
8. 构建工作路径
使用container的setupWorkingDirectory方法配置container的工作路径。
9. 创建container进程环境
使用container的createDaemonEnvironment方法配置container内的环境变量。 –env
10. populateCommand
根据步骤9收集到的环境变量，使用populateCommand构建execdriver命令execdriver.Command
11. 构建mount点
使用container的setupMounts方法设置container内部的mount配置。

2. 启动container进程

在准备好启动container进程的配置后，docker开始创建container进程，使用container的waitForStart方法完成此项任务。
通过为启动的container创建一个monitor来监控container进程的启动情况，这个地方使用了golang的一个特性：channel。
如果container进程正常启动，那么会在container.monitor.startSigbal通道受到一个信号；如果在promise.Go(container.monitor.Start)通道收到错误消息，说明container启动失败并返回错误信息。
也就是说container monitor监控container主进程的执行情况。如果container被指定了一种重启策略，那么monitor保证进程按重启策略重启。当container被stop，monitor会reset并cleanup container占用的资源。

- Monitor
- 进程

---

## Docker VS K8S


>Kubernetes 的优势

    使用 Pod 轻松组织服务
    它由 Google 开发，将多年宝贵的行业经验带到桌面
    容器编排工具中最大的社区。
    提供多种存储选项，包括本地 SAN 和公共云。
    坚持不可变更的基础架构原则
    
>Docker 的优势

    提供高效，简便的初始设置
    集成并使用现有 Docker 工具
    允许您详细描述您的应用程序生命周期
    Docker 允许用户轻松跟踪其容器版本，以检查先前版本之间的差异。
    简单的配置，可与 Docker Compose 交互。
    Docker 提供了一个快速启动的环境，可以启动虚拟机并让应用程序在虚拟环境中快速运行。
    文档提供了所有信息。
    提供简单快速的配置以促进您的业务
    确保应用程序隔离

>Kubernetes 的缺点

    迁移到无状态需要付出很多努力
    相比 Docker 可用性 API 来讲，功能有限
    高度复杂的安装 / 配置过程
    与现有 Docker CLI 和 Docker Compose 工具不兼容
    复杂的手动群集部署和自动水平伸缩设置

>Docker 的劣势

    不提供存储选项
    监控功能较弱
    没有自动重新计划非活动节点
    复杂的自动水平伸缩设置
    所有操作都必须在 CLI 中执行。
    基本的基础架构处理
    手动处理多个实例
    需要支持其他用于生产方面的工具 - 监视、修复、伸缩
    复杂的手动群集部署
    不支持运行状况检查
    Docker 是营利性 SaaS 公司。许多关键组件，如 Docker Engine、Docker Desktop 都是不开源的。
