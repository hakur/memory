> ### 什么是Pod
  pod 是k8s最小的资源分配调度单位，是一种虚拟的资源，申明方式为yaml文件，存储在etcd中。kubelet会根据pod yaml描述的内容去创建容器。

  一个pod内可以有多个容器，默认情况下这些容器的网络是互通的，因为他们都在一个网络命名空间下。
> ### 基本形态
```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
  namespace: default
spec:
  volumes:
  - name: data
    hostPath:
      path: /my/data
  containers:
  - image: nginx
    name: nginx
    imagePullPolicy: Always
    restartPolicy: Always
    volumeMounts:
    - name: data
      mountPath: /data
    resources:
      limits:
        cpu: "100m"
        memory: "1000Mi"
      request:
        cpu: "125m"
        memory: "1250Mi"
    command:
    - nginx
    args:
    - "-g 'daemon off;'"
    ports:
    - containerPort: 80
      hostPort: 80 # 等同于docker-p 80:80 通常不需要写这个
  initContainers:
  # ...和containers节点一模一样
```
> ### pod内的容器部署形式
* #### sidecar模式
  其实就是第2,3,4...个容器，没什么特色
* #### 大使ambassador模式
  和sidecar模式差不多，但是大使pod是作为网络代理程序的形式而存在
* #### 主容器
  通常指的是第一个被申明的起主要功能的容器，比如n一个pod内申明的nginx容器

> ### pod网络
* #### pause容器(kubelet的--pod-infra-container-image参数,但在1.21之后这个参数交给了CRI接口的实现者，比如contianerd)主要是提供了pod内所有容器网络网络命名空间共享的功能
    由于pod内是有多个container的，但是不同的container在docker创建他们的时候默认是不同的命名空间。
    因此为了能够满足容器A监听80端口，容器B监听88端口，容器B使用127.0.0.1访问容器A的80端口，所以引入了pause容器来创建namespace组，并将容器A和容器B的网络namespace挂到pause容器上。


> ### label 标签过滤

标签的意义在于它能够作为一组pod的筛选条件，选出我们想要的某一/几个pod

```yaml
kind: Pod # 资源类型是Pod，必须申明
apiVersion: v1 # 资源出自那个api组，对应的go代码 k8s.io/api/core/v1
metadata: # meta元素信息
  name: nginx # pod的名称是什么
  namespace: default # 资源将被创建到哪个空间下面
  labels: # 标签申明，这将用于给其他高级控制器或client-go sdk 做选择过滤
    mylabel-1: myvalue-1 # 标签1
    mylabel-2: myvalue-2 # 标签2
```

> ### pod 亲和与反亲和
pod的亲和与反亲和，表现为pod yaml 上的字段 podAffinity 和 podAntiAffinity。

https://kubernetes.io/zh/docs/concepts/scheduling-eviction/assign-pod-node/

pod亲和的意义在于能将pod调度到我们期望的节点上，这对于gpu某些磁盘敏感或需要不同型号gpu的pod有很大的帮助。

pod反亲和的意义在于能让具有相同label的pod不会被调度到同一个kubelet节点上，这对于hostNetwork=true的pod非常有用。
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-pod-affinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
        topologyKey: topology.kubernetes.io/zone
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: security
              operator: In
              values:
              - S2
          topologyKey: topology.kubernetes.io/zone
  containers:
  - name: with-pod-affinity
    image: k8s.gcr.io/pause:2.0
```

> ### 污点与容忍

https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/

污点通常会设置在 k8s node yaml 上，即kubectl get node 的那个node资源。

设置污点只是为了想要驱逐不想要的pod，比如一个节点上有gpu资源，但是纯cpu的pod被调度到这个节点，进而导致资源被占用，再而导致gpu pod 没有节点可以调度，这就很不好了。

因此需要给gpu节点的yaml 设置 taits 标签，然后节点会驱逐这些pod到其他节点上。

此时再给gpu pod加上tolerations，即容忍。容忍gpu的这个污点标签，就能调度到gpu节点上运行了。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "example-key"
    operator: "Exists"
    effect: "NoSchedule"
```

