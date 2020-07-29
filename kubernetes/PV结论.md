需求
1.本地卷  hadoop要求高性能磁盘
2.磁盘剩余检测不足不分配pod
3.分配10G 就不能超过10G

emptydir 超过10G 导致pod被删除 丢失数据 

所有的本地卷都不具备容灾，数据只有一份

loopback device 
    1.可以挂载为磁盘后限制写入的大小，非常精准
    2.超级大的单文件，可能发生断裂，且寻址理论上速度不会比物理磁盘快，创建速度超级慢
    3.需要自己开发storageClass
emptydir 
    1.本地盘，速度快
    2.无法限制写入大小，容量会超标，超标后pod驱逐数据丢失，对于hadoop这种海量数据读写处理风险极高
    3.挂载位置为/var/lib/kubelet/pods/{$PodUid}/volumes/kubernetes.io~empty-dir/{$你在volume节点指定的文件夹名称}
local persistent volume 
    1.本地盘，速度快，无法限制写入大小（根据挂入的类型是fileSystem还是block决定），不存在超出使用量导致的 驱逐导致的数据丢失
    2.节点挂，他也挂，容器内的程序需要能够容忍他挂掉的情况
    3.使用起来很麻烦，只支持静态pv，需要提前创建很多静态pv，同时pv需要设置节点亲和nodeAffinity，使用起来比较麻烦

ceph rbd （块设备）
    1.可以挂载为磁盘后限制写入的大小，非常精准，多份冗余容灾备份，随时扩容
    2.网络盘，速度没有本地盘快


mknod -m 0660 /dev/loop8 b 7 8

dd if=/dev/zero of=/root/yuxing/virtualfs bs=4096 count=30720
dd if=/dev/zero of=/root/yuxing/virtualfs bs=1024 count=23552000
dd if=/dev/zero of=/root/yuxing/virtualfs bs=64M count=100

losetup /dev/loop0 /root/yuxing/virtualfs

mkfs -t xfs /dev/loop0 #这句要出错 如果loop设备被占用了
#mkfs -t ext3 -m 1 -v /dev/loop0

mkdir /root/yuxing/vfs

mount -t xfs /dev/loop0 /root/yuxing/vfs

/host_mnt/c/Users/yuxing/.docker/Volumes/centos-pv-claim/

后续问题
设 lvm卷在 挂在 /data/lvm0

如果现有盘sda作系统盘,sdb数据盘,那么客户会增加第三块sdc么,且每台服务器的磁盘个数还不一样,那么加磁盘的时候 ,需要人肉设置一下lvm新增磁盘或者写一段代码让sdc自动加入lvm卷，自动加入lvm卷则引申出什么标记的卷需要加入进去

