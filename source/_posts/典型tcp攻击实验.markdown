---
layout: post
title: "【视频教程】典型 TCP 攻击实验"
date: 2015-06-10 15:25:33 +0800
comments: true
tags: [TCP, netwox]
---

记录典型 TCP 攻击实验的原理及操作步骤。

<!--more-->

在本次实验中，启用了三个虚拟机，处于同一子网中。同时使用了 Wireshark 来进行网络抓包分析，使用 netwox 工具箱来进行攻击实验。

1.ARP缓存中毒（ARP cache poisoning）

ARP 缓存是 ARP 协议的重要组成部分。ARP 协议运行的目标就是建立 MAC 地址和 IP 地址的映射，然后把这一映射关系保存在 ARP 缓存中，使得不必重复运行 ARP 协议。因为 ARP 缓存中的映射表并不是一直不变的，主机会定期发送 ARP 请求来更新它的 ARP 映射表，利用这个机制，攻击者可以伪造 ARP 应答帧使得主机错误的更新自己的 ARP 映射表，这个过程就是 ARP 缓存中毒。

这样的后果即使要么使主机发送 MAC 帧到错误的 MAC 地址，导致数据被窃听；要么由于 MAC 地址不存在，导致数据发送不成功。

关键 netwox 命令:

`netwox 80 --eth "00:00:00:00:00:00" --ip "192.168.224.134"` 表示 发送 ARP 的响应包，Wireshark 抓包如下：

![image](http://7xivx9.com1.z0.glb.clouddn.com/tcp_01arp.png)

具体过程演示：

<div class="video-container">
	<video src="http://7xivx9.com1.z0.glb.clouddn.com/tcp_01ARP_cache_poisoning.mp4" controls="controls"></video>
</div>

2.SYN 泛洪攻击

SYN 攻击是一种 DoS（Denial of Service）攻击，在这种攻击中黑客向被攻击者的 TCP 端口发送很多 SYN 请求，但是黑客并不是想完成三次握手协议，而是使用伪造的 IP 地址或者只进行三次握手协议中的第一次握手。因为 SYN 数据包用来打开一个 TCP 链接，所以受害者的机器会向伪造的地址发送一个 SYN/ACK 数据包作为回应，并等待预期的 ACK 响应。每个处于等待状态，半开的链接队列都讲进入空间有限的待处理队列。由于伪造的源地址实际上并不存在，所以将那些等待队列中的记录删除并完成建立 TCP 连接所需的 ACK 响应用于不会到来，相反每个半开的连接一定会超时，这将花费一段比较长的时间。

只要攻击者使用伪造的 SYN 数据包继续泛洪受害者的系统，受害者的待处理队列将一直处于满员，这使得真正的 SYN 数据包几乎不可能到达系统并打开有效的 TCP 连接。

关键 netwox 命令:

`netwox 76 -i "192.168.224.134" -p "23"` 代表向目标主机的23号端口发送大量 TCP 连接请求，目标主机连接请求如图：

![image](http://7xivx9.com1.z0.glb.clouddn.com/tcp_03synflooding.png)

具体过程演示：

<div class="video-container">
	<video src="http://7xivx9.com1.z0.glb.clouddn.com/tcp_03SYN_Flooding_Attack.mp4" controls="controls"></video>
</div>

3.对 Telnet 的 TCP RST 攻击

TCP RST 攻击可以终止两个被攻击主机之间的 TCP 连接。

A和服务器B之间建立了 TCP 连接，此时C伪造了一个 TCP 包发给B，使B异常的断开了与A之间的 TCP 连接，就是 RST 攻击了。实际上从RST 标志位的功能已经可以看出这种攻击如何达到效果了。

关键 netwox 命令:

`netwox 78 -i "192.168.224.138"` 代表向目标主机发送 TCP RST 数据包，使其中断 telnet 连接：

![image](http://7xivx9.com1.z0.glb.clouddn.com/tcp_04tcprst.png)

具体过程演示：

<div class="video-container">
	<video src="http://7xivx9.com1.z0.glb.clouddn.com/tcp_04TCP_RST.mp4" controls="controls"></video>
</div>

最后，感谢 静姐 提供 [知道创宇研发技能表](http://blog.knownsec.com/Knownsec_RD_Checklist/v2.2.html)！