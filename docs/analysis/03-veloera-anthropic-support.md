# Veloera 对 Anthropic 格式支持分析

## 相关工具GitHub地址

- **Veloera**: https://github.com/veloera/veloera
- **Claude Code**: https://claude.ai/code (官方工具，非开源)
- **cc-switch**: https://github.com/HoBeedzc/cc-switch
- **Mise**: https://github.com/jdx/mise

## 1. 支持情况总结

✅ **Veloera 确实支持 Anthropic/Claude 格式！**

根据官方文档确认：
- **原生支持 Claude Messages 格式**
- **支持推理模型** (如 `claude-3-7-sonnet-20250219-thinking`)
- **OpenAI 兼容格式同时可用**
- **流式输出支持**

## 2. 支持的 API 格式

### 2.1 Anthropic Claude Messages API
```bash
# Claude Messages 格式
POST /v1/messages
{
  "model": "claude-3-5-sonnet-20241022",
  "max_tokens": 1024,
  "messages": [
    {"role": "user", "content": "Hello, Claude"}
  ]
}
```

### 2.2 OpenAI 兼容格式 (同时支持)
```bash
# OpenAI 格式 (Veloera 自动转换)
POST /v1/chat/completions
{
  "model": "claude-3-5-sonnet-20241022",
  "max_tokens": 1024,
  "messages": [
    {"role": "user", "content": "Hello, Claude"}
  ]
}
```

## 3. CLI工具环境变量配置

### 3.1 Claude Code 配置
```bash
# Claude Code 配置 (使用Anthropic格式)
export ANTHROPIC_AUTH_TOKEN="your-veloera-token"
export ANTHROPIC_BASE_URL="http://172.17.0.1:3000/v1"

# 直接使用 Claude Code
claude "写一个React组件"
```

### 3.2 Qwen Code 配置
```bash
# Qwen Code 配置 (使用OpenAI兼容格式)
export OPENAI_API_KEY="your-veloera-token"
export OPENAI_BASE_URL="http://172.17.0.1:3000/v1"

# 直接使用 Qwen Code
qwen "写一个Python脚本"
```

### 3.3 Codex CLI 配置 (预期)
```bash
# Codex CLI 配置 (使用OpenAI兼容格式)
export OPENAI_API_KEY="your-veloera-token"
export OPENAI_BASE_URL="http://172.17.0.1:3000/v1"

# 使用 Codex CLI
codex "写一个函数"
```

### 3.4 其他可能的CLI工具
```bash
# 对于支持OpenAI格式的其他CLI工具
export OPENAI_API_KEY="your-veloera-token"
export OPENAI_BASE_URL="http://172.17.0.1:3000/v1"

# 对于需要特殊配置的CLI工具，请查阅具体文档
```

## 4. Veloera 部署和配置

### 4.1 Docker 部署
```yaml
# docker-compose.yml
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

### 4.2 环境变量配置
```bash
# .env
# 基础配置
DEFAULT_API_KEY=sk-your-default-key
REQUEST_TIMEOUT=600
STREAMING_TIMEOUT=100

# Claude 相关配置
ANTHROPIC_API_KEY=sk-your-anthropic-key

# 其他 Provider 配置
OPENAI_API_KEY=sk-your-openai-key
AZURE_API_KEY=sk-your-azure-key
GEMINI_API_KEY=sk-your-gemini-key
```

## 5. Claude Code 集成测试

### 5.1 连接测试脚本
```bash
#!/usr/bin/env bash
# test-veloera-claude.sh

echo "🧪 测试 Veloera + Claude Code 集成..."

# 配置环境变量
export ANTHROPIC_API_KEY="sk-your-veloera-api-key"
export ANTHROPIC_BASE_URL="http://172.17.0.1:3000/v1"

# 测试连接
response=$(curl -s "http://172.17.0.1:3000/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -d '{
    "model": "claude-3-5-sonnet-20241022",
    "max_tokens": 10,
    "messages": [{"role": "user", "content": "Hello"}]
  }')

if echo "$response" | grep -q "content"; then
  echo "✅ Veloera Claude Messages API 连接成功"
