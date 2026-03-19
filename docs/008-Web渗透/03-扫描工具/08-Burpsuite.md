# Burpsuite

> Web安全工具中的瑞士军刀，PortSwigger公司开发的统一集成工具，可发现全部现代WEB安全漏洞。所有工具共享一个能处理并显示HTTP消息的可扩展框架，模块之间无缝交换信息。
>
> 官网: [https://portswigger.net](https://portswigger.net)

## 0x01 版本与启动

- **Burp Free**: 免费版，功能受限
- **Burp Professional**: 专业版，功能完整

```bash
# Linux 启动
cd /opt/burppro16
./burp.sh

# Windows 启动
run.bat
```

!!! warning
    不要关闭启动 Burpsuite 的终端窗口，否则软件也会被关闭。

## 0x02 Proxy 代理

Burpsuite 核心功能之一，拦截浏览器与服务器之间的HTTP/HTTPS通信。

**Options 配置项**:

- **Invisible**: 支持主机头/多目标域名的透明代理
- **CA**: 导入/导出CA证书（用于HTTPS拦截）
- **Intercept**: 配置入站/出站拦截规则
- **Response modify**: 修改响应内容

**代理请求与普通请求的区别**:

```
# 代理请求
GET http://example.org/foo.php HTTP/1.1
Host: example.org

# 普通请求
GET /foo.php HTTP/1.1
Host: example.org
```

## 0x03 Target 目标

- **Scope**: 定义测试范围，排除不需要的URL（如logout）
- **Filter**: 过滤显示的请求
- **Comparing site map**: 对比不同时间点的站点地图差异

## 0x04 Scanner 扫描

- **Active Scan**: 主动扫描，向目标发送测试请求
- **Passive Scan**: 被动扫描，分析经过代理的流量
- **Scan queue**: 扫描队列管理
- **Result**: 扫描结果查看

## 0x05 Intruder 入侵

用于自动化定制攻击，支持四种攻击模式:

### Sniper（狙击手）

逐个位置、逐个payload进行替换测试。

| Request | Position | Payload |
|---------|----------|---------|
| #1 | 1 | Item_1_List_1 |
| #2 | 1 | Item_2_List_1 |
| #3 | 2 | Item_1_List_1 |
| #4 | 2 | Item_2_List_1 |

### Battering ram（攻城锤）

所有位置同时使用相同payload。

| Request | Position | Payload |
|---------|----------|---------|
| #1 | 1,2 | Item_1_List_1 |
| #2 | 1,2 | Item_2_List_1 |

### Pitchfork（干草叉）

多个位置使用对应列表中的payload，一一对应。

| Request | Position | Payload |
|---------|----------|---------|
| #1 | 1,2 | Item_1_List_1, Item_1_List_2 |
| #2 | 1,2 | Item_2_List_1, Item_2_List_2 |

### Cluster bomb（集束炸弹）

多个位置使用对应列表的全排列组合。

| Request | Position | Payload |
|---------|----------|---------|
| #1 | 1,2 | Item_1_List_1, Item_1_List_2 |
| #2 | 1,2 | Item_2_List_1, Item_1_List_2 |
| #3 | 1,2 | Item_1_List_1, Item_2_List_2 |
| #4 | 1,2 | Item_2_List_1, Item_2_List_2 |

## 0x06 Repeater 重放

手动修改和重新发送单个HTTP请求的工具。

- **Request History**: 请求历史记录
- **Change request method**: 切换 GET/POST 方法
- **Change body encoding**: 修改请求体编码
- **Copy as curl command**: 复制为curl命令
- **Convert selection**: 转换选中内容的编码
- **Engagement tools**: 辅助工具，如 `Generate CSRF PoC`
- **Follow redirections**: 自动跟随重定向
- **Process cookies in redirections**: 重定向中处理Cookie

## 0x07 Sequencer 序列分析

分析程序中可预测数据的随机性，用于评估:

- Session Cookies 的随机性
- Anti-CSRF Tokens 的可预测性

**使用流程**:

1. Start live capture 开始捕获
2. Analyze 分析（数据越多越准确）
3. 查看结果:
    - **Character-level**: 字符级别分析
    - **Bit-level**: 比特级别分析

> FIPS: 美国联邦信息处理标准 (Federal Information Processing Standard)

## 0x08 Decoder 编解码

使用各种编码方式绕过服务器端输入过滤:

- URL 编码/解码
- Base64 编码/解码
- HTML 编码/解码
- **Smart decode**: 智能识别并解码

## 0x09 Extender 扩展

- **BApp Store**: 在线插件商店
- 支持 Jython 扩展（需要 jython-standalone jar）
    - 下载: [https://www.jython.org/download](https://www.jython.org/download)
- 在 Options 中配置 Jython 路径

## 0x0A 其他代理截断工具

| 工具 | 说明 |
|------|------|
| **Paros** | 老牌Web代理工具 |
| **Webscarab** | OWASP出品 |
| **Burpsuite** | 功能最全面 |
