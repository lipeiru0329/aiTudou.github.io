---
title: kubetenetes-pods
date: 2020-06-08 12:52:14
tags:
---

## Kubetenetes - Pods Deployment Service

### Pods: 

In Kubernetes, the pod is the smallest deployable unit. It can be wrapped one or more containers inside the single pod. containers that are inside the pod share __the same resources and network__.
<!-- More -->

![](https://miro.medium.com/max/1400/1*s_obmSDgj5PULX6BDRPdrw.png)

### Deployment

Deployment is the next level of Pod. Deployment manages pods’ Life cycle. Anything which comes to pod goes through the deployment component. Responsible for monitoring. One of the main differences in deployment is to control the number of replicas. You do not need to maintain it manually when you use deployment.

![](https://miro.medium.com/max/1400/1*8D4NNejM0N68I7YekxxzPA.png)

这个就是所谓的yaml文件：

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      run: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
        resources: {}
status: {}
```

To check running deployments,
```
$ kubectl get deployments
```

__%% 如果需要用share input文件，需要创建secret (config) %%__
```
$ kubectl create secret generic {secret name} --from-env-file=./{config file name}.env
$ kubectl get secret {secret name}
```

查询pod详情
```
kubectl describe pod {pod name}
```

%% 如果发现pod起不来等等问题，可以用describe看一下config，因为有时候更改yaml文件，重启之后，pod并不会更新，所以说需要删除pod，然后重建新的pod

启动服务
```
kubectl apply -f {name}.yml
```
替换服务
```
kubectl replace -f {name}.yml
```
重启
```
kc get deployment

kc rollout restart deployment <deployment>

kc get pods
```
Delete
```
kubectl delete replicaset {pod-name}
kubectl delete deployment {deployment-name}
kubectl delete pod {pod-name}

kubectl delete replicaset.apps/{REPLICASET} deployment.apps/{DEPLOYMENT} /{SERVICE} pod/{POD} 
```

查看日志
```
kc logs {POD_NAME}
```

### Service:

If you want to communicate between services and want to communicate with outside the cluster, you have to define a service.

service: 是为了解决pod 重启之后 IP 变化的问题， --> Pod 会终止，Deployment 将创建新的 Pod，且使用不同的 IP。这正是 Service 要解决的问题。

service同时可以实现外部映射接口的问题，同时service的config也可以卸载yaml文件里面
```
apiVersion: v1
kind: Service
metadata:
  name: {name}
spec:
  selector:
    name: {name}
  type: NodePort
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
      nodePort: {external port}
```
这样的话，启动yaml的时候就可以直接全部启动deployment和service
```
kc get services
```

### Secret

Secret 对象类型用来保存敏感信息，例如密码、OAuth 令牌和 ssh key。 将这些信息放在 secret 中，并降低暴露的风险。

要使用 secret，pod 需要引用 secret。Pod 可以用两种方式使用 secret：作为 volume 中的文件被挂载到 pod 中的一个或者多个容器里，或者当 kubelet 为 pod 拉取镜像时使用。