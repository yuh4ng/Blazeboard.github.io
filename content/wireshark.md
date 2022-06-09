+++
title = "Whireshark"
date = 2020-07-28

[taxonomies]
categories = ["安全"]
+++

Whireshark安装与使用



抓包过滤器语法：

Type：host net port

方向：src dst

协议：ether ip tcp udp http ftp 等

逻辑运算符：与 &&    或||    非！
<!-- more -->



显示过滤器语法： 

逻辑操作符：and or not xor

比较操作符：==     ！    =

ip地址：ip.addr  ip.src  ip.dst

端口过滤：tcp.port    tcp.srcport    tcp.dstport    udp.port    tcp.flag.syn 

协议过滤：arp   ip   icmp   udp   tcp   bootp(dhcp)   dns



高级功能：

1. 数据流追踪：将TCP UDP ssl等数据进行重组并完整的呈现出来

   菜单栏-分析-追踪TCP流

2. 协议分层统计：通信流中不同协议占用的百分比

   菜单栏-统计-协议分层

3. 网络会话统计：统计会话之间接收和发送的数据包和字节数

   统计-会话

4. 网络节点统计：统计会话中每个节点接收和发送的数据包和字节数

   菜单栏-统计-终端节点

5. IO图表统计：对网络中的吞吐流量进行实时的图形统计

   菜单栏-统计-IO图表

6. 数据流图分析：将会话通信通过图形可视化显示出来

   菜单栏-统计-流量图表

7. 数据包的长度统计：统计数据流中包长度的分布

   菜单栏-统计-包长度

