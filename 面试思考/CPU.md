[文章引用](https://www.cnblogs.com/lubinlew/p/cpu_affinity.html)
# 1. CPU0
> ### 描述
    指的都一个物理CPU上是第1块核心
* #### 查看物理CPU个数
    cat /proc/cpuinfo|grep "physical id"|sort -u|wc -l
* #### 查看每个物理CPU中core的个数(即核数)
    cat /proc/cpuinfo|grep "cpu cores"|uniq
* #### 查看逻辑CPU的个数
    cat /proc/cpuinfo|grep "processor"|wc -l
* #### 查看CPU的名称型号
    cat /proc/cpuinfo|grep "name"|cut -f2 -d:|uniq
* #### 查看进程在哪个核心
    ps -eo pid,args,psr | grep nginx
> ### 负责的任务
* 
> ### 如何保护CPU0
* windows在任务管理器界面右击进程-选择相关性-然后取消cpu0的勾。命令如 cmd.exe /C start /affinity 6 notepad.exe,上述操作都是临时的设定CPU亲和性。
* linux使用taskset命令
# 2.CPU中断
> ### 描述
    cpu发生中断后会保存当前进程的上下文到内存，然后切换其他进程的上下文来执行。切换上下文是有性能开销的。
> ### 触发条件
* 程序发生了磁盘I/O(磁盘读取主要是cpu发出指令，主板来执行I/O并读到内存或从内存中写到磁盘)
> ### 影响

# 3.CPU缓存
> ### 一级缓存

> ### 二级缓存

> ### 三级缓存。


# 4.CPU的内存通道
> ### 描述


# 5.CPU的Numa模式
> ### 描述

# 6.超线程技术(Hyper-Threading)