> ### 资源请求与限制 requests & limits

https://kubernetes.io/zh/docs/tasks/configure-pod-container/assign-cpu-resource/

假如有一个elsatic search的pod需要支持很高的读写操作，且库中数据量巨大。

此时，没有设置内存资源限制，则这个pod因为读取和写入了太多的文件，将导致操作系统的buff/cache内存特别高。同时造成这个pod和操作系统以及其他pod争夺计算资源。

为了避免上述请求，设置pod的资源请求（requests）申明pod最少需要多少cpu和内存资源，通过限制（limits）来控制pod最多能使用多少cpu和内存资源，这样可以防止资源的争夺并保证pod能获得它所需的最小资源数量。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cpu-demo
  namespace: cpu-example
spec:
  containers:
  - name: cpu-demo-ctr
    image: vish/stress
    resources:
      limits:
        cpu: "1"
      requests:
        cpu: "0.5"
    args:
    - -cpus
    - "2"
```

> ### pod 漂移

设创建pod的方法不是纯pod，而是通过高级控制器如statefulset deployment等创建的。

作为K8S故障转移能力的体现之一，当kubelet节点出现问题的时候，比如磁盘快满了，内存快满了，失去联系等原因。

将会启动将运行中的pod或失联的pod重新调度到其他健康且符合条件的节点上。

在这个过程中，pod将会被重建，并保持现有的podIP,但pode的nodeIP字段将会发生变化。

如果pod是人工删除的，则pod会被重建到符合条件的节点上，由于是重建，此时podIP和nodeIP都变了。

> ### 数据安全性 持久卷 persistent volume PV
https://kubernetes.io/zh/docs/concepts/storage/persistent-volumes/

Pod总是需要一个外部数据存储目录的，K8S的解决办法是 PV（原始底层，建议使用高层对象PVC和StorageClass）

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pv-recycler
  namespace: default
spec:
  restartPolicy: Never
  volumes:
  - name: vol
    hostPath:
      path: /any/path/it/will/be/replaced
  containers:
  - name: pv-recycler
    image: "k8s.gcr.io/busybox"
    command: ["/bin/sh", "-c", "test -e /scrub && rm -rf /scrub/..?* /scrub/.[!.]* /scrub/*  && test -z \"$(ls -A /scrub)\" || exit 1"]
    volumeMounts:
    - name: vol
      mountPath: /scrub
```


