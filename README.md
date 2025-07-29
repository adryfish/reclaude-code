# Reclaude Code

<div align="center">

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Docker](https://img.shields.io/badge/Docker-Ready-blue.svg)](https://www.docker.com/)

**🚀 自建Claude Code镜像服务**

</div>

Claude Code镜像服务，可自部署的代理服务。

> 💡 如果需要部署 Claude 官网镜像，请使用：[reclaude](https://github.com/adryfish/reclaude)

## ⚠️ 免责声明

**使用本项目前请仔细阅读：**

🚨 **服务条款风险**: 使用本项目可能违反Anthropic的服务条款。请在使用前仔细阅读Anthropic的用户协议，使用本项目的一切风险由用户自行承担。

📖 **免责声明**: 本项目仅供技术学习和研究使用，作者不对因使用本项目导致的账户封禁、服务中断或其他损失承担任何责任。

## 🎯 替代方案

如果您不想自己搭建，也可以选择我们提供的服务：

### 🛠️ Flapcode - 基于 Reclaude-code 的在线服务

专为不便自建但需要使用 Claude Code 的用户打造的一站式解决方案。

**核心优势：**
- 🌐 **国内直连**：无需科学上网，即可极速访问 Claude Code 完整功能
- 🤝 **全站 Max 账号**：享受完整的模型体验，无需担心使用限制
- 💰 **计费简单透明**：采用与官方 API 相同的计费标准，每日更新使用额度
- 🔒 **安全可靠**：专业运维团队保障服务稳定性，让您专注于开发本身

## 快速开始

### Docker 运行

```shell
docker run -d \
  --name reclaude-code \
  --network host \
  -e PORT=4567 \
  -e PROXY_SERVER=http://your-proxy:port \
  -v ./data:/data \
  adryfish/reclaude-code
```

### Docker Compose 运行

创建 `docker-compose.yml`：

```yaml
services:
  reclaude-code:
    image: adryfish/reclaude-code
    container_name: reclaude-code
    network_mode: host
    env_file:
      - .env
    volumes:
      - ./data:/data
    restart: unless-stopped
```

按需创建 `.env`：

```bash
# 所有参数均为可选
PORT=4567
PROXY_SERVER=http://your-proxy:port
```

运行服务：

```shell
docker-compose up -d
```

## 环境变量

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `PORT` | 服务监听端口 | `4567` |
| `PROXY_SERVER` | HTTP 代理服务器地址 | 选填 |
| `CLAUDE_CLI_VERSION` | Claude CLI 版本号 | `1.0.54` |
| `DATA_DIR` | 数据目录路径 | `./data` |

## 使用说明

### 1. 启动服务
启动服务后，访问 `http://localhost:4567/api/hello` 确认服务正常运行

### 2. 获取认证令牌
使用你的 sessionKey 获取 reclaude_token：

```shell
curl -X POST http://localhost:4567/v1/auth/token \
  -H "Content-Type: application/json" \
  -d '{"session_key": "your-session-key"}'
```

### 3. 配置环境设置
编辑或创建 `~/.claude/settings.json` 文件：

```json
{
  "env": {
    "DISABLE_TELEMETRY": "1",
    "ANTHROPIC_BASE_URL": "http://localhost:4567",
    "ANTHROPIC_AUTH_TOKEN": "your-reclaude-token"
  }
}
```

完成以上步骤后，你的 Claude Code 客户端将使用本地镜像服务，享受更稳定的访问体验。

## 常见问题

### 提示需要登录或连通性检测失败

如果 Claude Code 客户端提示需要登录或连通性检测失败，一般是缺少 `.claude.json` 文件。可以通过以下方式获取：

```shell
curl -X GET http://localhost:4567/init/config/.claude.json \
  -H "Authorization: Bearer your-reclaude-token" \
  -o ~/.claude.json
```

## 用户交流

扫码加入QQ群与其他用户交流：

<img src="qqgroup.jpg" alt="QQ群二维码" width="300">