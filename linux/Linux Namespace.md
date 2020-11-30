> 文章引用：
* [一文搞懂 Linux network namespace](https://www.cnblogs.com/bakari/p/10443484.html)
> ### 描述
    隔离进程之间的互相发现和冲突。
> ### 各个命名空间的作用和名称
* ### network namespace
    网络命名空间的作用主要是隔离网络层的互相发现并虚拟出一个网络来。主要存在于ip netns子命令中

> ### 网络命名空间network namespace
* #### 添加一个网络命名空间
    ip netns add ${namespace}
* ### 列出网络命名空间
    ip netns ls
* ### 让一个程序在网络命名空间下运行
    ip netns exec ${namespace} ${binPath} ${binArgs}
* ### 让不同的网络命名空间互相通信
    默认情况下不同的网络命名空间是隔离的，无法互相通信。需要veth来连接两个网络命名空间。<br>
    ip link add type veth 
