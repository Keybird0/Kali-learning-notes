# IBM AppScan

> Watchfire AppScan 于2007年被IBM收购，成为 IBM Security AppScan Standard。业界优秀的Web应用安全测试工具，支持向导和完全配置两种扫描方式。

## 0x01 扫描过程

AppScan 采用两阶段扫描:

1. **探索阶段**: 爬取目标Web应用，发现URL和参数
2. **测试阶段**: 对发现的URL进行安全测试

第一个过程发现新的URL地址后，下一个扫描过程自动开始。

## 0x02 Glass Box 灰盒测试

类似于 Acunetix 的 AcuSensor:

- Agent 收集服务器端源代码信息和其他数据
- 支持 **Java**、**.NET** 两种平台

## 0x03 CLI 命令行

AppScan Standard 安装后会将安装目录加入系统 PATH，核心命令为 `AppScanCMD.exe`。

命令结构: `AppScanCMD` + 子命令 + 参数

### Exec 命令

使用指定 URL 创建新扫描、运行并保存:

| 参数 | 说明 |
|------|------|
| `/starting_url` | 设置扫描起始 URL |
| `/base_scan` | 源扫描文件（复用配置） |
| `/dest_scan` | 新扫描文件保存位置 |
| `/scan_template` | 扫描模板文件 |
| `/old_host` / `/new_host` | 域名替换（脚本复用） |
| `/login_file` | 登录序列文件 |
| `/policy_file` | 测试策略文件 |
| `/report_file` | 报告输出路径 |
| `/report_type` | 报告格式: xml/pdf/rtf/txt/html |
| `/explore_only` | 仅运行探索阶段 |
| `/test_only` | 仅运行测试阶段 |

### Report 命令

载入扫描文件并生成报告，参数与 Exec 中的同名参数相同。

### 退出代码

| 代码 | 意义 |
|------|------|
| 0 | 成功完成 |
| 1 | 启动失败 |
| 2 | 命令行错误 |
| 3 | 许可证无效 |
| 4 | 装入失败 |
| 5 | 扫描失败 |
| 6 | 报告失败 |

## 0x04 CLI 使用示例

### 针对单个故事的测试

```bash
AppScanCMD exec /starting_url http://sample.com/store1 \
  /scan_template mytemplate.scant \
  /policy_file itcs104.policy \
  /login_file user.login \
  /manual_explore_file store1.exd \
  /test_only \
  /report_file report/store1_report.pdf \
  /report_type pdf
```

### 脚本复用到新域名

```bash
AppScanCMD exec /starting_url http://sampleUAT.com/store1 \
  /scan_template mytemplate.scant \
  /old_host http://sampleFVT.com \
  /new_host http://sampleUAT.com \
  /policy_file itcs104.policy \
  /login_file user.login \
  /manual_explore_file store1.exd \
  /test_only \
  /report_file report/store1_report.pdf \
  /report_type pdf
```

## 0x05 提高脚本复用性

- 将通用扫描配置导出为 `.scant` 模板
- 将测试策略导出为 `.policy` 文件
- 将登录脚本导出为 `.login` 文件
- 将手工探索的 URL 导出为 `.exd` 文件
- 将多步骤操作序列导出为 `.seq` 文件

> 可通过 Ant 集成 AppScan Standard CLI 实现自动化构建安全测试。
