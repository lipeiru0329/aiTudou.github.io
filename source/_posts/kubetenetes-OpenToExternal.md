---
title: kubetenetes-OpenToExternal
date: 2020-06-08 13:39:33
tags:
---

## Kubetenetes -- Open to external

K8S一共有三个方式将集群外部流量导入到集群内的方式或者说暴露出去的方式，NodePort，LoadBalancer 和 Ingress

%% 这部分都是service里面的yaml 文件的配置

比如说 ClusterIP的service
```
apiVersion: v1
kind: Service
metadata:  
    name: my-internal-service
spec:
    selector:    
        app: my-app
    type: ClusterIP
    ports:  
        - name: http
          port: 80
          targetPort: 80
          protocol: TCP
```

<!-- More -->

### ClusterIP

ClusterIP 服务是 Kubernetes 的默认服务。它给你一个集群内的服务，集群内的其它应用都可以访问该服务。集群外部无法访问它。

```
apiVersion: v1
kind: Service
metadata:  
    name: my-internal-service
spec:
    selector:    
        app: my-app
    type: ClusterIP
    ports:  
        - name: http
          port: 80
          targetPort: 80
          protocol: TCP
```

但是啊 从Internet 没法访问 ClusterIP 服务，但是我们可以通过 Kubernetes 的 proxy 模式来访问该服务！

### NodePort

NodePort 服务是引导外部流量到你的服务的最原始方式。NodePort，正如这个名字所示，在所有节点（虚拟机）上开放一个特定端口，任何发送到该端口的流量都被转发到对应服务

![](http://dockone.io/uploads/article/20190626/58174ac44fdbacbbc89cec648260fcdf.png)

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
      nodePort: 30624
```

### LoadBalance

LoadBalance(负载均衡 LB)通常由云服务商提供. 如果环境不支持LB, 那么创建的LoadBalance将始终处于`<pending>`状态
```
[root@nas-centos1 k8s-test]# kubectl get services
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
k8s-test-service   LoadBalancer   10.244.29.126   <pending>     80:32681/TCP   13s
kubernetes         ClusterIP      10.244.0.1      <none>        443/TCP        33d
```
如果想要在本地开发环境测试LB, 我们可以选择[MetalLB](https://metallb.universe.tf/). 它是一个负载均衡实现. 安装方式此处不进行展开, 可参考官方文档

```
apiVersion: v1
kind: Service
metadata:
  name: k8s-test-service
spec:
  selector:
    app: k8s-test
    env: test
  ports:
    - port: 80	        # 服务端口
      targetPort: 8080	# 目标端口, 此处指的是pod的8080端口
      protocol: TCP
  type: LoadBalancer
```

```
[root@nas-centos1 k8s-test]# kubectl get services
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
k8s-test-service   LoadBalancer   10.244.151.128   10.33.30.2    80:31277/TCP   2s
kubernetes         ClusterIP      10.244.0.1       <none>        443/TCP        33d
```

然後10.33.30.2就可以使用了

### Ingress:

Unlike all the above examples, Ingress is actually NOT a type of service. Instead, it sits in front of multiple services and act as a “smart router” or entrypoint into your cluster.

The default GKE ingress controller will spin up a HTTP(S) Load Balancer for you. This will let you do both path based and subdomain based routing to backend services. For example, you can send everything on foo.yourdomain.com to the foo service, and everything under the yourdomain.com/bar/ path to the bar service.

it similiar to the K8S NodePort + Ngnix

![](https://miro.medium.com/max/1400/1*KIVa4hUVZxg-8Ncabo8pdg.png)

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: my-ingress
spec:
  backend:
    serviceName: other
    servicePort: 8080
  rules:
  - host: foo.mydomain.com
    http:
      paths:
      - backend:
          serviceName: foo
          servicePort: 8080
  - host: mydomain.com
    http:
      paths:
      - path: /bar/*
        backend:
          serviceName: bar
          servicePort: 8080
```