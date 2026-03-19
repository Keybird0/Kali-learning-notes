# Armitage 图形前端

> Metasploit 的图形化前端界面，降低了 Metasploit Framework 的使用门槛，适合团队协作和可视化渗透测试。

## 0x01 简介

- 基于 Java 的 Metasploit GUI 前端
- 可视化目标管理和攻击流程
- 支持多人协作（Team Server）
- 自动化攻击推荐

## 0x02 启动

```bash
# 先启动数据库
service postgresql start

# 初始化 Metasploit 数据库
msfdb init

# 启动 Armitage
armitage
```

## 0x03 主要功能

| 功能 | 说明 |
|------|------|
| **Hosts** | 可视化管理目标主机 |
| **Attacks** | 自动匹配和推荐可用的攻击模块 |
| **Hail Mary** | 自动化攻击（对所有目标尝试所有匹配的Exploit） |
| **Pivot** | 可视化内网穿透路由 |
| **Collaboration** | 多人协作渗透测试 |

## 0x04 使用流程

1. 添加目标主机（手动或通过 Nmap 扫描）
2. 查看主机信息和开放服务
3. 选择攻击模块或使用自动推荐
4. 执行攻击并管理 Session
5. 后渗透利用（右键目标主机）

> Armitage 本质上是 Metasploit 的封装，所有操作都可以在 msfconsole 中用命令完成。
