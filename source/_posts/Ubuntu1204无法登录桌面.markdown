---
layout: post
title: "Ubuntu12.04 管理员无法登录桌面，只能guest登录"
date: 2015-05-03 15:25:33 +0800
comments: true
tags: Linux
---

今天使用Ubuntu的时候遇到了一个问题，在登录界面输入正确的管理员密码后却无法登录到桌面，一直在那里循环，访客模式就可以登录进桌面，网上查了一下，找到了解决办法。

<!--more-->

解决办法步骤如下：

- 在登录界面按下  `Ctrl + Alt + F1` 切换到tty， 输入管理员账号和密码
- 输入 `sudo rm -r .Xauthority*`
- 输入 `sudo reboot`

OK，解决~

截图如下：

![image](http://7xivx9.com1.z0.glb.clouddn.com/Ubuntu1204.png)