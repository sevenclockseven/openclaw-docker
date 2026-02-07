# OpenClaw Docker

[![Build Status](https://img.shields.io/github/actions/workflow/status/senvenclockseven/openclaw-docker/build.yml?branch=main)](https://github.com/senvenclockseven/openclaw-docker/actions)

本项目用于自动监控 [openclaw/openclaw](https://github.com/openclaw/openclaw) 仓库的 Release 更新，并自动构建 Docker 镜像推送到 Docker Hub。

## 镜像列表

| 镜像地址 | 说明 |
|---------|------|
| `senvenclockseven/openclaw-cn:latest` | 最新稳定版 |
| `senvenclockseven/openclaw-cn:v1.2.3` | 指定版本号 |
| `senvenclockseven/openclaw-cn:20260207` | 按发布日期打包 |

## 自动构建机制

- **定时任务**：每天 UTC 0 点检查上游仓库是否有新 Release
- **手动触发**：点击 GitHub Actions 手动运行
- **Webhook**：上游仓库发布新版本时自动触发

## 快速使用

```bash
# 拉取最新镜像
docker pull senvenclockseven/openclaw-cn:latest

# 运行 gateway 服务
docker run -d \
  --name openclaw-gateway \
  -p 18789:18789 \
  -p 18790:18790 \
  -e CLAUDE_AI_SESSION_KEY=your_session_key \
  -v $(pwd)/data/.openclaw:/home/node/.openclaw \
  -v $(pwd)/data/clawd:/home/node/clawd \
  senvenclockseven/openclaw-cn:latest \
  node dist/index.js gateway --bind lan --port 18789
```

## Docker Compose 部署

```yaml
version: '3.8'

services:
  openclaw-cn:
    image: senvenclockseven/openclaw-cn:latest
    container_name: openclaw-cn
    restart: unless-stopped
    ports:
      - "18789:18789"
      - "18790:18790"
    environment:
      - HOME=/home/node
      - TERM=xterm-256color
      - CLAUDE_AI_SESSION_KEY=${CLAUDE_AI_SESSION_KEY}
      - CLAUDE_WEB_SESSION_KEY=${CLAUDE_WEB_SESSION_KEY}
      - CLAUDE_WEB_COOKIE=${CLAUDE_WEB_COOKIE}
    volumes:
      - ${OPENCLAW_CONFIG_DIR:-./data/.openclaw}:/home/node/.openclaw
      - ${OPENCLAW_WORKSPACE_DIR:-./data/clawd}:/home/node/clawd
    command: >
      node dist/index.js gateway
      --bind ${OPENCLAW_GATEWAY_BIND:-lan}
      --port ${OPENCLAW_GATEWAY_PORT:-18789}
```

## 环境变量

| 变量 | 必填 | 默认值 | 说明 |
|------|------|--------|------|
| `CLAUDE_AI_SESSION_KEY` | 是 | - | Claude AI 会话密钥 |
| `CLAUDE_WEB_SESSION_KEY` | 否 | - | Web 会话密钥 |
| `CLAUDE_WEB_COOKIE` | 否 | - | Web Cookie |
| `OPENCLAW_GATEWAY_BIND` | 否 | `lan` | 绑定接口 (`lan` 或 `127.0.0.1`) |
| `OPENCLAW_GATEWAY_PORT` | 否 | `18789` | 网关端口 |
| `OPENCLAW_BRIDGE_PORT` | 否 | `18790` | 桥接端口 |

## 本地开发

```bash
# 克隆仓库
git clone https://github.com/senvenclockseven/openclaw-docker.git
cd openclaw-docker

# 本地构建测试
docker build -t openclaw-cn:local .
```

## GitHub Actions

工作流文件：`.github/workflows/build.yml`

### Secrets 配置

在 GitHub 仓库 Settings → Secrets 中添加：

| Secret Name | 说明 |
|------------|------|
| `DOCKERHUB_USERNAME` | Docker Hub 用户名 |
| `DOCKERHUB_TOKEN` | Docker Hub Access Token |
| `GH_PAT` | GitHub Personal Access Token（repo 权限） |

### 获取 Docker Hub Token

1. 登录 [Docker Hub](https://hub.docker.com/)
2. 进入 Account Settings → Security
3. 点击 New Access Token
4. 复制生成的 Token

## 协议

本项目遵循上游仓库 [openclaw/openclaw](https://github.com/openclaw/openclaw) 的开源协议。
