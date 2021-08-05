* 查看当前集群的节点列表
```sh
kubectl get node
```

* 查看当前集群的资源列表
```sh
kubectl api-resources
kubectl api-verions
```

* 查看当前集群的所有组件状态
```sh
kubectl get componentStatus
```

* 获取所有空间的pod 
```sh
kubectl get pod -A
```

* 获取 kube-system 空间下的所有pod
```sh
kubectl -n kube-system get pod 
```

* 设置 default 空间下所有的 deployment 的 pod数量为1
```sh
kubectl scale deploy --replicas=1 --all
```

* 给 default 空间下的 nginx deployment 更换镜像
```sh
kubectl patch deploy nginx --patch='{"spec":"template":{"spec":"containers":[{"name":"nginx","image":"nginx:1.17"}]}}' --type=merge
```

* 给 default 空间下的 nginx deployment 更换label
```sh
kubectl patch deploy nginx --patch='{"metadata":"labels":{"aa":"bb"}}'
kubectl patch deploy nginx --patch='{"metadata":"labels":{"aa":"bb"}}' --type=merge 
```

* 删除所有空间下被驱逐的pod
```sh
function deleteEvictedPod() {
    kubectl get pod -A | grep Evicted | awk '{print "kubectl delete pod -n " $1 " " $2}' | bash
}

```

* 查看容器日志
```sh
kubectl logs nginx -c main -f
```

* 进入容器执行命令
```sh
kubectl exec -it nginx -c main -- bash
```