else
  echo "❌ Veloera Claude Messages API 连接失败"
  echo "响应: $response"
  exit 1
fi

# 测试 Claude Code
echo "🤖 测试 Claude Code 集成..."
if command -v claude >/dev/null; then
  echo "Claude Code 已安装"
  claude --version
else
  echo "⚠️  Claude Code 未安装，请先安装"
fi
```

### 5.2 模型可用性检查
```bash
#!/usr/bin/env bash
# check-models.sh

echo "📋 检查可用模型..."

# 获取模型列表
models=$(curl -s "http://172.17.0.1:3000/v1/models" \
  -H "x-api-key: sk-your-veloera-api-key" \
  | jq -r '.data[].id' 2>/dev/null)

if [[ -n "$models" ]]; then
  echo "✅ 可用模型:"
  echo "$models" | while read -r model; do
    echo "  - $model"
  done
else
  echo "❌ 无法获取模型列表"
fi
```

## 6. 直接CLI工具配置

### 6.1 Claude Code 环境变量配置
```bash
# 通过环境变量直接配置Claude Code
export ANTHROPIC_API_KEY="your-veloera-token"
export ANTHROPIC_BASE_URL="http://172.17.0.1:3000/v1"
export ANTHROPIC_MODEL="claude-3-5-sonnet-20241022"

# 直接使用Claude Code
claude "写一个React组件"
```

### 6.2 Mise 环境配置
```toml
# .mise.toml
[env]
# Veloera 配置
VELOERA_TOKEN = "sk-your-veloera-api-key"
VELOERA_URL = "http://172.17.0.1:3000/v1"

# Claude Code 配置 (Anthropic 格式)
ANTHROPIC_API_KEY = "{{ env.VELOERA_TOKEN }}"
ANTHROPIC_BASE_URL = "{{ env.VELOERA_URL }}"
```

## 7. 使用示例

### 7.1 Web 开发项目
```bash
# 进入项目目录
cd ~/projects/my-react-app

# 自动切换配置 (如果配置了自动检测)
echo "AI configuration loaded"

# 使用 Claude Code
claude "帮我创建一个用户登录组件，包含表单验证"
```

### 7.2 内容创作项目
```bash
# 进入内容创作项目目录
cd ~/projects/content-creation

# 使用 Claude Code 进行内容创作
claude "基于这个技术文档，写一篇技术博客文章"
```

## 8. 故障排查

### 8.1 常见问题
```bash
# 检查 Veloera 服务状态
docker ps | grep veloera
curl -s "http://172.17.0.1:3000/v1/models"

# 检查配置
mise env | grep -E "(VELOERA|ANTHROPIC)"

# 测试连接
echo "Testing AI connection..."
```

### 8.2 调试模式
```bash
# 启用详细日志
export ANTHROPIC_LOG_LEVEL=debug

# 测试 API 调用
curl -v "http://172.17.0.1:3000/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: $VELOERA_TOKEN" \
  -d '{"model": "claude-3-5-sonnet-20241022", "max_tokens": 10, "messages": [{"role": "user", "content": "Test"}]}'
```

## 9. 性能优化

### 9.1 缓存配置
```bash
# 启用 Veloera 缓存
export CACHE_ENABLED=true
export CACHE_TTL=3600
export CACHE_RATIO=0.5
```

### 9.2 并发控制
```bash
# 并发请求限制
export CONCURRENT_LIMIT=10
export RATE_LIMIT=60
```

## 10. 总结

✅ **Veloera 完全支持 Claude Code 直接集成方案**

### 关键优势：
1. **原生 Anthropic 格式支持**：无需格式转换
2. **多 Provider 统一管理**：BigGLM、OpenAI、Gemini等
3. **Claude Code 直接兼容**：配置简单
4. **高性能和可扩展性**：企业级 API Gateway

### 推荐架构：
```
Claude Code (工具) + Veloera (服务) + Mise (环境管理)
```

这个方案完美解决了需求：**通过环境变量实现项目级配置**，同时享受 Veloera 的统一 API 管理能力。