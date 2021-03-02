---
title: kubenetes
date: 2020-06-02 11:16:14
tags:
disqusId: aitudou-blog
---

## Kebetenes 学习2

### Namespace:

Namespace（命名空间）是kubernetes系统中的另一个重要的概念，通过将系统内部的对象“分配”到不同的Namespace中，形成逻辑上分组的不同项目、小组或用户组，便于不同的分组在共享使用整个集群的资源的同时还能被分别管理。

Kubernetes集群在启动后，会创建一个名为“default”的Namespace，如果不特别指明Namespace，则用户创建的Pod、RC、Service都被系统创建到“default”的Namespace中。

<!-- More -->

1. 查询
```
[root@localhost k8s]# kubectl get namespaces
NAME      STATUS    AGE
default   Active    6d
```

2. 创建
(假设新的namespace叫develop)

```
1. kc create namespace develop
2. kubectl create secret docker-registry regcred-develop --docker-server={server} --docker-username={username} --docker-password={password} -n=develop
```

3. 公共config

```
kc apply -f data-config.yml -n=develop

```

4. yml文件 自己的service yml文件, 所有metadata里添加"namespace: {namespace}", namespace字段和name平级

__port唯一__ 这个很重要 即使是在不同的namespace

5. command line
之后的command line 如果要specify namespace --> -n={namespace}