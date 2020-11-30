源码版本以kubernetes v1.19.4为例<br>
控制器代码位于/pkg/controller/endpoint/endpoints_controller.go

> ## 有什么用
    记录了一个服务下的所有Running的pod的ip列表。ip列表用于kube-proxy添加和更改[iptable、ipvs]规则。
> ## 是如何被创建的
* 创建一个service的时候会创建一个endpoint，这个动作
> ## pod的ip更新（增减）到endpoint subset的流程
    endpoint controller会开启三个个informer来监听资源的变化，
    如pod、service和endpoint。
![avatar](img/ep-informers-code.png)
    当pod变化的时候会将符合service selector的pod列出来做for循环遍历，每个pod会被检查是否有IP,有则会被收录。/pkg/controller/endpoint/endpoints_controller.go#podToEndpointAddressForService函数。