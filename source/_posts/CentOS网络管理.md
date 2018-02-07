---
title: CentOS网络管理
date: 2018-01-30 21:08:09
tags:
---

linux作为大部分服务器采用的系统,网络管理是最基本的一个环节,下面来介绍一下CentOS的网络管理功能

## IP地址
### 分类
```
A类：
    第一段为网络号，后三段为主机号
    网络号：
        0 000 0000 - 0 111 1111：1-127
    网络数量：126，127
    每个网络中的主机数量：2^24-2
    默认子网掩码：255.0.0.0，/8
        用于与IP地址按位进行“与”运算，从而取出其网络地址；
            1.3.2.1/255.0.0.0 = 1.0.0.0
            1.3.2.1/255.255.0.0= 1.3.0.0	
    私网地址：10.0.0.0/255.0.0.0-10.255.255.255/255.0.0.0
B类：
    前两段为网络号，后两段为主机号
    网络号：
        10 00 0000 - 10 11 1111：128-191
        网络数：2^14
        每个网络中的主机数量：2^16-2
        默认子网掩码：255.255.0.0，/16
        私网地址：172.16.0.0-172.31.255.255							
C类：
    前三段为网络号，最后一段为主机号
    网络号：
        110 0 0000 - 110 1 1111：192-223
    网络数：2^21
    每个网络中的主机数量：2^8-2
    默认子网掩码：255.255.255.0,  /24
    私网地址: 192.168.0.0-192.168.255.255
D类：组播
    1110 0000 - 1110 1111：224-239
E类：科研
    240-255
```
## 端口
端口是通信时，进程的数字标识
### 分类
```
16bits：
    0-65535：1-65535
        1-1023：固定分配，而且只有管理员有权限启用；
        1024-4W：半固定，
        4W+：临时；
```
## 总结
1. MAC：本地通信；范围：本地局域网；
2. IP：界定通信主机，源和目标；范围：互联网；
3. Port：界定进程；范围：主机 ；

## 配置管理
### 分类
1. ifcfg家族：
    - ifconfig：配置IP，NETMASK
    - route：路由
    - netstat：状态及统计数据查看
2. iproute2家族：
    - ip OBJECT：
    - addr：地址和掩码；
    - link：接口
    - route：路由  
ss：状态及统计数据查看
3. CentOS 7：nm(Network Manager)家族
    - nmcli：命令行工具
    - nmtui：text window 工具
### 配置文件
    /etc/sysconfig/network-scripts/ifcfg-NETCARD_NAME

以上是静态配置IP的方法,如果要动态分配,则依赖于本地网络中有DHCP(Dynamic Host Configure Procotol)服务
### 网络接口命名方式：
#### 传统命名：
- 以太网：ethX, [0,oo)，例如eth0, eth1, ...
- PPP网络：pppX, [0,...], 例如，ppp0, ppp1, ...

#### 可预测命名方案（CentOS）：

1. 如果Firmware或BIOS为主板上集成的设备提供的索引信息可用，则根据此索引进行命名，如eno1, eno2, ...
2. 如果Firmware或BIOS为PCI-E扩展槽所提供的索引信息可用，且可预测，则根据此索引进行命名，如ens1, ens2, ...
3. 如果硬件接口的物理位置信息可用，则根据此信息命名，如enp2s0, ...
4. 如果用户显式定义，也可根据MAC地址命名，例如enx122161ab2e10, ...
上述均不可用，则仍使用传统方式命名；

#### 命名格式的组成：
- en：ethernet
- wl：wlan
- ww：wwan

#### 名称类型：
- o\<index\>：集成设备的设备索引号；
- s\<slot\>：扩展槽的索引号；
- x\<MAC\>：基于MAC地址的命名；
- p\<bus\>s\<slot\>：基于总线及槽的拓扑结构进行命名；

## iproute家族： 
### 用法			
```
ip [ OPTIONS ] OBJECT { COMMAND | help }
OBJECT := { link | addr | route | netns  }
```

