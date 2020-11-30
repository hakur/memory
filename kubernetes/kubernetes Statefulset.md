源码版本以kubernetes v1.19.4为例<br>
控制器代码位于/pkg/controller/statefulset
> ## 特性
* 顺序启动停止pod，名称是固定的，如nginx-0 nginx-1，有利于利用服务访问如statefulset自身配置spec.serviceName: nginx，那么可以使用服务名来访问pod，不需要走做pod dns，则可以curl nginx-0.nginx访问pod nginx-0.
* 不会自动回滚，pod发生错误时不会自动重启，如image 从[centos:7]换到一个[centos:不存在tag] 则会发生错误，此时即使是换回正确的[centos:7]镜像也不会导致pod重启并不会running，会永远卡在error阶段不会重新启动，需要人肉删除pod重启。<br>
    * statefulset 默认策略为rollingupdate的时候 如果podManagementPolicy是默认值OrderedReady 那么statefulset下面的任何一个pod出问题了都不会触发更新也不会重建pod，当podManagementPolicy设置为并发创建Parallel时 将会无视pod是否健康而重建pod
    * [issue](https://github.com/kubernetes/kubernetes/issues/67250)
    * 代码设定/pkg/controller/statefulset/stateful_set_control.go
    ```go
    // If we have a Pod that has been created but is not running and ready we can not make progress.
    // We must ensure that all for each Pod, when we create it, all of its predecessors, with respect to its
    // ordinal, are Running and Ready.
    if !isRunningAndReady(replicas[i]) && monotonic {
        klog.V(4).Infof(
            "StatefulSet %s/%s is waiting for Pod %s to be Running and Ready",
            set.Namespace,
            set.Name,
            replicas[i].Name)
        return &status, nil
    }
    ```
