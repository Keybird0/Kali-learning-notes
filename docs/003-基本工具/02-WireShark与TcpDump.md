# WireShark 与 TcpDump





## 0x01 WIRESHARK

  抓包嗅探协议分析
  安全专家必备技能
  抓包引擎

```
Libpcap9-----Linux
Winpcap10-----Windows
```

 强大的解码能力

WIRESHARK-----基本使用方法

1.   启动

2.   选择抓包网卡

3.   混杂模式 (√ use promiscuous mode all interfaces)

4.   实时抓包

5.   保存和分析捕获文件

6.   首选项

   ​

wrieshark/tmpdump/...-pcap   //兼容性最好的包保存格式

WIRESHARK-----筛选器
 功能:  过滤掉干扰的数据包

- 抓包筛选器

- 显示筛选器

  ​

处理流程

```
      Display Filters
            ↑
      Capture Engine
            ↑
      Capture Filters
            ↑
         Neterwork
```

筛选举例:`(udp) && !(ip.addr==192.468.1.118)`  //筛选出udp协议中含特定ip的包



WIRESHARK-常见协议包可以显示出分层结构

- arp
- icmp
- tcp
- udp
- dns
- http
- ftp

例:arp

```
 第一层包的相关信息。多少帧，大小，传输时间等统计信息
 第二层数据包的内容字段。首先是目标地址，源地址。上层协议类型。  占位地段
 第三arp包头。 硬件地址类型1表示以太网    协议类型ip解析成arp地址  硬件地址长度   ip地址4字节32位   操作代码  arp包头内容包含发送端的mac和ip目标端的mac和ip
```


例：ssdp

```
 第一层汇总信息
 第二层二层包头 目标地址，源地址   上一层协议是ip协议
 第三层。ip版本号  头长度  dsf  total长度（ip头到数据字段）   identification:0*7b52(31570)=大文件传输时会分割成小块，这是标记号段接收端
 根据号顺序重组    ragment offset:0 =偏移量    time to live:1 =生存时间  protocol:UDP (17)=上层四层协议（共1-255种协议)
 header chechsum:0*8cf3[correct]=ip头的校验值，数据包被修改后校验值为错误的
 第四层 user Datagram protocol,src port:56253(56253),Dst port:ssdp(1900) =源端口目标端口
```


</br>
</br>
>  tcp包三次会话以syn标号1为开始连接。目标返回ack值并发syn值。 我再次发送ack值确认。然后开始发送信息,每发送包都要确认

>  dns三层都走ip四层是基于udp协议

>  http三层ip四层tcp  repuest method:get=请求方法get  request url:http://dict.cn/ws.php=请求地址    request version:http:/1.1=请求版本1.1  user-agent:mozilla/4.0(compatible:msie 5.00;windows 98)\r\n=客户端信息 http如果以非80端口传输则认不出，不能解析。数据包右键-Decode As-Transport-选择http并应用就可以了

</br>
</br>
wireshark**通过端口区分协议**

数据流
http smtp pop3 ssl
http smtp pop3 大部分以明文传送容易被查看    ssl四层同样基于tcp应用变成了ssl
许多数据包为了访问一个页面产生。为了解决一个一个查看数据包而改用数据流  `右键--Follow TCP Stream`



wireshark---信息统计实践

wireshark官网有供学习的例子文件


统计功能位于`statistics菜单`下
  + summary数据包的摘要信息
  + endpoints查看数据包列表里一共有多少ip地址通信  ethernet 2 二层包头  可以排序可看哪个ip大量发送接收包大量小包可以造成网络性能弱化
  + protocol hierarchy查看什么类型的协议包占比
  + packet lengths  按包的长度查看流量
  + conversations 查看通讯 的机器之间流量
  + 码类型按端口区分协议

`Analyze菜单`下expert info专家信息可以给出当前网络出现的信息


`实践`

```
抓包对比nc ncat加密与不加密的流量
WireShark样例包下载地址：
https://wiki.wireshark.org/SampleCaptures
```

<br>
**一些使用事项**  
+ dhclinent eth0   强制重新获取dhcp ip地址  
+ wireshark对大流量数据分析有所欠缺  
+ 企业抓包布置方案 sniffer     cace/riverbed---基于wireshark开发  
+ 已有自动抓包分析告警的商业软件





## 0x02 TcpDump

简介:

>  No-GUI的抓包分析工具
>
>  Linux、Unix系统默认安装



- 抓包


  默认只抓68个字节

  `tcpdump -i eth0 -s 0 -w file.pcap`

  `tcpdump -i eth0 port 22`

- 读取抓包文件

  `Tcpdump -r file.pcap`

#### 选项介绍:

