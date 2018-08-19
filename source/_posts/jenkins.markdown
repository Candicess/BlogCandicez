title: 搭建Jenkins环境构建Maven
date: 2017-06-30 15:04:44 
tags: Jenkins
----

讲解如何使用Jenkins做持续集成。

<!--more-->

## Jenkins的部署
在其官网上下载 Jenkins.war 包放在服务器的 Tomcat 容器中即可，直接访问服务器地址，进入Jenkins后设置登录的用户信息。

## Jenkins的配置

### 插件
(1)在进行配置之前，由于使用的是 Git 拉去代码的方式，所以要首先安装插件。
![Git](http://img.blog.csdn.net/20151218194433833)

(2)因为要部署到远端的服务器，所以还需要安装"Deploy to container Plugin"插件，如图
![Deploy to container Plugin](http://img.blog.csdn.net/20151218193834804)

安装好之后重启即可

## 新建Job

创建一个新任务，构建一个自由风格的软件项目，源码管理选择Git，并配置好仓库的信息，构建的触发器为当BitBucket有新的代码提交时，如下图所示
![Jenkins1](http://7xivx9.com1.z0.glb.clouddn.com/jenkins1.png)

具体构建时，保存本次构建的 COMMIT_ID 和 TASK_ID 方便查看本次构建结果，如下图
![Jenkins2](http://7xivx9.com1.z0.glb.clouddn.com/jenkins2.png)

构建之后就要部署，使用"Deploy to container Plugin"插件，写好相关的信息即可，如下图
![Jenkins3](http://7xivx9.com1.z0.glb.clouddn.com/jenkins3.png)

## 参考文章

[自动构建](https://wiki.jenkins.io/display/JENKINS/BitBucket+Plugin)