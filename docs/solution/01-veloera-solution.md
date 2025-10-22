# Veloera统一AI工具管理方案

## 1. 方案概述

基于Veloera作为Standalone API Gateway，实现多个Provider的汇集、HTTP API转发和格式改写，配合Mise实现项目级自动切换。

## 2. 核心架构

```
┌─────────────────────────────────────────────────────────────┐
│                    Veloera (Standalone Gateway)            │
│  ┌─────────────────┐  ┌─────────────────┐  ┌──────────────┐ │
│  │  BigGLM API    │  │ BigGLM Standard │  │ DeepSeek API │ │
│  └─────────────────┘  └─────────────────┘  └──────────────┘ │
│  ┌─────────────────┐  ┌─────────────────┐                   │
│  │   Kimi API      │  │   其他Provider  │                   │
│  └─────────────────┘  └─────────────────┘                   │
└─────────────────────────────────────────────────────────────┘
                              │ HTTP API转发/改写 + 特殊endpoint支持
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      统一OpenAI兼容HTTP API                  │
│  http://localhost:3000/v1                                   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Mise (项目管理层)                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐   │
│  │  项目A配置   │  │  项目B配置   │  │     项目C配置        │   │
│  │ BigGLM/GLM  │  │  BigGLM/GLM   │  │   DeepSeek/Chat     │   │
│  └─────────────┘  └─────────────┘  └─────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                        工具层                               │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐   │
│  │Claude Code  │  │Claude CLI   │  │  Qwen/Gemini CLI    │   │
│  │+ CCR        │  │             │  │                     │   │
│  └─────────────┘  └─────────────┘  └─────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## 3. 方案优势

### 3.1 解决的核心问题
- Provider统一汇集: 通过Veloera统一管理BigGLM、DeepSeek、Kimi等
- API Key集中管理: 一处配置，多处使用，更换key只需要改一个地方
- 项目隔离: 不同项目使用不同的AI配置
- 工具统一: 支持第三方API的AI工具使用统一的配置源和API接口
- 规则同步: 统一管理各工具的rules配置
- IDE集成: Cursor、VS Code Copilot、Kiro等IDE的AI配置

### 3.2 技术优势
- 统一HTTP接口: 支持第三方API的工具通过统一的OpenAI兼容HTTP API访问
- API转发/改写: 自动转换不同Provider的API格式为统一格式
- 统一endpoint支持: 通过统一的API接口访问所有Provider
- 项目级自动切换: 进入不同项目目录自动切换合适的model/provider
- 使用透明化: Web界面查看所有provider的使用情况和统计
- 高可用性: Veloera提供负载均衡和故障转移

## 4. 实施架构

### 4.1 层次结构

1. Provider汇集层 (Veloera): 管理所有Provider API Key，提供统一HTTP API接口
2. 项目层 (Mise): 项目级环境管理，实现项目切换
3. 工具层: 各种AI工具通过统一接口使用

### 4.2 数据流

```
外部Provider → Veloera → 统一HTTP API → Mise环境变量 → CLI工具
```

## 5. Veloera部署配置

### 5.1 Docker部署 (推荐)

```yaml
# docker-compose.yml
version: '3.8'
services:
  veloera:
    restart: unless-stopped
    image: ghcr.io/veloera/veloera:latest
    env_file: .env
    container_name: veloera
    ports:
      - "3000:3000"
    volumes:
      - ./data:/data
    environment:
      - TZ=Asia/Shanghai
```

### 5.2 Veloera配置文件

```json
{
  "providers": [
    {
      "name": "bigglm",
      "type": "openai",
      "baseURL": "https://open.bigmodel.cn/api/paas/v4",
      "apiKey": "your-bigglm-api-key",
      "models": ["glm-4.6", "glm-4.5"],
      "description": "BigGLM Standard Models - 通用对话模型，支持编程任务"
    },
    {
      "name": "deepseek",
      "type": "openai",
      "baseURL": "https://api.deepseek.com/v1",
      "apiKey": "your-deepseek-api-key",
      "models": ["deepseek-chat"],
      "description": "DeepSeek Chat - 性价比高的编程模型"
    },
    {
      "name": "kimi",
      "type": "openai",
      "baseURL": "https://api.moonshot.cn/v1",
      "apiKey": "your-kimi-api-key",
      "models": ["moonshot-v1-8k"],
      "description": "Kimi Moonshot - 长文本处理模型"
    }
  ],
  "globalSettings": {
    "defaultTimeout": 30000,
    "maxRetries": 3,
    "enableLogging": true
  }
}
```

## 6. Mise配置管理

### 6.1 全局配置

```toml
[env]
# Veloera全局配置
VELOERA_URL = "http://localhost:3000/v1"
VELOERA_TOKEN = "your-veloera-token"

# 默认配置
DEFAULT_PROVIDER = "bigglm"
DEFAULT_MODEL = "GLM-4.6"

