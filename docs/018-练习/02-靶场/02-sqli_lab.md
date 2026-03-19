# SQLi-Labs

> SQL 注入专项练习靶场，包含 65+ 个注入场景

## 简介

SQLi-Labs 是学习 SQL 注入最全面的练习平台，涵盖了几乎所有类型的 SQL 注入场景。

- 项目地址：[https://github.com/Audi-1/sqli-labs](https://github.com/Audi-1/sqli-labs)

## 课程结构

| 课程 | 内容 |
|------|------|
| Less 1-20 | GET 型基础注入（字符型、数字型、盲注） |
| Less 21-37 | POST 型注入、Cookie 注入、Header 注入 |
| Less 38-53 | Stacked Injection（堆叠注入） |
| Less 54-65 | 挑战模式（限制尝试次数） |

## 快速搭建

```bash
# Docker 方式
docker run -d -p 80:80 acgpiano/sqli-labs

# 或手动安装
git clone https://github.com/Audi-1/sqli-labs.git
# 需要 PHP 5.x + MySQL 环境
```

!!! info "注意"
    SQLi-Labs 依赖较老版本的 PHP（5.x），在现代 PHP 7/8 环境下部分 Less 可能报错，建议使用 Docker 方式部署。
