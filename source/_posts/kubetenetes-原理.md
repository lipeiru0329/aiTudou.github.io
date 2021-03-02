---
title: kubetenetes-原理
date: 2020-07-08 11:13:39
tags:
---

## K8S 原理

这节是 K8S的原理 分析。
<!-- More -->

### k8s 架构体系

#### Master节点

Master节点主要有四个组件，分别是：api-server、controller-manager、kube-scheduler 和 etcd。

api-server

    kube-apiserver 作为 k8s 集群的核心，负责整个集群功能模块的交互和通信，集群内的各个功能模块如 kubelet、controller、scheduler 等都通过 api-server 提供的接口将信息存入到 etcd 中，当需要这些信息时，又通过 api-server 提供的 restful 接口，如get、watch 接口来获取，从而实现整个 k8s 集群功能模块的数据交互。

controller-manager

    controller-manager 作为 k8s 集群的管理控制中心，负责集群内 Node、Namespace、Service、Token、Replication 等资源对象的管理，使集群内的资源对象维持在预期的工作状态。

    每一个 controller 通过 api-server 提供的 restful 接口实时监控集群内每个资源对象的状态，当发生故障，导致资源对象的工作状态发生变化，就进行干预，尝试将资源对象从当前状态恢复为预期的工作状态，常见的 controller 有 Namespace Controller、Node Controller、Service Controller、ServiceAccount Controller、Token Controller、ResourceQuote Controller、Replication Controller等。

kube-scheduler

    kube-scheduler 简单理解为通过特定的调度算法和策略为待调度的 Pod 列表中的每个 Pod 选择一个最合适的节点进行调度，调度主要分为两个阶段，预选阶段和优选阶段，其中预选阶段是遍历所有的 node 节点，根据策略和限制筛选出候选节点，优选阶段是在第一步的基础上，通过相应的策略为每一个候选节点进行打分，分数最高者胜出，随后目标节点的 kubelet 进程通过 api-server 提供的接口监控到 kube-scheduler 产生的 pod 绑定事件，从 etcd 中获取 Pod 的清单，然后下载镜像，启动容器。

ETCD

    强一致性的键值对存储，k8s 集群中的所有资源对象都存储在 etcd 中。

#### Node节点

node节点主要有三个组件：分别是 kubelet、kube-proxy 和 容器运行时 docker 或者 rkt。

kubelet

    在 k8s 集群中，每个 node 节点都会运行一个 kubelet 进程，该进程用来处理 Master 节点下达到该节点的任务，同时，通过 api-server 提供的接口定期向 Master 节点报告自身的资源使用情况，并通过 cadvisor 组件监控节点和容器的使用情况。

kube-proxy

    kube-proxy 就是一个智能的软件负载均衡器，将 service 的请求转发到后端具体的 Pod 实例上，并提供负载均衡和会话保持机制，目前有三种工作模式，分别是：用户模式（userspace）、iptables 模式和 IPVS 模式。

容器运行时——docker

    负责管理 node 节点上的所有容器和容器 IP 的分配。


### ETCD

Etcd是Kubernetes集群中的一个十分重要的组件，用于保存集群所有的网络配置和对象的状态信息。在后面具体的安装环境中，我们安装的etcd的版本是v3.1.5，整个kubernetes系统中一共有两个服务需要用到etcd用来协同和存储配置，分别是：

- 网络插件flannel、对于其它网络插件也需要用到etcd存储网络的配置信息
- kubernetes本身，包括各种对象的状态和元信息配置



### 启动Pods

1. 客户端提交创建请求，可以通过 api-server 提供的 restful 接口，或者是通过 kubectl 命令行工具，支持的数据类型包括 JSON 和 YAML。

2. api-server 处理用户请求，将 pod 信息存储至 etcd 中。

3. kube-scheduler 通过 api-server 提供的接口监控到未绑定的 pod，尝试为 pod 分配 node 节点，主要分为两个阶段，预选阶段和优选阶段，其中预选阶段是遍历所有的 node 节点，根据策略筛选出候选节点，而优选阶段是在第一步的基础上，为每一个候选节点进行打分，分数最高者胜出。

4. 选择分数最高的节点，进行 pod binding 操作，并将结果存储至 etcd 中。

5. 随后目标节点的 kubelet 进程通过 api-server 提供的接口监测到 kube-scheduler 产生的 pod 绑定事件，然后从 etcd 获取 pod 清单，下载镜像并启动容器。

![](https://upload-images.jianshu.io/upload_images/16605471-363ddd93976fbd60.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/759/format/webp)

---

预选阶段的策略有：

(1) MatchNodeSelector：判断节点的 label 是否满足 Pod 的 nodeSelector 属性值。

(2) PodFitResource：判断节点的资源是否满足 Pod 的需求，批判的标准是：当前节点已运行的所有 Pod 的 request值 + 待调度的 Pod 的 request 值是否超过节点的资源容量。

(3) PodFitHostName：判断节点的主机名称是否满足 Pod 的 nodeName 属性值。

(4) PodFitHostPort：判断 Pod 的端口所映射的节点端口是否被节点其他 Pod 所占用。

(5) CheckNodeMemoryPressure：判断 Pod 是否可以调度到内存有压力的节点，这取决于 Pod 的 Qos 配置，如果是 BestEffort（尽量满足，优先级最低），则不允许调度。

(6) CheckNodeDiskPressure：如果当前节点磁盘有压力，则不允许调度。

优选阶段的策略有：

(1) SelectorSpreadPriority：尽量减少节点上同属一个 SVC/RC/RS 的 Pod 副本数，为了更好的实现容灾，对于同属一个 SVC/RC/RS 的 Pod 实例，应尽量调度到不同的 node 节点。

(2) LeastRequestPriority：优先调度到请求资源较少的节点，节点的优先级由节点的空闲资源与节点总容量的比值决定的，即（节点总容量 - 已经运行的 Pod 所需资源）/ 节点总容量，CPU 和 Memory 具有相同的权重，最终的值由这两部分组成。

(3) BalancedResourceAllocation：该策略不能单独使用，必须和 LeaseRequestPriority 策略一起结合使用，尽量调度到 CPU 和 Memory 使用均衡的节点上。

---

