---
name: "claude-feishu-bridge"
description: "Claude-飞书桥接服务：生成一个可公开分享的飞书机器人桥接方案骨架，用于接收飞书消息并转发到 Anthropic Messages 兼容接口。适用于创建飞书机器人原型、搭建飞书消息自动回复、或整理 Claude/兼容模型的飞书桥接示例。"
---

# Claude-飞书桥接服务

生成一个**可公开分享的桥接方案骨架**，让用户通过飞书与 Claude 或 Anthropic Messages 兼容模型对话。

## 本 skill 的定位

这是一个**原型级、可公开发布的方案骨架**，不是生产环境即插即用成品。

它提供：
- 部署步骤说明
- 安全版示例配置
- 参考 `server.js` 示例
- 公开发布前的风险提示

它**不承诺**：
- 所有厂商 API 与 Anthropic Messages 接口完全兼容
- 默认已具备完整生产安全能力
- 默认支持所有飞书消息类型和复杂会话线程

## 文件说明

本 skill 附带以下参考文件：
- `references/server.js.example`：桥接服务示例实现
- `references/.env.example`：环境变量模板
- `references/package.json.example`：依赖示例
- `references/.gitignore.example`：Git 忽略示例

当需要实际部署时，先读取这些参考文件，再按环境改成真实项目文件。

## 使用边界

### 适合场景
- 快速做飞书机器人原型
- 演示飞书消息 → AI 回复的桥接流程
- 作为 GitHub 公开示例仓库的起点

### 不适合直接上线的场景
- 对外正式生产服务
- 高并发消息处理
- 涉及严格审计、签名校验、消息去重、重试队列的企业场景

## 最小部署流程

### 1. 创建项目目录

```bash
mkdir -p claude-feishu-bridge && cd claude-feishu-bridge
mkdir -p logs
```

### 2. 安装依赖

```bash
npm init -y
npm install express axios winston dotenv
```

### 3. 创建配置文件

把 `references/.env.example` 复制为项目根目录 `.env`，填入真实值：

```bash
cp references/.env.example .env
```

### 4. 创建服务文件

把 `references/server.js.example` 复制到项目根目录：

```bash
cp references/server.js.example server.js
```

### 5. 可选：补 package.json / .gitignore

```bash
cp references/package.json.example package.json
cp references/.gitignore.example .gitignore
```

### 6. 启动服务

```bash
node server.js
```

或使用 PM2：

```bash
pm2 start server.js --name claude-feishu-bridge
pm2 logs claude-feishu-bridge
```

## 飞书应用配置

### 步骤 1：创建飞书应用
1. 访问 [飞书开放平台](https://open.feishu.cn/app)
2. 创建企业自建应用
3. 填写应用名称和描述

### 步骤 2：获取凭证
在「凭证与基础信息」中获取：
- App ID
- App Secret

### 步骤 3：配置权限
建议至少配置：
- `im:message`
- `im:message:send_as_bot`

如需额外用户信息能力，再单独加只读权限。

### 步骤 4：配置事件订阅
- Webhook URL：`https://你的域名/webhook`
- 订阅事件：`im.message.receive_v1`

### 步骤 5：发布应用
创建版本、审核并发布。

## 模型兼容性说明

本 skill 的示例代码按 **Anthropic Messages API 风格**发送请求：

```http
POST /v1/messages
x-api-key: ...
anthropic-version: 2023-06-01
```

因此：
- **Anthropic Claude 官方接口**：最直接
- **其他厂商**：仅在其实际兼容该协议时可用

不要默认假设所有国内模型平台都完全兼容。上线前必须逐个验证：
- 请求路径是否兼容
- 鉴权头是否兼容
- model 名称是否兼容
- 返回结构是否兼容
- 限流和超时行为是否兼容

## 安全要求

### 发布到 GitHub 前必须满足
1. 不上传 `.env`
2. 不上传真实日志
3. 不上传真实 webhook 域名、测试账号、内网地址
4. 不上传真实 `App Secret` / API Key
5. 不在示例里硬编码白名单用户真实 ID

### 运行时安全提醒
1. 白名单必须真正生效
2. 测试接口默认关闭，只有本地调试才开启
3. 建议补充飞书事件签名/安全校验
4. 建议限制日志内容，不记录原始敏感消息正文
5. 公网部署时建议放在反向代理后面，并配置 HTTPS

## 常用命令

```bash
# 健康检查
curl http://127.0.0.1:3000/health

# PM2 状态
pm2 status
pm2 logs claude-feishu-bridge
pm2 restart claude-feishu-bridge
pm2 stop claude-feishu-bridge
```

## 常见问题

### 1. 消息发送失败：权限不足
在飞书开放平台检查消息发送相关权限。

### 2. Webhook 验证失败
确认公网地址可访问、服务已启动、HTTPS 正常。

### 3. AI 回复为空
检查 API Key、模型名、下游接口兼容性。

### 4. Token 获取失败
检查 `FEISHU_APP_ID` 和 `FEISHU_APP_SECRET`。

## GitHub 发布建议

如果要公开发布，建议把这个 skill 作为：
- **示例桥接方案**
- **原型参考实现**
- **非生产默认配置**

不要把它描述成“开箱即用的生产级飞书-Claude 网关”。
