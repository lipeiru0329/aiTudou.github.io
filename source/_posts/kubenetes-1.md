---
title: kubenetes-1
date: 2020-06-02 13:27:16
tags:
---

## Kutebenetes - 1

Kubernetes 是一个可移植的、可扩展的开源平台，用于管理容器化的工作负载和服务，可促进声明式配置和自动化。Kubernetes 拥有一个庞大且快速增长的生态系统。Kubernetes 的服务、支持和工具广泛可用。
<!-- More -->

传统部署时代 --> 虚拟化部署时代 VM --> 容器部署时代

__容器部署时代__： 容器类似于 VM，但是它们具有轻量级的隔离属性，可以在应用程序之间共享操作系统（OS）。因此，容器被认为是轻量级的。容器与 VM 类似，具有自己的文件系统、CPU、内存、进程空间等。由于它们与基础架构分离，因此可以跨云和 OS 分发进行移植。

Kubernetes 为您提供：

1. 服务发现和负载均衡

Kubernetes 可以使用 DNS 名称或自己的 IP 地址公开容器，如果到容器的流量很大，Kubernetes 可以负载均衡并分配网络流量，从而使部署稳定。

2. 存储编排

Kubernetes 允许您自动挂载您选择的存储系统，例如本地存储、公共云提供商等。

3. 自动部署和回滚

您可以使用 Kubernetes 描述已部署容器的所需状态，它可以以受控的速率将实际状态更改为所需状态。例如，您可以自动化 Kubernetes 来为您的部署创建新容器，删除现有容器并将它们的所有资源用于新容器。

4. 自动二进制打包

Kubernetes 允许您指定每个容器所需 CPU 和内存（RAM）。当容器指定了资源请求时，Kubernetes 可以做出更好的决策来管理容器的资源。

5. 自我修复

Kubernetes 重新启动失败的容器、替换容器、杀死不响应用户定义的运行状况检查的容器，并且在准备好服务之前不将其通告给客户端。

6. 密钥与配置管理

Kubernetes 允许您存储和管理敏感信息，例如密码、OAuth 令牌和 ssh 密钥。您可以在不重建容器镜像的情况下部署和更新密钥和应用程序配置，也无需在堆栈配置中暴露密钥

---

### Master Node

Master 负责管理整个集群。 

Master 协调集群中的所有活动，例如调度应用、维护应用的所需状态、应用扩容以及推出新的更新。

Node 是一个虚拟机或者物理机，它在 Kubernetes 集群中充当工作机器的角色 

每个Node都有 Kubelet , 它管理 Node 而且是 Node 与 Master 通信的代理。 Node 还应该具有用于​​处理容器操作的工具，例如 Docker 或 rkt 。处理生产级流量的 Kubernetes 集群至少应具有三个 Node 。

Master 管理集群，Node 用于托管正在运行的应用。

在 Kubernetes 上部署应用时，您告诉 Master 启动应用容器。 Master 就编排容器在群集的 Node 上运行。 Node 使用 Master 暴露的 Kubernetes API 与 Master 通信。终端用户也可以使用 Kubernetes API 与集群交互。

### 安装

安装 K8S 可以用 [Minikube](https://kubernetes.io/zh/docs/setup/learning-environment/minikube/)

- Show all nodes

```
kubectl get nodes
```

- 
 