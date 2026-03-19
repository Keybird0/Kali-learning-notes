# DVWA

> Damn Vulnerable Web Application — 最经典的 Web 安全练习靶场

## 简介

DVWA 是一个用 PHP/MySQL 编写的故意存在漏洞的 Web 应用程序，用于安全专业人员测试技能和工具，也帮助开发者理解 Web 安全。

- 项目地址：[https://github.com/digininja/DVWA](https://github.com/digininja/DVWA)
- 也可直接使用 Metasploitable 2 虚拟机（内置 DVWA）

## 包含的漏洞类型

| 漏洞类型 | 难度级别 |
|---------|---------|
| Brute Force | Low / Medium / High / Impossible |
| Command Injection | Low / Medium / High / Impossible |
| CSRF | Low / Medium / High / Impossible |
| File Inclusion | Low / Medium / High / Impossible |
| File Upload | Low / Medium / High / Impossible |
| SQL Injection | Low / Medium / High / Impossible |
| SQL Injection (Blind) | Low / Medium / High / Impossible |
| XSS (Reflected) | Low / Medium / High / Impossible |
| XSS (Stored) | Low / Medium / High / Impossible |

## 快速搭建

```bash
# Docker 方式（推荐）
docker run --rm -it -p 80:80 vulnerables/web-dvwa

# 或手动安装
git clone https://github.com/digininja/DVWA.git
# 配置 PHP + MySQL 环境后访问 http://localhost/DVWA/setup.php
```

!!! tip "安全等级"
    DVWA 的安全等级分为 Low、Medium、High、Impossible 四级。建议从 Low 开始练习，逐步提升难度，对比不同等级的源码差异来理解防御方式。
