# 解决linux 大量TIME_WAIT

```
netstat  -anp | grep TIME_WAIT | wc -l 统计数量
```

# 解决方案
```
vi /etc/sysctl.conf
```

# 加入以下参数
```
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 30
```

# 执行
```
/sbin/sysctl -p
```

sysctl -a 查看全部内核参数变量