> -A 以ASCII格式打印出所有分组，并将链路层的头最小化。  
> -c 在收到指定的数量的分组后，tcpdump就会停止。  
> -C 在将一个原始分组写入文件之前，检查文件当前的大小是否超过了参数file_size 中指定的大小。如果超过了指定大小，则关闭当前文件，然后在打开一个新的文件。参数 file_size 的单位是兆字节（是1,000,000字节，而不是1,048,576字节）。  
> -d 将匹配信息包的代码以人们能够理解的汇编格式给出。  
> -dd 将匹配信息包的代码以c语言程序段的格式给出。  
> -ddd 将匹配信息包的代码以十进制的形式给出。  
> -D 打印出系统中所有可以用tcpdump截包的网络接口。  
> -e 在输出行打印出数据链路层的头部信息。
> -E 用spi@ipaddr algo:secret解密那些以addr作为地址，并且包含了安全参数索引值spi的IPsec ESP分组。
> -f 将外部的Internet地址以数字的形式打印出来。
> -F 从指定的文件中读取表达式，忽略命令行中给出的表达式。  
> -i 指定监听的网络接口。  
> -l 使标准输出变为缓冲行形式，可以把数据导出到文件。  
> -L 列出网络接口的已知数据链路。  
> -m 从文件module中导入SMI MIB模块定义。该参数可以被使用多次，以导入多个MIB模块。
> -M 如果tcp报文中存在TCP-MD5选项，则需要用secret作为共享的验证码用于验证TCP-MD5选选项摘要（详情可参考RFC 2385）。
> -b 在数据-链路层上选择协议，包括ip、arp、rarp、ipx都是这一层的。
> -n 不把网络地址转换成名字。
> -nn 不进行端口名称的转换。
> -N 不输出主机名中的域名部分。例如，‘nic.ddn.mil‘只输出’nic‘。
> -t 在输出的每一行不打印时间戳。
> -O 不运行分组分组匹配（packet-matching）代码优化程序。  
> -P 不将网络接口设置成混杂模式。  
> -q 快速输出。只输出较少的协议信息。  
> -r 从指定的文件中读取包(这些包一般通过-w选项产生)。  
> -S 将tcp的序列号以绝对值形式输出，而不是相对值。  
> -s 从每个分组中读取最开始的snaplen个字节，而不是默认的68个字节。  
> -T 将监听到的包直接解释为指定的类型的报文，常见的类型有rpc远程过程调用）和snmp（简单网络管理协议；）。  
> -t 不在每一行中输出时间戳。  
> -tt 在每一行中输出非格式化的时间戳。  
> -ttt 输出本行和前面一行之间的时间差。  
> -tttt 在每一行中输出由date处理的默认格式的时间戳。  
> -u 输出未解码的NFS句柄。  
> -v 输出一个稍微详细的信息，例如在ip包中可以包括ttl和服务类型的信息。  
> -vv 输出详细的报文信息。  
> -w 直接将分组写入文件中，而不是不分析并打印出来。  
>


</br>
使用帮助
```
    root:～# tcpdump -h
    tcpdump version 4.3.0
    libpcap version 1.3.0
    Usage: tcpdump [-aAbdDefhHIJKlLnNOpqRStuUvxX][ -B size ] [ -c count ]
    [ -C file_size ] [ -E algo:secret ] [ -F file ] [ -G seconds]
    [ -i interface ] [ -j tstamptype ] [ -M secret ]
    [ -r file ] [ -s snaplen ] [ -T type ] [ -w file ]
    [ -W filecount ] [ -y datalinktype] [ -z command ]
    [ -Z user ] [ expression ]
```

- 抓包

```
root:～# tcpdump -i eth0 -s 0 -w a.cap     //监听网络接口字节为0保存到a.cap文件中
tcpdump: listening on eth0, link-type EN10MB(Ethernet), capture size 65535 bytes
^C15 packets captured
15 packets receiced by filter
0 packets drodded by kernel

root:～# tcpdump -r a.cap                  //读取a.cap文件内容

root:～# tcpdump -A -r a.cap               //以ASCII格式打印出所有的分组并且读取此文件

root:～# tcpdump -X -r a.cap               //以十六进制格式打印出所有的分组并且读取此文件

root:～# tcpdump -i eth0 tcp port 22
```

- 读包


```
  tcpdump -n -r http.cap | awk '{print $3}'| sort -u
  tcpdump -n src host 145.254.160.237 -r http.cap
  tcpdump -n dst host 145.254.160.237 -r http.cap
  tcpdump -n port 53 -r http.cap
  tcpdump -nX port 80 -r http.cap

root:～/Downloads# tcpdump -n -r http.cap | awk '{print $3}'| sort -u
reading from file http.cap, link type En10MB(Ethernet)
145.253.2.203.53
145.254.160.237.3009
145.254.160.237.3371
145.254.160.237.3372
216.239.59.99.80
65.208.228.223.80

0                  1                    2                   3

0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1

+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

|          Source Port          |     Destination Port          |

+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

|                        Sequence Number                        |

+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

|                      Acknowledgment Nuber                     |

+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

|  Date  |      |C|E|U|A|P|R|A|F|                               |

| Offset |  Res.|W|C|R|C|S|S|Y|I|         Windwos               |

|        |      |R|E|G|K|H|T|N|N|                               |

+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

|            Checksum           |     Urgent Pointer            |

+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

|                      Options               |     Padding      |

+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

|                                data                           |

+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+


TCPDUMP------高级筛选

  tcpdump -A -n 'tcp[13]=24' -r http.cap

说明：
  00011000b = 24 in decimal

```
