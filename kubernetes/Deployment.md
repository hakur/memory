## 简介
> ### 什么是deployment
    是一个高级的pod生命周期控制器，简写 deploy。
    kubectl get deploy -A
    
    https://kubernetes.io/zh/docs/tasks/run-application/run-stateless-application-deployment/
    https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/
> ### 为什么需要
* 因为它会帮自动创建ReplicaSet,简称RS，而RS控制着pod的生命周期。
* 故障转移，自动创建pod副本数到期望个数
* 滚动升级pod镜像，升级期间不中断服务，升级失败则回滚，升级成功则清理旧的Pod

> ### yaml样本
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```