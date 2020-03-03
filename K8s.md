# K8s

## Basic Concept

##### Master的组件

API Server : 所有服务访问的统一入口

Controller Manager : 维持副本期望数；

Scheduler:  服务接收任务，选择合适的节点进行分配任务；

etcd: 键值对数据库，存储K8S集群所有重要信心（持久化）；

##### Node组件：

Kubelet: 直接跟容器引擎交互，实现容器的生命周期管理

Kube-proxy：负责写入规则至IPTABLES、IPVS，实现服务映射访问；

#####　其它组件

coreDNS ：可以为集群中的SVC创建一个域名与IP的对应关系解析，是实现负载功能的其中一项

Dashboard 为K8S集群提供了一个Ｂ/S结构访问体系；

INGRESS CONTROLLER :　官方只能实现四层代理，INGRESS可以实现七层代理;

FEDETATION：提供一个可以跨集群中心多K8S的统一管理功能；

Prometheus：提供一个K8S集群的监控能力；

ELK :　提供K8S集群日志统一分析接入平台；



## POD

分类：自主Pod，控制器管理的Pod





## 常见指令

#### kubectl

```shell
# view the nodes in the cluster
kubectl get nodes 

# create a deployment,name it kubernetes-bootcamp which image is from grc.io/....
kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1

# list your deployments 
kubectl get deployments
```



##### kubectl get pods 

列出pods资源

**kubectl describe** 展示某个资源的详细信息

**kubectl logs** 打印pod中某个container的log

**kubectl exec** 在pod中某个container执行一个指令，

例如 kubectl exec $POD_NAME env 可以查看pod的环境变量env

例如 kubectl exec -ti $POD_NAME bash  可以进入pod中的container的bash内

[参考](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-interactive/)