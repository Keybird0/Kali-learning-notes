# Scapy
## 0x01 简介
`Scapy`是一个可以让用户发送、侦听和解析并伪装网络报文的Python程序。这些功能可以用于制作侦测、扫描和攻击网络的工具。

换言之，`Scapy` 是一个强大的操纵报文的交互程序。它可以伪造或者解析多种协议的报文，还具有发送、捕获、匹配请求和响应这些报文以及更多的功能。`Scapy` 可以轻松地做到像扫描(scanning)、路由跟踪(tracerouting)、探测(probing)、单元测试(unit tests)、攻击(attacks)和发现网络(network discorvery)这样的传统任务。它可以代替hping,arpspoof,arp-sk,arping,p0f 甚至是部分的Namp,tcpdump和tshark 的功能。  
它即可以作为一个单独的工具使用,也可以作为python的第三方库,导入python中再进一步编程.

## 0x02 学习链接
https://www.gitbook.com/book/wizardforcel/scapy-docs/details (中文指南)
http://scapy.readthedocs.io/en/latest/ (官方英文指导)

附:自己写的ping探测
ping.py

```python
#!/usr/bin/env python
# coding: utf-8

import getopt
import time
from scapy.all import *


# define some global variables
target = " "
port = 80
method = "icmp"
count = 3
t = 0
ans = []
unans = []




# ARP ping
def arping1():
    ans, unans = srp(Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst=target), timeout=1)
    
    for snd, rcv in ans:
        print ls(rcv)
    print type(ans)
    print ans.summary(lambda(s, r): r.sprintf("%Ether.src% %ARP.psrc%"))

def arping2():
    global ans
    global unans
    for i in range(count):
        ans, unans = arping(target)
        for snd, rcv in ans:
            s = "Reply from " + rcv.psrc + "(" + rcv.hwsrc + ")"
            print s
    

# ICMP ping
def icmping():
    global target
    global ans
    global unans
    global t

    ts = time.time()
    ans, unans = sr(IP(dst=target)/ICMP(seq=(1,count), type=13), inter=0.3, retry=3, timeout=1)        
        
    
    for snd, rcv in ans:
        t =  (rcv.ts_ori - rcv.ts_rx)
        s = str(rcv.len) + "   bytes from  " + snd.dst + " : icmp_seq=" + str(snd.seq) + "   ttl=" + str(rcv.ttl) + "   time=" + str(t/10000.0) + " ms"
        print s 
        # ls(rcv)
    
    
# TCP ping , use SYN type
def tcping():
    global ans
    global unans
    ans, unans = sr(IP(dst=target)/TCP(seq=(1,count),flags="S"))
    
    
# UDP ping 
# If all else fails there is always UDP Ping which will produce ICMP Port unreachable errors
# from live hosts. Here you can pick any port which is most likely to be closed,
# such as port 0:
def udping():
    global ans
    global unans    
    ans, unans = srloop(IP(dst=target)/UDP(dport=0), count = count)
    
    # Any response to our probes will indicate a live host. We can collect results with the following command:
    ans.summary(lambda(s, r): r.sprintf("%IP.src% is alive"))

# Define personal format ping data.   
def define():
    global ans
    global unans
    
    ether = Ether(dst='ff:ff:ff:ff:ff:ff', src = 'b4:6d:83:17:cc:c4')  # 
    ip = IP(version = 4, dst=target, id = 8, ttl = 64)
    # Many firewalls include a rule to drop TCP packets that do not have TCP Timestamp option set which is a common occurrence in popular port scanners.
    tcp = TCP(sport = 20, dport = 80, seq=(1, count), flags = 'S', options=[('Timestamp', (0,0))])  
    
    ans, unans = sr(ether/ip/tcp, timeout = 1)
    


# print usage 
def usage():
    print "a simple ping program."
    print
    print "Usage: ping.py -t target_host -p port [options]"
    print 
    print "[options]:"
    print "-i       use icmp for ping(default)"
    print "-a       use arp for ping, just can use in Lan"
    print "-s       use syn for ping"
    print "-u       use udp for ping"
    print "-d       use personal define format for ping"
    print "Examples: "
    print "ping.py -t 127.0.0.1 -p 80               test ping localhost on port 80"
    print "ping.py -t example.com -u                test ping example.com use udp"
    sys.exit(0)


# print results 
def result():
    for snd, rcv in ans:
        s = str(rcv.len) + "   bytes from  " + snd.dst + " : icmp_seq=" + str(snd.seq) + "   ttl=" + str(rcv.ttl) 
        print s 



def main():
    global target
    global port
    global method
    global count
    
    try:
        if not len(sys.argv[1:]):
            usage()
        if len(sys.argv[1:]) == 1:
            tar = sys.argv[1]
            compile_ip=re.compile('^((25[0-5]|2[0-4]\d|[01]?\d\d?)\.){3}(25[0-5]|2[0-4]\d|[01]?\d\d?)$')
            compile_host=re.compile('^(\w+\.|)\w+\.+\w+$')  # support two format host address www.example.com and example.com　
            if compile_ip.match(tar):    
                target = tar
            elif compile_host.match(tar):
                target = tar
            else:
                print "Error, invalid ip or host address"
                print 
                usage()   
    
        # read the commandline options
        try:
            opts, args = getopt.getopt(sys.argv[1:],"huiasdt:p:c:",["help","udp","icmp","arp","syn","define","target=","port=", "count="])
        except getopt.GetoptError as err:
            print str(err)
            usage()
    
    
        for o,a in opts:
            if o in ("-h","--help"):
                usage()
            elif o in ("-u", "--udp"):
                method = "udp"
            elif o in ("-i", "--icmp"):
                method = "icmp"
            elif o in ("-a", "--arp"):
                method = "arp"
            elif o in ("-s", "--syn"):
                method = "syn"            
            elif o in ("-d", "--define"):
                method = "define"
            elif o in ("-t", "--target"):
                target = at
            elif o in ("-p", "--port"):
                port = int(a)
            elif o in ("-c", "--count"):
                count = int(a)
            else:
                assert False,"Unhandled Option"
                
        #print method, target, port, count
        
        if method == "icmp":
            icmping()
        elif method == "udp":
            udping()         
        elif method == "arp":
            #arping1()
            arping2()
        elif method == "syn":
            tcping()
            result()
        elif method == "define":
            define()
            result()
        else:
            usage()
    except KeyboardInterrupt:
        print "Ctrl + c for exit."
        sys.exit(0)
            
    print 
    print 
    print "ping ended"    
    
            
if __name__ == '__main__':
    main()
```
