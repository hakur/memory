> ## 描述
    kubenetes开放了外部硬件资源的汇报和分配接口，叫做DevicePlugin。
    一个device plugin程序运行在OutTree（即不需要嵌入k8s源码中编译）。如果运行在容器中则需要给与privileged=true的权限，否则将无法访问硬件资源。
    一个device plugin程序启动后，需要通过grpc接口连接kubelet。自身启动后需要用某个数据结构来维护设备列表，如slice或者链表。
    一个device plugin最好是只负责一种类型的硬件如tty设备或者显卡设备

> ## 接口
* ### Resgister (kubeletEndpoint string, resourceName string) error
    这个接口主要是负责连接并注册自己给kubelet
* ### Allocate (ctx context.Context, reqs *pluginapi.AllocateRequest) (*pluginapi.AllocateResponse, error)
    这个接口主要负责处理来自kubelet的硬件资源申请，所以一个device plugin在启动的时候必须建立一个数据接口如slice或者链表来保存设备列表，并在本函数中取出一个赋值到返回值上以返回给kueblet。
* ### ListAndWatch(e *pluginapi.Empty, s pluginapi.DevicePlugin_ListAndWatchServer) error
    这个接口是kubelet定期调用的接口，负责上报设备列表和设备健康状态。

> ## 实现范例
[k8s-tty-device](https://github.com/hakur/k8s-tty-device)