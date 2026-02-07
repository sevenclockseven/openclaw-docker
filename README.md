# OpenClaw Docker

自动构建 [openclaw/openclaw](https://github.com/openclaw/openclaw) 的 Docker 镜像。

## 镜像

| 镜像 | 说明 |
|-----|------|
| `ghcr.io/sevenclockseven/openclaw:latest` | 最新版本 |
| `ghcr.io/sevenclockseven/openclaw:v1.2.3` | 指定版本 |
| `ghcr.io/sevenclockseven/openclaw:20260207` | 日期标签 |

## 快速使用

```bash
docker pull ghcr.io/sevenclockseven/openclaw:latest

docker run -d \
  --name openclaw \
  -p 18789:18789 \
  -p 18790:18790 \
  -e CLAUDE_AI_SESSION_KEY=your_key \
  -v $(pwd)/data:/home/node/.openclaw \
  ghcr.io/sevenclockseven/openclaw:latest \
  node dist/index.js gateway --bind lan
```

## Docker Compose

```yaml
services:
  openclaw:
    image: ghcr.io/sevenclockseven/openclaw:latest
    ports:
      - "18789:18789"
      - "18790:18790"
    environment:
      - CLAUDE_AI_SESSION_KEY=${CLAUDE_AI_SESSION_KEY}
    volumes:
      - ./data:/home/node/.openclaw
    command: node dist/index.js gateway --bind lan
```

## 配置

| 环境变量 | 必填 | 默认值 | 说明 |
|---------|------|--------|------|
| `CLAUDE_AI_SESSION_KEY` | ✅ | - | Claude AI 会话密钥 |
| `CLAUDE_WEB_SESSION_KEY` | ❌ | - | Web 会话密钥 |
| `CLAUDE_WEB_COOKIE` | ❌ | - | Web Cookie |
| `OPENCLAW_GATEWAY_BIND` | ❌ | `lan` | 绑定接口 |
| `OPENCLAW_GATEWAY_PORT` | ❌ | `18789` | 网关端口 |

## GitHub Actions

自动检测上游更新并构建镜像。

### Secrets 配置

Settings → Secrets and variables → Actions 添加：

| Secret | 权限 |
|--------|------|
| `GH_PAT` | `repo` |

生成：GitHub Settings → Developer settings → Personal access tokens → Tokens (classic) → 勾选 `repo`
