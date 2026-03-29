# Kali Linux 渗透测试学习笔记

Kali Linux 渗透测试学习笔记，基于安全牛苑房弘老师的 [Kali Linux 渗透测试课程](https://edu.aqniu.com/course/83) 整理。

> **📌 关于本笔记**：最初于 **2018 年**编写，2026 年借助 AI 进行了现代化升级——更新过时命令和工具、标注已废弃内容、补充现代安全实践。部分原始内容可能仍基于 Kali 2018 版本环境，请以实际版本为准。

## 在线阅读

**[https://keybird0.github.io/Kali-learning-notes/](https://keybird0.github.io/Kali-learning-notes/)**

## 目录概览

| 章节 | 内容 |
|------|------|
| 前言 | 笔记介绍、法律法规 |
| 系统安装及环境部署 | Kali 安装、实验环境搭建 |
| 基本工具 | NC、WireShark、nmap、scapy、iptables |
| 信息收集 | 被动信息收集、主动信息收集 |
| 弱点扫描 | 扫描概念与工具（Nessus、Nexpose等） |
| 缓冲区溢出 | Windows/Linux 溢出实例 |
| Web渗透 | 扫描工具、手动漏洞挖掘、WebShell |
| 密码破解 | 认证、爆破、中间人攻击 |
| 流量操作与隧道 | 端口转发、SSH隧道 |
| 免杀 | 杀毒原理与免杀实践 |
| 提权 | 用户提权、漏洞提权、配置提权 |
| Metasploit | 基本使用、后渗透、社会工程学 |
| 无线攻击 | WEP/WPA/WPS 攻击 |
| 拒绝服务 | DoS 攻击原理与工具 |
| 取证 | 计算机取证、流量取证 |
| 练习 | CTF、靶场实战 |

## 本地构建

```bash
pip install -r requirements.txt
mkdocs serve
```

访问 `http://127.0.0.1:8000` 预览。

## License

[GNU General Public License v3.0](LICENSE)
