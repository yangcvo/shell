# shell
script ，脚本

### 搞开发的同事让我把他写个脚本可以实现ipset.sh eth0 192.168.1.1 255.255.255.0 192.168.254.254来配置某个网卡的信息

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