# CLI工具配置
OPENAI_API_KEY = "{{ env.VELOERA_TOKEN }}"
OPENAI_BASE_URL = "{{ env.VELOERA_URL }}"
ANTHROPIC_AUTH_TOKEN = "{{ env.VELOERA_TOKEN }}"
ANTHROPIC_BASE_URL = "{{ env.VELOERA_URL }}"
```

### 6.2 项目级配置示例

#### 开发项目
```toml
[env]
# 工具配置
OPENAI_MODEL = "GLM-4.6"
CLAUDE_MODEL = "GLM-4.6"
```

#### 数据分析项目
```toml
[env]
OPENAI_MODEL = "GLM-4.6"
CLAUDE_MODEL = "GLM-4.6"
```

#### 经济模式项目
```toml
[env]
OPENAI_MODEL = "moonshot-v1-8k"
CLAUDE_MODEL = "moonshot-v1-8k"
```

## 7. 工具集成配置

### 7.1 CLI工具配置
```bash
# 自动通过mise环境变量配置
export OPENAI_API_KEY="$(mise get VELOERA_TOKEN)"
export OPENAI_BASE_URL="$(mise get VELOERA_URL)"
export CLAUDE_MODEL="${CLAUDE_MODEL:-GLM-4.6}"
```

### 7.2 Claude Code + CCR配置
```bash
# CCR配置文件 ~/.claude-code-router/config.json
{
  "APIKEY": "your-ccr-api-key",
  "Providers": [
    {
      "name": "veloera",
      "api_base_url": "http://10.1.1.11:3000/v1/chat/completions",
      "api_key": "sk-your-veloera-api-key",
      "models": [
        "glm-4.5",
        "glm-4.5-air",
        "glm-4.5v",
        "glm-4-long",
        "glm-4.6"
      ],
      "transformer": {
        "use": [
          "OpenAI"
        ]
      }
    }
  ],
  "Router": {
    "default": "veloera,glm-4.6",
    "background": "veloera,glm-4.5",
    "think": "veloera,glm-4.5",
    "longContext": "veloera,glm-4-long",
    "longContextThreshold": 200000,
    "webSearch": "veloera,glm-4.5",
    "image": "veloera,glm-4.5v"
  }
}
```

## 6. 项目配置示例

### 6.1 模型切换脚本
```bash
#!/usr/bin/env bash
set -euo pipefail

switch_model() {
    local model="$1"
    echo "🔄 切换到模型: $model"
    export OPENAI_MODEL="$model"
    export CLAUDE_MODEL="$model"
    echo "✅ 已切换到模型: $model"
}
```

### 6.2 状态查看脚本
```bash
#!/usr/bin/env bash
set -euo pipefail

echo "🤖 AI工具状态报告"
echo "=================="
echo "📡 Veloera配置:"
echo "  URL: ${VELOERA_URL:-未设置}"
echo "  当前模型: ${CLAUDE_MODEL:-未设置}"
echo "📁 项目信息:"
echo "  项目路径: $(pwd)"
echo "  项目名称: $(basename "$(pwd)")"
echo "🛠️ 工具配置:"
echo "  OPENAI_API_KEY: ${OPENAI_API_KEY:0:8}..."
echo "  OPENAI_BASE_URL: ${OPENAI_BASE_URL:-未设置}"
```

### 6.3 连接测试脚本
```bash
#!/usr/bin/env bash
set -euo pipefail

echo "🧪 测试AI工具连接..."

VELOERA_URL="${VELOERA_URL:-http://localhost:3000/v1}"
VELOERA_TOKEN="${VELOERA_TOKEN:-未设置}"
MODEL="${CLAUDE_MODEL:-glm-4.6}"

response=$(curl -s "$VELOERA_URL/chat/completions" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $VELOERA_TOKEN" \
    -d '{"model": "'"$MODEL"'", "messages": [{"role": "user", "content": "Hello, test connection"}], "max_tokens": 10}')

if echo "$response" | grep -q '"choices"'; then
    echo "✅ Veloera连接成功"
else
    echo "❌ Veloera连接失败"
fi
```

## 9. 使用流程

### 9.1 初始部署
```bash
# 1. 部署Veloera
docker-compose up -d

# 2. 配置Provider
# 编辑config.json文件
# 添加BigGLM、DeepSeek、Kimi等Provider

# 3. 配置Mise全局环境
mise settings set experimental true
```

### 9.2 项目配置
```bash
# 进入项目目录
cd ~/projects/my-web-app

# 初始化mise
mise init

# 创建项目配置
cat > .mise.toml << EOF
[env]
OPENAI_MODEL = "GLM-4.6"
CLAUDE_MODEL = "GLM-4.6"
EOF

# 应用配置
eval "$(mise env bash)"
```

### 9.3 日常使用
```bash
# 查看状态
./scripts/status.sh

# 切换模型
./scripts/switch-model.sh deepseek-chat

# 使用AI工具
claude "写一个React组件"
qwen "分析这个数据"
```

## 10. 维护和故障排查

### 10.1 常见问题排查

Veloera连接问题:
```bash
docker ps | grep veloera
lsof -i :3000
curl -s "http://localhost:3000/v1/models"
```

配置问题:
```bash
mise env | grep -E "(VELOERA|OPENAI|ANTHROPIC)"
cat .mise.toml
```

### 10.2 维护建议

定期检查:
- 检查Veloera服务状态
- 验证API连接性
- 备份配置数据

备份策略:
- Veloera配置文件备份
- Provider Token安全存储
- 配置版本控制

## 11. 优势总结

- 统一管理: Veloera统一管理所有Provider，一处配置处处使用
- HTTP API转发: 自动转换不同Provider的API格式
- 项目级切换: Mise实现项目间的自动切换
- 配置简化: 更换API Key只需要在Veloera改一个地方
- 使用透明化: Web界面查看使用统计和成本
- 高可用性: Veloera提供负载均衡和故障转移
- 扩展性强: 轻松添加新的Provider和工具

这个方案完美解决了Provider分散、配置复杂的核心痛点！