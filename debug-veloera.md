# Veloera调试脚本

## 1. 直接测试Veloera + BigGLM

```bash
#!/usr/bin/env bash
# debug-veloera.sh

echo "🧪 调试Veloera + BigGLM集成..."

# 测试1：直接用OpenAI格式测试BigGLM
echo "=== 测试1: OpenAI格式直接请求 ==="
curl -v "http://172.17.0.1:3000/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-veloera-api-key" \
  -d '{
    "model": "glm-4-6",
    "messages": [{"role": "user", "content": "Hello, test"}],
    "max_tokens": 50,
    "stream": false
  }' \
  --max-time 30

echo -e "\n\n=== 测试2: Anthropic格式请求 ==="
# 测试2：用Anthropic格式
curl -v "http://172.17.0.1:3000/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: sk-your-veloera-api-key" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5-20250929",
    "messages": [{"role": "user", "content": "Hello, test"}],
    "max_tokens": 50
  }' \
  --max-time 30

echo -e "\n\n=== 测试3: 检查Veloera日志 ==="
echo "查看Veloera容器日志:"
docker logs veloera --tail 20

echo -e "\n\n=== 测试4: 检查模型可用性 ==="
curl -s "http://172.17.0.1:3000/v1/models" \
  -H "Authorization: Bearer sk-your-veloera-api-key" | jq '.'
```

## 2. Mise环境配置

```toml
# .mise.toml (调试版本)
[env]
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://172.17.0.1:3000/v1"
API_TIMEOUT_MS = "6000000"  # 10分钟超时
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"
```

## 3. 调试脚本

可以直接运行以下命令进行调试：

```bash
# 测试OpenAI格式
echo "🧪 测试OpenAI格式..."
curl -v "http://172.17.0.1:3000/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-veloera-api-key" \
  -d '{
    "model": "glm-4-6",
    "messages": [{"role": "user", "content": "Hello, test"}],
    "max_tokens": 50,
    "stream": false
  }' \
  --max-time 30

# 测试Anthropic格式
echo "🧪 测试Anthropic格式..."
curl -v "http://172.17.0.1:3000/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: sk-your-veloera-api-key" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "glm-4.6",
    "messages": [{"role": "user", "content": "Hello, test"}],
    "max_tokens": 50
  }' \
  --max-time 30

# 检查Veloera日志
echo "📋 Veloera最新日志:"
docker logs veloera --tail 20

# 检查可用模型
echo "🤖 可用模型列表:"
curl -s "http://172.17.0.1:3000/v1/models" \
  -H "Authorization: Bearer sk-your-veloera-api-key" | jq '.'

# 测试Claude Code
echo "🤖 测试Claude Code..."
echo "如果卡住，按Ctrl+C停止"
echo "然后在另一个终端运行: docker logs veloera --tail 20"
claude "Hello, this is a test message"
```

## 3. 可能的解决方案

### 方案A：增加超时和禁用流式
```toml
[env]
API_TIMEOUT_MS = "6000000"  # 10分钟
CLAUDE_CODE_DISABLE_STREAMING = "1"  # 如果存在这个变量
```

### 方案B：检查Veloera配置
确保Veloera的BigGLM配置正确：
- API Key有效
- 模型名称正确
- endpoint URL正确

### 方案C：尝试不同的流式设置
```bash
# 测试非流式请求
curl "http://10.1.1.11:3000/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: sk-token" \
  -d '{
    "model": "claude-sonnet-4-5-20250929",
    "messages": [{"role": "user", "content": "Hello"}],
    "max_tokens": 50,
    "stream": false
  }'
```