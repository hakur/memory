> ### 描述
cgroups主要是做资源限制而不是隔离进程。是一个树形结构的继承，子组继承父组，覆盖机制是父设定覆盖子设定。

https://www.kernel.org/doc/Documentation/cgroup-v1/cgroups.txt

https://man7.org/linux/man-pages/man7/cgroups.7.html

* 关键词
    * 子系统 sub system 说的是cgroups的各个控制器，如blkio，cpu这些

> ### 各个cgroups的名称和作用
### cgroups v1
目录位置 /proc/cgroups

* #### blkio 对设备的IO速度进行限制
* #### cpu 控制cpu时间片的分配 最低 kernel 2.6.24
    在分时操作系统 (非实时操作系统) 中，cpu将会按照时间片给OS线程赋予生命周期。在K8S中一个CPU核心的所有时间片被映射为1000个millicores单位，无论cpu任何型号.
    
    根据cgroups man的解释该控制器将保证进程的最少cpu时间片获取量，当cpu不繁忙时将不会限制进程的cpu使用率。在linux3.2中，该控制器被扩展了带宽控制(bandwidth)功能，如果编译内核时使用了参数CONFIG_CFS_BANDWIDTH。
* #### cpuacct 提供了按进程组计算使用率的功能 最低kernel 2.6.24
* #### cpuset 用于将进程绑定到一组cgroup上
### cgroups v2
* 
* 
* 
* 
> ### 树形结构

> ### 使用示范