# shell
script ，脚本

1. 搞开发的同事让我把他写个脚本可以实现ipset.sh eth0 192.168.1.1 255.255.255.0 192.168.254.254来配置某个网卡的信息

```
#!/bin/bash
hwaddr=ifconfig |grep HWaddr |awk '{print $5}'
echo “DEVICE=$1
HWADDR=$hwaddr
TYPE=Ethernet
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
IPADDR=$2
NETMASK=$3
GATEWAY=$4” > /etc/sysconfig/network-scripts/ifcfg-$1
service network restart
```

2. shell脚本实现将文件中的几行输出合并成一行显示现在有一个文件aa.txt ,其实现格式如下，而我想实现的结果是192.168.1.17 down
```
[root@localhost shell]# cat aa.txt
192.168.1.17
down
192.168.1.103
open
192.168.1.221
open
192.168.1.104
down
192.168.1.175
down
192.168.1.112
open
192.168.1.11
down
[root@localhost shell]# awk ‘{if (NR%2==0){print $0} else {printf”%s “,$0}}’ aa.txt
192.168.1.17 down
192.168.1.103 open
192.168.1.221 open
192.168.1.104 down
192.168.1.175 down
192.168.1.112 open
192.168.1.11 down
```
或者

```
[root@localhost shell]# cat aa.txt | awk ‘{if(NR%2!=0)ORS=” “;else ORS=”\n”;print}’
192.168.1.17 down
192.168.1.103 open
192.168.1.221 open
192.168.1.104 down
192.168.1.175 down
192.168.1.112 open
192.168.1.11 down
```
但是如果aa.txt 的内容如果换种形式
