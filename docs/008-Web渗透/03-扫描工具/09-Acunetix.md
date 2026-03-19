# Acunetix Web Vulnerability Scanner

> 商业Web漏洞扫描器，支持自动化和手动爬网，具备AcuSensor灰盒测试能力。

## 0x01 主要特性

- **自动/手动爬网**: 支持 AJAX、JavaScript 动态页面
- **AcuSensor 灰盒测试**:
    - 可发现爬网无法发现的文件
    - 提供额外的漏洞扫描能力
    - 可发现存在漏洞的源码行号
    - 支持 PHP（5.0以上）、.NET（不获取源码情况下注入已编译.NET）
- **合规报告**: 生成 PCI、ISO 27001 标准合规报告
- **网络扫描**: FTP, DNS, SMTP, IMAP, POP3, SSH, SNMP, Telnet
- **集成 OpenVAS**: 扫描网络层漏洞

## 0x02 AcuSensor 安装（PHP）

1. 在 Acunetix 中生成 agent 文件 `acu_phpaspect.php`
2. 将文件拷贝到目标服务器 Web 程序可访问的目录

```bash
# 拷贝到目标服务器
scp acu_phpaspect.php user@target:/var/www/html/

# 设置文件权限
sudo chown www-data:www-data acu_phpaspect.php
```

3. 修改 `.htaccess` 或 `php.ini` 配置:

```ini
php_value auto_prepend_file '[path to acu_phpaspect.php file]'
```

## 0x03 使用流程

1. 配置扫描目标 URL
2. 选择扫描策略（完整扫描/快速扫描等）
3. 配置 AcuSensor（可选）
4. 启动扫描
5. 查看结果并生成报告
