# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

newapi.ai 多账号自动签到系统，支持多个 newapi.ai 公益站的每日自动签到、多种身份验证方式（Cookie、GitHub OAuth、Linux.do OAuth）、智能通知和代理支持。

## 常用命令

```bash
# 安装依赖
uv sync --dev

# 下载浏览器（首次运行必须）
python3 -m camoufox fetch

# 运行签到
uv run main.py

# 运行测试
uv run pytest tests/

# 代码检查和格式化
uv run ruff check .
uv run ruff format .
```

## 代码架构

### 核心文件

- `main.py` - 主入口，编排签到流程和汇总结果
- `checkin.py` - `CheckIn` 核心类，处理签到逻辑、OAuth认证、浏览器自动化
- `sign_in_with_github.py` / `sign_in_with_linuxdo.py` - OAuth 登录模块
- `PROVIDERS.json` - 供应商配置（API端点、OAuth客户端ID等）

### utils/ 工具模块

- `config.py` - 配置管理（`AccountConfig`、`ProviderConfig`、`AppConfig`）
- `notify.py` - 通知系统（邮箱、钉钉、飞书、企业微信、PushPlus、Server酱、Telegram）
- `get_cdk.py` / `topup.py` - CDK获取和充值逻辑
- `get_cf_clearance.py` - Cloudflare 绕过
- `browser_utils.py` - 浏览器自动化辅助函数

### 执行流程

```
main.py → CheckIn.execute()
  ├─ 认证（cookies/GitHub OAuth/Linux.do OAuth）
  ├─ 浏览器自动化（Camoufox）
  ├─ 签到执行
  ├─ 充值处理（如配置了CDK）
  └─ 通知发送
```

## 关键技术决策

- **浏览器**: 使用 Camoufox（Firefox）而非 Chromium，反爬虫指纹更强
- **HTTP请求**: 使用 curl_cffi 绕过 WAF/Cloudflare
- **包管理**: 使用 uv 替代 pip
- **异步**: 全程使用 asyncio

## 配置说明

环境变量配置（参考 `.env.example`）：
- `ACCOUNTS` - 账号配置（JSON数组）
- `ACCOUNTS_LINUX_DO` / `ACCOUNTS_GITHUB` - 全局 OAuth 账号
- `PROVIDERS` - 自定义供应商配置（覆盖 PROVIDERS.json）
- `PROXY` - 全局代理配置

## 代码规范

- 使用 ruff 进行代码检查和格式化
- 单引号字符串
- 120字符行宽限制
- 遵循 pre-commit hooks 配置
