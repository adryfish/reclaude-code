# reclaude code

Claude Code 镜像服务。

> 如果需要部署 Claude 官网镜像，请使用：[reclaude](https://github.com/adryfish/reclaude)

## 快速开始

```shell
docker run -d \
  --name reclaude-code \
  --network host \
  -e PORT=4567 \
  -e RECLAUDE_SECRET=your-secret-key \
  -e PROXY_SERVER=http://your-proxy:port \
  adryfish/reclaude-code
```

### 使用 Docker Compose

创建 `docker-compose.yml` 文件：

```yaml
services:
  reclaude-code:
    image: adryfish/reclaude-code
    container_name: reclaude-code
    network_mode: host
    env_file:
      - .env
    restart: unless-stopped
```

创建 `.env` 文件：

```env
PORT=4567
PROXY_SERVER=http://your-proxy:port
RECLAUDE_SECRET=your-secret-key
RECLAUDE_SHOW_SESSION_KEY=true
CLAUDE_CLI_VERSION=1.0.51
```

运行服务：

```shell
docker-compose up -d
```

## 📢 广告

<div style="border: 2px solid #f39c12; padding: 15px; background-color: #fffbe6; border-radius: 10px;"> 
  <details open> 
    <summary><b>🌟 推荐工具：EasyChat - Claude 官网镜像服务</b></summary>

🛠️ EasyChat 是一个基于 Reclaude 部署的在线服务，适合不方便自建但希望使用 Claude 的用户。

- 🚀 **免注册体验**：开箱即用，完整还原官网功能，点击即可开始使用。
- 🌐 **国内可访问**：无需科学上网，直连 Claude 官网服务。
- 🤝 **共享会员机制**：支持使用 Claude 会员账号，无需担心封号风险。

🔗 **访问地址**：<a href="https://easychat.top" target="_blank">https://easychat.top</a>

  </details>
</div>

## 环境变量

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `PORT` | 服务监听端口 | `4567` |
| `PROXY_SERVER` | HTTP 代理服务器地址 | 选填 |
| `RECLAUDE_SECRET` | 加密密钥 | 自动生成 |
| `RECLAUDE_SHOW_SESSION_KEY` | 是否显示会话密钥 | `true` |
| `CLAUDE_CLI_VERSION` | Claude CLI 版本号 | `1.0.51` |

## 使用说明

### 1. 启动服务
启动服务后，访问 `http://localhost:4567/api/hello` 确认服务正常运行

### 2. 获取认证令牌
使用你的 sessionKey 获取 oauth_token：

```shell
curl -X POST http://localhost:4567/v1/auth/token \
  -H "Content-Type: application/json" \
  -d '{"session_key": "your-session-key"}'
```

从返回结果的 `metadata.oauth_token` 字段中获取 oauth_token。

### 3. 生成配置文件

#### 生成 .claude.json 文件
```shell
curl -X GET http://localhost:4567/init/config/.claude.json \
  -H "Authorization: Bearer your-oauth-token" \
  -o ~/.claude.json
```

#### 生成 .credentials.json 文件
```shell
# 如果 .claude 目录不存在，先创建
mkdir -p ~/.claude

# 下载 credentials 文件
curl -X GET http://localhost:4567/init/config/.credentials.json \
  -H "Authorization: Bearer your-oauth-token" \
  -o ~/.claude/.credentials.json
```

### 4. 配置环境设置
编辑或创建 `~/.claude/settings.json` 文件：

```json
{
  "env": {
    "DISABLE_TELEMETRY": "1",
    "ANTHROPIC_BASE_URL": "http://localhost:4567"
  }
}
```

### 5. 完成配置
完成以上步骤后，你的 Claude Code 客户端将使用本地镜像服务，享受更稳定的访问体验。

## API接口

### POST /v1/auth/token
获取认证令牌

**请求示例：**
```shell
curl -X POST http://localhost:4567/v1/auth/token \
  -H "Content-Type: application/json" \
  -d '{"session_key": "sk-ant-sid01-aBcDeFgHiJkLmNoPqRsTuVwXyZ1234567890AbCdEfGhIjKlMnOpQrStUvWxYz-A1B2C3AA"}'
```

**响应示例：**
```json
{
    "token_type": "Bearer",
    "access_token": "sk-ant-oat01-XyZ9WvUtSrQpOnMlKjIhGfEdCbA0987654321ZyXwVuTsRqPoNmLkJiHgFeDcBa-1A2B3C4AA",
    "expires_in": 28800,
    "refresh_token": "sk-ant-ort01-QwErTyUiOpAsDfGhJkLzXcVbNm1234567890QwErTyUiOpAsDfGhJkLzXcVbNm-5D6E7F8AA",
    "scope": "user:inference user:profile",
    "organization": {
        "uuid": "12345678-1234-1234-1234-123456789012",
        "name": "example@email.com's Organization"
    },
    "account": {
        "uuid": "87654321-4321-4321-4321-210987654321",
        "email_address": "example@email.com"
    },
    "metadata": {
        "expires_at": "2026-07-12T08:37:15.466337787Z",
        "oauth_token": "AbCdEfGhIjKlMnOpQrStUvWxYz1234567890QwErTyUiOpAsDfGhJkLzXcVbNm9876543210..."
    }
}
```

### GET /init/config/.claude.json
获取用户配置文件（需要在请求头中包含 oauth_token）

**请求示例：**
```shell
curl -X GET http://localhost:4567/init/config/.claude.json \
  -H "Authorization: Bearer AbCdEfGhIjKlMnOpQrStUvWxYz1234567890..."
```

**响应示例：**
```json
{
    "installMethod": "unknown",
    "autoUpdates": false,
    "theme": "light",
    "firstStartTime": "2025-07-12T08:39:43.820526757Z",
    "userID": "a1b2c3d4e5f6789012345678901234567890abcdef123456789012345678901234",
    "oauthAccount": {
        "accountUuid": "87654321-4321-4321-4321-210987654321",
        "emailAddress": "example@email.com",
        "organizationUuid": "12345678-1234-1234-1234-123456789012",
        "organizationRole": "admin",
        "workspaceRole": null,
        "organizationName": "example@email.com's Organization"
    },
    "hasCompletedOnboarding": true,
    "lastOnboardingVersion": "1.0.51"
}
```

### GET /init/config/.credentials.json
获取用户凭证信息（需要在请求头中包含 oauth_token）

**请求示例：**
```shell
curl -X GET http://localhost:4567/init/config/.credentials.json \
  -H "Authorization: Bearer AbCdEfGhIjKlMnOpQrStUvWxYz1234567890..."
```

**响应示例：**
```json
{
    "claudeAiOauth": {
        "accessToken": "Xy9ZaBcDeFgHiJkLmNoPqRsTuVwX1234567890AbCdEfGhIjKlMnOpQrStUvWxYz9876543210...",
        "refreshToken": "QwErTyUiOpAsDfGhJkLzXcVbNm1234567890QwErTyUiOpAsDfGhJkLzXcVbNm0987654321...",
        "expiresAt": 1783845645000,
        "scopes": [
            "user:inference",
            "user:profile"
        ],
        "subscriptionType": "pro"
    }
}
```

## 用户交流

扫码加入QQ群与其他用户交流：

<img src="qqgroup.jpg" alt="QQ群二维码" width="300">