#### ip link： network device configuration
```
ip  link  set - change device attributes
dev NAME (default)：指明要管理的设备，dev关键字可省略；
up和down：
multicast on或multicast off：启用或禁用多播功能；
name NAME：重命名接口
mtu NUMBER：设置MTU的大小，默认为1500；
netns PID：ns为namespace，用于将接口移动到指定的网络名称空间；

ip  link  show  - display device attributes

ip  link  help -  显示简要使用帮助；
```
#### ip netns：  - manage network namespaces.
```
ip  netns  list：列出所有的netns
ip  netns  add  NAME：创建指定的netns
ip  netns  del  NAME：删除指定的netns
ip  netns   exec  NAME  COMMAND：在指定的netns中运行命令
```
#### ip address - protocol address management.
```
ip address add - add new protocol address
ip  addr  add  IFADDR  dev  IFACE
[label NAME]：为额外添加的地址指明接口别名；
[broadcast ADDRESS]：广播地址；会根据IP和NETMASK自动计算得到；
[scope SCOPE_VALUE]：
global：全局可用；
link：接口可用；
host：仅本机可用；												

ip address delete - delete protocol address
ip addr  delete  IFADDR  dev  IFACE 

ip address show - look at protocol addresses
ip  addr   list  [IFACE]：显示接口的地址；

ip address flush - flush protocol addresses
ip  addr  flush  dev  IFACE
```
#### ip route - routing table management
```
ip route add - add new route
ip route change - change route
ip route replace - change or add new one
ip  route   add  TYPE PREFIX  via GW  [dev  IFACE]  [src SOURCE_IP]

示例：
# ip route add 192.168.0.0/24  via 10.0.0.1  dev eth1 src  10.0.20.100
# ip  route  add default  via  GW						

ip route delete - delete route
ip  route  del  TYPE PRIFIX 

示例：
# ip  route delete  192.168.1.0/24

ip route show - list routes
TYPE PRIFIX  
ip route flush - flush routing tables
TYPE  PRIFIX

ip route get - get a single route
ip  route  get  TYPE PRIFIX

示例：ip route  get  192.168.0.0/24
```
### ss命令：
用法
```
ss  [options]  [ FILTER ]
```

选项：
```
-t：TCP协议的相关连接
-u：UDP相关的连接
-w：raw socket相关的连接
-l：监听状态的连接
-a：所有状态的连接
-n：数字格式
-p：相关的程序及其PID
-e：扩展格式信息
-m：内存用量
-o：计时器信息
```
FILTER := [ state TCP-STATE ]  [ EXPRESSION ]

#### TCP的常见状态：
- LISTEN：监听
- ESTABLISEHD：建立的连接
- FIN_WAIT_1：
- FIN_WAIT_2：
- SYN_SENT：
- SYN_RECV：
- CLOSED：

#### EXPRESSION：
```
dport = 
sport = 
示例：'( dport = :22 or sport = :22)'
~]# ss   -tan    '(  dport = :22 or sport = :22  )'
~]# ss  -tan  state  ESTABLISHED
```
## 配置文件：
IP/NETMASK/GW/DNS等属性的配置文件：/etc/sysconfig/network-scripts/ifcfg-IFACE  
路由的相关配置文件：/etc/sysconfig/networkj-scripts/route-IFACE  
配置文件/etc/sysconfig/network-scripts/ifcfg-IFACE通过大量参数来定义接口的属性；其可通过vim等文本编辑器直接修改，也可以使用专用的命令的进行修改（CentOS6：system-config-network (setup)，CentOS 7: nmtui）
```
ifcfg-IFACE配置文件参数：
DEVICE：此配置文件对应的设备的名称；
ONBOOT：在系统引导过程中，是否激活此接口；
UUID：此设备的惟一标识；
IPV6INIT：是否初始化IPv6；
BOOTPROTO：激活此接口时使用什么协议来配置接口属性，常用的有dhcp、bootp、static、none；
TYPE：接口类型，常见的有Ethernet, Bridge；
DNS1：第一DNS服务器指向；
DNS2：备用DNS服务器指向；
DOMAIN：DNS搜索域；
IPADDR： IP地址；
NETMASK：子网掩码；CentOS 7支持使用PREFIX以长度方式指明子网掩码；
GATEWAY：默认网关；
USERCTL：是否允许普通用户控制此设备；
PEERDNS：如果BOOTPROTO的值为“dhcp”，是否允许dhcp server分配的dns服务器指向覆盖本地手动指定的DNS服务器指向；默认为允许；
HWADDR：设备的MAC地址；

NM_CONTROLLED：是否使用NetworkManager服务来控制接口；
```

### 管理网络服务：
CentOS 6:  service  SERVICE  {start|stop|restart|status}
CentOS 7：systemctl  {start|stop|restart|status}  SERVICE[.service]

### 配置文件修改之后，如果要生效，需要重启网络服务；
CentOS 6：# service  network  restart
CentOS 7：# systemctl  restart  network.service

### 路由配置文件	
用到非默认网关路由：/etc/sysconfig/network-scripts/route-IFACE 
支持两种配置方式，但不可混用；
1. 每行一个路由条目：  
```
TARGET  via  GW
```

2. 每三行一个路由条目：
```
ADDRESS#=TARGET
NETMASK#=MASK
GATEWAY#=NEXTHOP
```
