# OWASP ZAP

> Zed Attack Proxy，OWASP出品的Web应用集成渗透测试和漏洞挖掘工具。开源免费、跨平台、简单易用，类似于 Burpsuite 的开源替代。

## 0x01 安装

```bash
# Kali 中已预装，如未安装：
sudo apt update
sudo apt install -y zaproxy

# 或使用 Docker
docker run -u zap -p 8080:8080 -p 8090:8090 -i ghcr.io/zaproxy/zaproxy:stable zap-webswing.sh
```

## 0x02 核心功能

- **代理拦截**: 拦截和修改 HTTP/HTTPS 请求/响应，支持 WebSocket 拦截
- **主动扫描**: 自动对目标发送攻击载荷，检测 SQL 注入、XSS、路径遍历、命令注入等
- **被动扫描**: 分析经过代理的所有流量，检测信息泄露、不安全的 Cookie 设置、缺失的安全头等
- **爬虫 (Spider)**: 传统 HTML 链接解析 + Ajax Spider（浏览器引擎爬取 JS 渲染页面）
- **Fuzzer**: 对参数进行模糊测试，支持自定义字典和载荷
- **暴力破解**: 支持目录扫描和密码爆破
- **API**: 通过 `http://zap/` 访问，支持 CI/CD 集成

## 0x02 基本设置

1. **本地代理**: 菜单栏 → 工具 → 选项 → 本地代理
2. **连接设置**: 工具 → 选项 → Connection（设置超时、网络代理、认证）
3. **Spider 设置**: 工具 → 选项 → Spider（连接线程等）
4. **暴力破解**: 工具 → 选项 → 暴力破解（导入字典文件）
5. **扫描策略**: 分析 → 扫描策略

```bash
# 确认 ZAP 代理端口
netstat -pantu | grep 8080
```

## 0x03 重要配置

| 配置项 | 说明 |
|--------|------|
| **Persist Session** | 持久化会话 |
| **Mode** | safe / Protected / Standard / ATTACK |
| **Scan policy** | 扫描策略配置 |
| **Anti CSRF Tokens** | CSRF Token处理 |
| **HTTPS CA** | CA证书配置 |
| **Scope / Contexts** | 测试范围与上下文 |
| **Http Sessions** | Session Token管理 |
| **Passive scan** | 被动扫描规则 |

## 0x04 标准扫描工作流程

1. **设置代理**: 配置浏览器使用 ZAP 代理
2. **手动爬网**: 手动浏览应用，探索所有功能
3. **自动爬网**: 使用 Spider 发现遗漏的链接
4. **暴力扫描**: 使用字典扫描未引用的文件和目录
5. **主动扫描**: 自动扫描发现基本漏洞
6. **手动测试**: 针对扫描结果进行人工验证和深入测试

## 0x05 模糊测试 (Fuzzer)

大量无效或意外数据提交到目标的技术:

- 菜单栏 → 工具 → 选项 → Fuzzer 导入测试列表
- 可选择默认测试的漏洞类型

## 0x06 命令行使用

```bash
# 快速扫描（Baseline Scan）
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t https://target.com

# 完整扫描
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-full-scan.py -t https://target.com

# API 扫描
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-api-scan.py -t https://target.com/api/openapi.json -f openapi
```

## 0x07 升级插件

- 通过 `add-ons` 管理界面安装和更新插件
- 字典文件目录: ZAP安装目录下的 `dirbuster/`

## 0x08 与 Burp Suite 对比

| 特性 | OWASP ZAP | Burp Suite |
|------|-----------|------------|
| 价格 | 完全免费 | 社区版免费/专业版付费 |
| 开源 | 是 | 否 |
| 自动化扫描 | 支持 | 专业版支持 |
| 插件生态 | Marketplace | BApp Store |
| API/CI 集成 | 内置支持 | 专业版支持 |
| 适合人群 | 入门+专业 | 专业人员 |
