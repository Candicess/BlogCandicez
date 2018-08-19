title: 必备Linux常用命令概要
date: 2017-03-12 15:04:44 
tags: Linux
----

记录下常见必备的Linux命令，方便日后回顾。

<!--more-->

## 两个很有用的网站

[软件测试人员必备Linux命令](http://www.cnblogs.com/Javame/p/3968343.html)

[Linux命令速查](http://man.linuxde.net/)

## CheckList

- cat 查看文件内容
- chmod 修改文件权限
- chown 将文件的所有者修改为另一个用户
- find 查找文件名
- file 查看文件的内容类型
- mkdir 创建目录
- more、less 分页查看（空格下一页 B上一页）
- head、tail 查看文件头部、尾部  默认10行
- mv、cp  移动 拷贝
- rm(`rm -rf folder_name`) 移除
- which(显示一个命令的真实地址，如 which git)
- scp(`scp sedstudy.txt root@47.94.89.226:~/sedstudycopy.txt`) 在两台主机之间进行文件的拷贝
- awk
- grep 过滤
- sed
- look
- wc
- cd
- pwd(显示当前的绝对路径)
- mount umount(挂载和卸载设备)
- ls (ls -lsa     ll) 查看文件
- telnet(查看47.x主机的80端口是否开放 `telnet 47.94.89.226 80`)
- ifconfig 查看网络设置
- ping 
- netstat 	
- kill(kill pid)
- ps(最常用的是ps -aux，可以再结合grep，如查找ssh的进程pid号-> `ps -aux | grep ssh`)
- free(-b:B -k:KB -m:MB -t:Total) 查看内存的使用情况
- top(需要查一下各个字段的含义)
- shutdown(现在关机:`shutdown -h now`  现在重启:`shutdown -r now`)
- sudo 以超级管理员的身份执行命令
- who(显示目前登录系统的用户信息)
- whoami(显示当前有效的用户名称)
- su 切换用户
- clear
- crontab(定时/定期执行命令或脚本)
- tar 压缩 解压
- source(链接shell) 配置文件生效（服务器配Java环境）
- \>覆盖 \>\>追加
- man	查看命令帮助
- curl 访问url或下载文件(访问并过滤->`curl http://zhouwut.cn | grep better`   下载文件->`curl -o filename.html http://zhouwut.cn/index.html`  -o是output)
- wget 下载文件(`wget http://zhouwut.cn/index.html`)


## 备忘

### sed命令

TODO
AND

#### 增
第2行下增加sth
sed '2a sth' 

2-5行每行的下一行增加sth
sed '2,5a sth' 

2-最后一行每行的下一行增加sth
sed '2,$a sth' 

2行前增加sth
sed '2i sth'

#### 删
删除第2-5行
sed '2,5d'

#### 替换 c->取代
2-5行的内容为一行的sth
sed '2,5c sth'

#### 查
只显示2-5行的内容(Todo：加不加-n会有区别，不知道为什么)
sed -n '2,5p'

搜寻有sth关键字的行
sed -n '/sth/p'

只显示不包含sth的行
sed '/sth/d'

打印第一个找到的first行到第4行的内容
sed -n '/first/, 4p'

#### 字符串替换
sed 's/origin/after/g'

删除包含delme和delmine
sed 's/delme//g' | sed 's/delmine//g'

直接对源文件修改
sed -i 's/first/second/g' filename

### awk

awk [-F  field-separator]  'commands'

awk依次会读入每一行，并以每一行为单位进行操作

显示以:分割的每行的第一个元素
cat /etc/passwd | awk -F ':' '{print $1}'

显示以:分割的每行的第一个、和第七个元素，输出时中间加个逗号
cat /etc/passwd | awk -F ':' '{print $1 "," $7}'

同上，但是在开头和结尾添加自定义内容
cat /etc/passwd | awk -F ':' 'BEGIN {print "name, shell"} {print $1 "," $7} END {print "I am the end."}'

搜索/etc/passwd中以root关键字开头的所有行，并显示对应的第一个和第七个元素(/pattern/，其中pattern支持正则)
cat /etc/passwd | awk -F ':' '/^root/{print $1, $7}'


### wc - word count

基本命令
-c chars bytes 统计字节数
-l lines 统计行数
-m 统计字符数。这个标志不能与 -c 标志一起使用，结果一般与-c相同
-w words 统计字数。一个字被定义为由空白、跳格或换行字符分隔的字符串
-L 打印最长行的长度

wc 命令 文件名

显示行数
wc -l /etc/passwd

显示单词数
wc -w /etc/passwd

显示字节数(字符数)
wc -c /etc/passwd

显示行数+单词数+字节数
wc /etc/passwd


### look

查询每行以某个字符串开头的所有行
look root /etc/passwd


### netstat

-a (all)显示所有选项，默认不显示LISTEN相关
-t (tcp)仅显示tcp相关选项
-u (udp)仅显示udp相关选项
-n 拒绝显示别名，能显示数字的全部转化成数字。
-l 仅列出有在 Listen (监听) 的服務状态

-p 显示建立相关链接的程序名
-r 显示路由信息，路由表
-e 显示扩展信息，例如uid等
-s 按各个协议进行统计
-c 每隔一个固定时间，执行该netstat命令。

以上参数都可组合使用，如 netstat -atul

列出所有的tcp端口
netstat -at

列出所有正在监听的udp端口
netstat -alu

显示ssh进程占用的端口
netstat -ap | grep ssh