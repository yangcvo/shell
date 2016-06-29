# shell
script ，脚本

##### 1. 搞开发的同事让我把他写个脚本可以实现ipset.sh eth0 192.168.1.1 255.255.255.0 192.168.254.254来配置某个网卡的信息

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

###### 2. shell脚本实现将文件中的几行输出合并成一行显示现在有一个文件aa.txt ,其实现格式如下，而我想实现的结果是192.168.1.17 down
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
或者
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




## sed

##### 1. sed 取某时段内apache的访问日志
开发部有如下需求：
  * 导出 2013-05-24 15:00:00 ～ 2013-05-28 16:00:00 之间的apache访问日志
Apache日志格式为：
```
222.92.115.194 - - [28/May/2013:17:01:00 +0800] "GET /media/js/jquery.eislideshow.js HTTP/1.1" 304 -
222.92.115.194 - - [28/May/2013:17:01:00 +0800] "GET /media/style/tpl/tpl_buy_left/tpl_buy_left.js HTTP/1.1" 304 -
222.92.115.194 - - [28/May/2013:17:01:01 +0800] "GET /favicon.ico HTTP/1.1" 404 17846
222.92.115.194 - - [28/May/2013:17:01:01 +0800] "GET /large-display/interactive/ HTTP/1.1" 200 21382
222.92.115.194 - - [28/May/2013:17:01:02 +0800] "GET /favicon.ico HTTP/1.1" 404 17846
222.92.115.194 - - [28/May/2013:17:01:04 +0800] "GET /large-display/single/ HTTP/1.1" 200 21386
222.92.115.194 - - [28/May/2013:17:01:04 +0800] "GET /favicon.ico HTTP/1.1" 404 17846
222.92.115.195 - - [28/May/2013:17:01:05 +0800] "GET /dsc/ HTTP/1.1" 200 34530
222.92.115.195 - - [28/May/2013:17:01:05 +0800] "GET /media/img/channel_icon.jpg HTTP/1.1" 404 17846


截取命令：

[root@style logs]# sed -n '/24\/May\/2013:15:00:01/,/28\/May\/2013:16:59:58/p' xxxx-access_log > 20130524.15-20130528.16-access_log.txt

PS:需要注意的是如果起始时间在日志中不存在，则整个截取将返回 0 行结果。而如果结束时间在日志中不存在，则会截取到日志的最后一条。所以在截取前得要找到最日志中最合适的起始点和结束点。
我的做法是先使用grep去找到两个点  再使用sed去截取

# 找出 2013-05-24 15点第一条记录的时间
[root@style logs]# grep '24/May/2013:15' xxxx-access_log | head -1
10.200.114.183 - - [24/May/2013:15:00:01 +0800] "GET /gp10/pic_259_218_1368781965.png HTTP/1.0" 401 484
# 找出 2013-05-28 16点最后一条记录的时间
[root@style logs]# grep '28/May/2013:16' xxxx-access_log | tail -1
222.92.115.195 - - [28/May/2013:16:59:58 +0800] "GET /favicon.ico HTTP/1.1" 404 17846
# 然后取这两个时间段之间的记录
```
