title: mac抓包工具--Charles
date: 2016-08-30 22:18:44
tags: Mac
---

上篇文章介绍了 Mac 中抓局域网数据包的使用方法，主要是基于 ARP 欺骗。这次将要介绍的 Charles 的原理是`设置中间代理`来完成抓包的工作。

<!--more-->

# Charles 的安装与破解（都是程序员，女人何必为难女人）

 - 下载原版 Charles 路径：[我的七牛云存储](http://7xivx9.com1.z0.glb.clouddn.com/charles-proxy-3.10.2.dmg) or [官网](http://www.charlesproxy.com/download/)，并正常安装（此版本为 3.10.2，目前为官网最新）
 - 下载破解 jar 包：[我的七牛云存储](http://7xivx9.com1.z0.glb.clouddn.com/charles.jar)
 - 显示 Charles 包的内容
 	![image](http://7xivx9.com1.z0.glb.clouddn.com/charles-cracker-1.png)
 - 替换 `charles.jar`
 	![image](http://7xivx9.com1.z0.glb.clouddn.com/charles-cracker-2.png)
 - 完成，打开即可使用

# 使用 Charles 抓取网络请求

参考文章 : [使用Charles抓取App网络请求](http://www.brighttj.com/ios/ios-use-charles-fetch-web-request-in-app.html)