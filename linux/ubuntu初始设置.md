ubuntu从 17.10后放弃从/etc/network/interfaces 设置IP

改成了 /etc/netplan/*.yaml 如 
```yaml
# /etc/netplan/00-installler-config.yaml
network:
  ethernets:
    eth0:
      dhcp4: no
      addresses: [192.168.1.4/24]
      gateway4: 192.168.1.1
      optional: true
      nameservers:
        addresses: [223.5.5.5,223.6.6.6]

```
[文章1](https://liyiping.cn/article/linux-network-resolve/)
[文章2](https://www.cnblogs.com/zhangshenghui/p/6512507.html)
systemd-resolvd 会强制修改/etc/resolv.conf 需要修改它的配置文件 /etc/systemd/resolved.conf
```conf
[Resolve]
DNS=192.168.0.175 114.114.114.114
#FallbackDNS=
#Domains=
LLMNR=no
#MulticastDNS=no
#DNSSEC=no
#DNSOverTLS=no
#Cache=yes
#DNSStubListener=yes
#ReadEtcHosts=yes
~     
```