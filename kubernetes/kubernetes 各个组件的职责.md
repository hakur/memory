## 简介
### 什么是Kubernetes
源自google Borg的开源大规模容器管理平台，简称k8s。

### 为什么需要Kubernetes
* 大规模集群的容器管理，调度，故障转移
* 容器网络的打通
* 创建资源的校验 admission webhook
* 容器监控
* 升级容器不中断服务
* ......

## 组件简介
### master侧
* #### kube-apiserver
    * 无状态http服务，负责提供restful api和http长连接以供发送各种事件。
    * 负责和etcd做数据交互
    * 多实例时不需要做选举，但需要haproxy+VIP来做高可用

* #### kube-controller-manager
    * 负责连接kube-apiserver，监听各种k8s资源的Add/Update/Delete，并做出反应，控制各种资源的声明周期
    * 多实例时需要启用选举

* #### kube-shceduler 
    * 负责连接kube-apiserver,监听pod资源，调度pod到符合条件的kuebelet节点上。
    * 多实例时需要启用选举

### node侧
* #### kubelet 
    * 负责连接kube-apiserver，上报节点状态信息。
    * 使用内置的cadvisor库收集节点资源信息，比如pod及其容器的信息/磁盘/内存/CPU
    * 根据pod的yaml内容，创建出响应的pod
* #### kube-proxy
    * 负责连接kube-apiserver，监听service endpoint等资源的信息
    * 以IPVS模式为例，根据service和endpoint创建出相应的IPVS路由规则在主机上，以辅助打通容器网络（相关的替代方案也不少，这软件功能比较单一）
    * 负责创建default空间的kubernetes服务的路由规则