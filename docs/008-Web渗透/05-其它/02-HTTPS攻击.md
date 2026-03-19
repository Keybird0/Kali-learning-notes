# HTTPS攻击

## 0x01 HTTPS简介
+ 为实现CIA (保密性, 完整性, 可用性)
+ 解决的是信息传输过程中数据被篡改,窃听
+ 加密通过,密钥交换算法,对称加密,非对称加密,单向加密(HASH)手段

## 0x02 HTTPS攻击方法
+ 降级攻击
+ 解密攻击(明文, 证书伪造)
+ 协议漏洞, 实现方法的漏洞, 配置不严格

## 0x03 知识铺垫
### 安全套接层(Secure socket layer)
+ 保证网络通信安全的加密协议
+ 1994年由Netscape开发成为统一标准
+ 1999年TLS(Transport layer security)取代SSL v3
+ 近年来发现的ssl协议漏洞使业界认为其漏洞已不可软件修复.主要的严重漏洞
+ - Heartbleed
+ - POODLE
+ - BEAST
+ TLS最新版本为1.2
+ 由发展历史等关系, TLS/SSL, HTTPS, HTTP over SSL 一般表示相同含义

 ### SSL/TLS其它应用场景
 > SSL/TLS 用于HTTP, 称为HTTPS
 + 邮件传输(服务器间, 客户端与服务器间)
 + 数据库服务器间
 + LDAP身份认证服务器间
 + SSL VPN
 + DRP通信过程的加密和身份认证

 ### 加密过程
1. 握手
2. 协商加密协议
3. 获取公钥证书
4. 验证公钥证书
5. 交换会话秘钥
6. 加密传输
> 1. 浏览器将自己支持的加密规则发给服务器
> 2. 服务器端收到请求,发送给客户端公钥证书(这里用到非对称加密方式)和HASH算法
> 3. 客户端收到公钥之后,利用自己的信任的根证书对收到公钥进行验证.若通过,客户端再随机生成对称密钥, 再利用服务器发给客户端的公钥对对称密钥进行加密,HASH计算握手信息,在利用生成密钥对hash值加密,然后把公钥加密的对称密钥及对称密钥加密的hash值发送给服务器.
> 4. 之后服务器利用自己的私钥对加密的对称密钥进行解密,得到对称密钥. 再利用对称密钥解密hash值,对hash值进行验证.
> 5. 之后信息的传输内容是这样的: 客户端:先用对称密钥加密要传输的信息,再利用Hash算法得出加了密的信息的HASH值.再利用公钥对hash值进行加密,之后把对称密钥加密了的信息,及其hash值,及hash值利用公钥加密后的信息,传输给服务器.   服务器: 利用对称密钥对要传输的内容进行加密,并计算hash,然后传输给客户端.(有点绕,说得也不是非常准确,大致就是这样.看不懂多看几遍)



### 常用的加密方法
#### 非对称加密算法(用于key exchange)
+ Diffie-Hellman
+ Rivest Shamir Adleman (RSA)
+ Elliptic Curve Cryptography (ECC)


#### 对称加密算法(用于加密数据)
+ Data Encryption Standard (DES) / 3DES
+ Advance Encryption Standard (AES)
+ nternational Data Encryption Algorithm (IDEA)
+ Rivest Cipher 4 (RC4) (应用场景举例: WEP, TLS/SSL, RDP, Secure Shell)

#### Hash算法(用于校验)
+ MD5
+ SHA-1
+ SHA-2 (TLS 1.2唯一支持)

### SSL的弱点
- ssl是不同的对称, 非对称, 单项加密算法的组合加密实现(cipher suite)
- 服务器为实现更好的兼容性, 支持大量的过时的cipher suite.
- 协商过程中可能强迫降低加密强度.
- 现代处理器的计算性能使可再可接受的时间内破解过时的加密算法.


## 0x04 HTTPS弱点扫描
+ `openssl`

```
openssl s_client connect www.baidu.com:443

检查支持的不安全的cipher
openssl s_client -tls1_2 -cipher 'NULL,EXPORT,LOW,DES' -connect www.baidu.com:443
```

+ `sslscan`
> 自动识别ssl配置错误, 过期协议, 过时的cipher suite, 默认检查crime, heartbleed漏洞, 绿色表示安全, 红色,黄色需引起注意


```
sslscan --tlsall www.baidu.com:443

分析证书详细信息
sslscan --show-certificate --no-ciphersuites www.baidu.com
```

+ `sslyze`
> 检查ssl过时版本, 检查存在弱点的cipher suite, 扫描多站点时,支持读自文件,检查是否支持回话恢复


```
sslyze --regular www.baidu.com:443
```

+ `nmap`
```
nmap --script=ssl-enum-ciphers.nse www.baidu.com
```

+ 在线查询
https://www.ssllabs.com/ssltest
