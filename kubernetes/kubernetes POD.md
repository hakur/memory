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
* #### 大使ambassador模式
    和sidecar模式差不多，但是大使pod是作为网络代理程序的形式而存在
* #### sidecar模式
    其实就是第2,3,4...个容器，没什么特色

> ### pod网络
* #### pause容器(kubelet的--pod-infra-container-image参数)主要是提供了pod内所有容器网络网络命名空间共享的功能
    由于pod内是有多个container的，但是不同的container在docker创建他们的时候默认是不同的命名空间。
    因此为了能够满足容器A监听80端口，容器B监听88端口，容器B使用127.0.0.1访问容器A的80端口，所以引入了pause容器来创建namespace组，并将容器A和容器B的网络namespace挂到pause容器上。