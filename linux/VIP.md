### 是什么？
    vip是virtual ip的简写，也是网卡别名，一张网卡可以有多个IP绑定。主要目的是发送ARP广播并导流到本机。
### 为什么？
    在vip的实际应用典例keepalived中，keepalived通过组播地址224.0.0.18发送选举信号，成功选举则添加VIP并发送ARP广播，以便让流量能转发过来。
    * 小心，如果组播被关闭，则所有keepalived都会发送ARP广播导致混乱
### 怎么办？
    比如网卡是eht0 给网卡建立别名 :1 即eth0:1，vip地址192.168.1.2，则命令是
    ifconfig eth0:1 192.168.1.4 broadcast 192.168.1.255 netmask 255.255.255.0 up
    然后发送ARP广播
    route add -host 192.168.1.4 dev eth0:1

    数据包从 eth0:1 到 eth0 再被发送出去