> ### yaml 进阶形态
```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: nginx
data:
 MY_NAME: aaa

---

kind: Pod # 资源类型是Pod，必须申明
apiVersion: v1 # 资源出自那个api组，对应的go代码 k8s.io/api/core/v1
metadata: # meta元素信息
  name: nginx # pod的名称是什么
  namespace: default # 资源将被创建到哪个空间下面
  labels: # 标签申明，这将用于给其他高级控制器或client-go sdk 做选择过滤
    mylabel-1: myvalue-1 # 标签1
    mylabel-2: myvalue-2 # 标签2
spec: # pod控制器创建POD时所需的内容申明
  # ephemeralContainers: # 临时容器组 https://kubernetes.io/zh/docs/concepts/workloads/pods/ephemeral-containers/
  # - name: test
  #   image: docker.io/library/busybox:1.33.1 # 镜像名称
  #   imagePullPolicy: IfNotPresent # 镜像下载策略为IfNotPresent,表示如果磁盘上没有就去执行镜像pull操作，开发环境建议使用Always，以获得每次创建容器都去更新一次镜像的能力。 https://pkg.go.dev/k8s.io/api/core/v1#PullPolicy
  #   env:
  #   - name: POD_NAME
  #     valueFrom:
  #       fieldRef: # 引用pod自己的申明yaml文件的内容
  #         apiVersion: v1 # 默认值v1，可以不写
  #         fieldPath: metadata.name # 引用自上面的metadata字段
  #   - name: POD_IP
  #     valueFrom:
  #       fieldRef: # 引用pod自己的申明yaml文件的内容
  #         apiVersion: v1 # 默认值v1，可以不写
  #         fieldPath: status.podIP # 虽然我们没有写status.podIP,但在pod内的容器创建之前就会分配到POD IP，但这个过程再存入etcd之后
  #   - name: COMBO
  #     value: $(POD_NAME)_$(POD_IP) # 这种括号会被存入etcd之前就解析为具体的值，之后再存入etcd，然后再创建POD资源。因此括号引用的变量必须再env字段申明，且必须再本字段之前申明
  #   command: # docker file 的 COMMAND 字段，容器的init进程
  #   - /bin/bash
  #   - -c
  #   args:
  #   - |
  #     #!/bin/bash
  #     while [ true ] ;do
  #       echo $(date) ${POD_NAME} $(POD_IP) ${COMBO}
  #       sleep 1
  #     done
  initContainers: #初始化容器组
  - name: hello
    image: docker.io/library/busybox:1.33.1 # 镜像名称
    imagePullPolicy: IfNotPresent # 镜像下载策略为IfNotPresent,表示如果磁盘上没有就去执行镜像pull操作，开发环境建议使用Always，以获得每次创建容器都去更新一次镜像的能力。 https://pkg.go.dev/k8s.io/api/core/v1#PullPolicy
    env:
    - name: DIR
      value: /bin
    envFrom:
    - configMapRef:
        name: nginx
    command: # docker file 的 COMMAND 字段，容器的init进程
    - echo
    args:
    - ${DIR} ${MY_NAME}
  containers: # 标准容器组
  - name: nginx
    image: nginx:1.18
    imagePullPolicy: IfNotPresent
    resources: # 设置资源限制与请求，为容器提供稳定的CPU和内存，字段非必须，但如果不设置内存limits，将会导致kubelet所在的操作系统 buff/cache 剧增，因为任何的文件IO都会引发buff/cache增加，释放机制为linux内存不足时释放，如果容器有内存限制则容器带来的buff/cache不会超过这个限制且会基于容器总内存决定是否释放buff/cache内存。
      # 扩展resource的方法是编写 k8s device plugin，比如nvidia显卡资源的申请就是 nvidia/k8s-device-plugin
      requests: # 最小资源需求，必须小于等于limits字段
        cpu: 100m
        memory: 128Mi
      limits: # 最大资源使用量，必须大于等于requests,建议和requests值相同或1.3倍于requests的值
        cpu: 100m
        memory: 128Mi
    ports:
    - containerPort: 80
      name: http # prometheus-operator 的 kind: ServiceMonitor 会用到这个字段
  hostNetwork: true # 如果使用host网络则kubelet所在主机会开启一个80端口 并将流量路由到nginx容器中，通常建议关闭这种模式，流量走k8s集群的cni网络
  tolerations: # 对节点的污点标签的容忍申明 https://pkg.go.dev/k8s.io/api/core/v1#Toleration
  - effect: NoSchedule # pod 不容忍节点污点时的驱逐方式。 https://pkg.go.dev/k8s.io/api/core/v1#TaintEffect  NoSchedule:不调度新的pod到这个节点上，但允许现有的pod继续运行在当前节点， NoExecute: 立即驱逐任何状态的Pod，且不允许新的pod调度到当前节点， 
    key: master-node.hakurei.cn
    operator: Exists # 比较方式。 Exists | Equal
    # value: "true" # 可选参数 operator=Exists 时，不能设置这个参数
    # tolerationSeconds: 300 # 可选参数，容忍这个污点多少秒，时间结束后，pod将被调度到其他能够容忍的地方。只有effetct=NoExecute时才能设置
  
```