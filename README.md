# Claude Feishu Bridge

> A prototype bridge for connecting Feishu bots with Claude or Anthropic Messages-compatible AI models.

一个 **飞书机器人 ↔ Claude / Anthropic Messages 兼容模型** 的原型桥接方案。

It demonstrates a minimal end-to-end flow:

> **Feishu message → Webhook → AI model call → Feishu reply**

它用于演示这样一条最小链路：

> **飞书消息 → Webhook 接收 → AI 模型调用 → 飞书回复**

---

## Project Status / 项目状态

**Prototype / Reference Implementation**

This repository is intended as a **public example** and **starting point**, not a production-ready gateway.

本仓库定位为：
- 原型
- 参考实现
- 可公开分享的桥接骨架

它**不是**：
- 生产级网关
- 企业级安全方案
- 默认适配所有模型平台的完整成品

---

## Features

- Receive Feishu messages via webhook
- Forward user input to Claude or compatible AI endpoints
- Send AI responses back to Feishu chats
- Environment-variable based configuration
- Optional sender allowlist
- Health check endpoint
- PM2-friendly deployment

### 中文概述

- 接收飞书 webhook 消息
- 将消息转发到 Claude 或兼容 Anthropic Messages 的模型接口
- 把 AI 回复发回飞书
- 使用环境变量管理配置
- 支持可选白名单
- 提供健康检查接口
- 适合 PM2 管理

---

## Repository Layout

This repository currently includes:

- `SKILL.md` — skill instructions and scope
- `README.md` — public-facing project overview
- `references/server.js.example` — reference server implementation
- `references/.env.example` — environment template
- `references/package.json.example` — dependency example
- `references/.gitignore.example` — git ignore example

---

## Quick Start

### 1. Prepare a local project directory

```bash
mkdir -p claude-feishu-bridge && cd claude-feishu-bridge
mkdir -p logs references
```

### 2. Copy the reference files

```bash
cp references/server.js.example server.js
cp references/.env.example .env
cp references/package.json.example package.json
cp references/.gitignore.example .gitignore
```

### 3. Install dependencies

```bash
npm install
```

### 4. Start the service

```bash
node server.js
```

Or with PM2:

```bash
pm2 start server.js --name claude-feishu-bridge
pm2 logs claude-feishu-bridge
```

---

## Feishu Setup

1. Create a self-built app in Feishu Open Platform.
2. Get:
   - App ID
   - App Secret
3. Add permissions such as:
   - `im:message`
   - `im:message:send_as_bot`
4. Configure event subscription:
   - Webhook URL: `https://your-domain/webhook`
   - Event: `im.message.receive_v1`
5. Publish the app.

---

## Compatibility Notes

This project sends requests in the **Anthropic Messages API style**:

```http
POST /v1/messages
x-api-key: ...
anthropic-version: 2023-06-01
```

That means:
- **Anthropic Claude official endpoints** are the most direct fit.
- **Other vendors** may work only if they truly implement compatible behavior.

Before real deployment, verify:
- request path
- auth headers
- model naming
- response schema
- timeout / rate-limit behavior

### 中文说明

本项目默认按 **Anthropic Messages API 风格**组织请求。
并不是所有厂商都完全兼容。正式使用前请逐项验证：
- 请求路径
- 鉴权方式
- model 名称
- 返回结构
- 限流与超时行为

---

## Security Notes

Default safety principles:

- Do not commit `.env`
- Do not commit real secrets
- Do not commit real webhook domains
- Do not commit real logs
- Keep test endpoints disabled by default
- Treat this repo as a prototype unless you harden it

If you want to use this in production, add at least:
- signature / event verification
- stricter log redaction
- HTTPS / reverse proxy
- retry / dedupe / rate limiting
- finer-grained access control

### 中文说明

默认安全要求：
- 不提交 `.env`
- 不提交真实密钥
- 不提交真实 webhook 域名
- 不提交真实日志
- 测试接口默认关闭
- 默认按原型项目使用，不直接当生产系统上线

如需正式上线，至少补：
- 事件签名校验
- 更严格的日志脱敏
- HTTPS / 反向代理
- 重试、去重、限流
- 更细粒度权限控制

---

## Use Cases

You can use this project when you want to:

- talk to Claude inside Feishu
- prototype an internal AI bot for a team
- connect Feishu messages to an Anthropic-compatible backend
- validate the minimum bridge before building production features

### 中文适用场景

你可以把它用于：
- 在飞书里接入 Claude
- 做公司内部 AI 问答机器人原型
- 把飞书消息接到 Anthropic 兼容模型上
- 先跑通最小桥接链路，再逐步补生产能力

---

## Disclaimer

This project is a minimal prototype and reference implementation.
Use it as a starting point, not as a production-ready service.

本项目是最小原型与参考实现，不应直接视为生产级服务。
