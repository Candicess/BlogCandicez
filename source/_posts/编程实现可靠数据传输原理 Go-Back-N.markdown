---
layout: post
title: "编程实现可靠数据传输原理Go-Back-N"
date: 2014-11-29 15:20:33 +0800
comments: true
tags: [Java, GBN]
---

有一个计算机网络的实验，需要模拟GBN的过程，即在本地条件下模拟丢包重传机制。刚看到这个实验时有些不知所措，因为不知道怎样能实现丢包，在本地编程，即使是使用不可靠数据传输UDP的DatagramSocket，由于localhost到localhost走的是回环链路，也就是从客户机的应用层--传输层--网络层 到 服务器的网络层--传输层--应用层，这个肯定无法丢包，也用实验进行验证了（比如连续发送1万个DatagramPacket，发现每一个都被收到了）。

那么问题来了，怎样才能丢包？

<!--more-->

通过前面的叙述，可知在链路上丢包是不可能的了，那我们可以从服务器端下手，也就是说，即使服务器收到了某个数据包，也不给客户机发ACK，那么到了客户机设置的定时器后，客户机自然就认为是丢包了。所以可以给服务器设置一个丢包概率，用Math.random()函数来实现，该函数返回一个从0到1之间的随机数，可用来设置“丢包”概率。如设置丢包概率为0.3：

```
if (Math.random()<=0.7){ 
    //接收成功，发ack; 
}else{
    //呵呵，什么都不做
}
```

“丢包”问题解决了，下面就是怎么发送数据包，用之前学过的TCP的Socket也行，用UDP的DatagramSocket也行，这个实验就采用UDP编程吧，所以，下一个问题就是，如何用UDP编程。

同样也需要Socket（不理解Socket是什么的可以看前几篇文章），不过这次的是DatagramSocket，代表是UDP的数据报套接字。DatagramSocket要往外发送数据包，也就是DatagramPacket。举一个客户机可以向服务器发送数据并在服务器显示的例子。

客户端代码如下：

```
public class UDPClient { 
    public static void main(String[] args) throws Exception { 
        //创建UDP的socket 
        DatagramSocket socket = new DatagramSocket(); 
        //通过域名来获得服务器的IP地址 
        InetAddress serverAddress = InetAddress.getByName("localhost"); 
        String str = "Hello Server, I am client"; 
        //需要将发送的字符串转换成字节数组，才能在互联网中传输 
        byte[] data = str.getBytes(); 
        //新建数据包，指明发送数据内容，发送的长度，服务器地址，服务器端口号 
        DatagramPacket packet = new DatagramPacket(data, data.length, 
                serverAddress, 8899); 
        //使用socket发送数据包 
        socket.send(packet); 
    } 
}
```

对应服务器端代码如下：

```
public class UDPServer { 
    public static void main(String[] args) throws Exception { 
        //新建服务器端socket，并同时设置监听端口 
        DatagramSocket socket = new DatagramSocket(8899); 
        while (true){ 
            byte[] data = new byte[1024]; 
            //新建数据包，会把后面收到的内容放到data字节数组里，最大长度为data.length 
            DatagramPacket packet = new DatagramPacket(data, data.length); 
            //此方法为阻塞方法（block method）,直到监听到数据包后才会往下执行，不然就一直等待，就像ServerSocket.accept()方法一样 
            socket.receive(packet); 
            //执行到这里说明监听到了数据包，并把其中内容转换为字符串 
            String result = new String(packet.getData()); 
            //打印 
            System.out.println("receive result : " + result); 
        } 
    } 
}
```

务必先运行服务器端，再运行客户端。运行结果如下：

![image](http://img.blog.csdn.net/20141129165114704?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWNvZGV5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

若想服务器端给客户端发送数据，可同样操作。

利用DatagramSocket发送DatagramPacket的问题解决了，那么下一个问题，在GBN协议里面，客户端发送一个数据之后要设置定时器的，这个定时器怎么设置？

在java里有两种方式来设置定时器，首先是第一种方法，使用Timer结合ActionListener即可。

代码如下，其中值得注意的一点是Timer要导入java.swing这个包中的，而不是java.util这个包中的，否则不能正确运行。

```
import java.awt.event.ActionEvent; 
import java.awt.event.ActionListener;
import javax.swing.Timer;
public class TimerMain { 
    public static void main(String[] args) { 
        //设置定时器，第一个参数为延迟执行时间，单位是毫秒；第二个参数为到时间后执行的动作，类型为ActionListener 
        Timer timer = new Timer(3000, new DelayActionListener()); 
        //这个必须写，才可以使得timer生效 
        timer.start(); 
        //为了不让程序结束，加个死循环，不然timer定时器还没到，主程序就结束了 
        while (true){ 
        } 
    } 
}
//自定义动作类实现ActionListener接口 
class DelayActionListener implements ActionListener{ 
    //此为回调方法，当时间到后自动执行actionPerformed方法 
    @Override 
    public void actionPerformed(ActionEvent e) { 
        System.out.println("定时器时间到"); 
    } 
}
```

运行结果如下：每隔3秒执行一次。如果需要取消定时器，使用timer.stop()方法即可。

![image](http://img.blog.csdn.net/20141129165207500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWNvZGV5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

设置定时器的第二种方法是使用Timer结合TimerTask，在android里很常用，其实本来就是java的东西，代码如下：

```
import java.util.Timer; 
import java.util.TimerTask;
public class TimerTaskMain { 
    public static void main(String[] args) { 
        //使用TimerTask 
        TimerTask timerTask = new TimerTask() { 
            @Override 
            public void run() { 
                System.out.println("timerTask定时器时间到"); 
            } 
        }; 
        Timer timer = new Timer(); 
        //第一个参数为要执行的目标，第二个参数为第一次执行延迟的时间，第三个参数为循环执行的时间间隔，单位都是毫秒 
        timer.schedule(timerTask, 3000, 3000); 
    } 
}
```

运行结果如下：每隔3秒执行一次。如果需要取消定时器，使用timerTask.cancel()方法即可。

![image](http://img.blog.csdn.net/20141129165146918?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWNvZGV5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

有了如上这些东西，再结合GBN的原理和自己控制的逻辑操作，写GBN就应该可以实现了，就不贴完整代码了，自己实现印象更深刻些。