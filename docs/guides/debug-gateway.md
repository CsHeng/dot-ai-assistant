# Gateway 调试指南

## 概述

本指南适用于调试所有主流 AI Gateway 的集成问题：
- **New-API** (QuantumNous/new-api)
- **One-API** (songquanpeng/one-api)
- **Veloera** (当前项目使用)
- **LiteLLM** (BerriAI/litellm)
- **Portkey** Gateway

所有这些 Gateway 都支持相似的消息格式，本调试指南适用于所有工具。

## 1. 通用调试脚本

### 基础连接测试

```bash
#!/usr/bin/env bash
# debug-gateway.sh

echo "🧪 调试 AI Gateway 集成..."

# 配置你的 Gateway 地址和 API Key
GATEWAY_URL="http://172.17.0.1:3000"  # 根据你的部署修改
API_KEY="sk-your-gateway-api-key"     # 替换为你的 API Key

echo "=== 测试1: OpenAI 格式请求 ==="
curl -v "${GATEWAY_URL}/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${API_KEY}" \
  -d '{
    "model": "glm-4.6",
    "messages": [{"role": "user", "content": "Hello, test"}],
    "max_tokens": 50,
    "stream": false
  }' \
  --max-time 30

echo -e "\n\n=== 测试2: Anthropic 格式请求 ==="
curl -v "${GATEWAY_URL}/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: ${API_KEY}" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5-20250929",
    "messages": [{"role": "user", "content": "Hello, test"}],
    "max_tokens": 50
  }' \
  --max-time 30

echo -e "\n\n=== 测试3: 检查可用模型 ==="
curl -s "${GATEWAY_URL}/v1/models" \
  -H "Authorization: Bearer ${API_KEY}" | jq '.'

echo -e "\n\n=== 测试4: Gemini 格式请求 ==="
curl -v "${GATEWAY_URL}/v1/models/gemini-pro:generateContent" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${API_KEY}" \
  -d '{
    "contents": [{"parts": [{"text": "Hello, test"}]}],
    "generationConfig": {"maxOutputTokens": 50}
  }' \
  --max-time 30
```

### Gateway 特定调试

#### New-API / One-API 调试

```bash
# New-API 特定测试
GATEWAY_URL="http://localhost:3000"

echo "=== New-API 特定测试 ==="
# 检查 New-API 状态
curl -s "${GATEWAY_URL}/api/status" | jq '.'

# 检查渠道状态
curl -s "${GATEWAY_URL}/api/channel" \
  -H "Authorization: Bearer ${API_KEY}" | jq '.'
```

#### Veloera 调试

```bash
# Veloera 特定测试
echo "=== Veloera 特定测试 ==="
# 检查 Veloera 日志
docker logs veloera --tail 20

# 检查 Veloera 状态（如果支持）
curl -s "${GATEWAY_URL}/api/status" | jq '.' 2>/dev/null || echo "Veloera 状态 API 不可用"
```

#### LiteLLM 调试

```bash
# LiteLLM 特定测试
echo "=== LiteLLM 特定测试 ==="
# LiteLLM 健康检查
curl -s "${GATEWAY_URL}/health" | jq '.' 2>/dev/null || echo "健康检查端点不可用"

# LiteLLM 模型列表
curl -s "${GATEWAY_URL}/v1/models" \
  -H "Authorization: Bearer ${API_KEY}" | jq '.data[] | select(.id | contains("gemini"))'
```

## 2. CLI 工具调试

### 通用环境配置

```toml
# .mise.toml (通用调试配置)
[env]
# Gateway 配置
OPENAI_BASE_URL = "http://172.17.0.1:3000/v1"
OPENAI_API_KEY = "sk-your-gateway-api-key"
OPENAI_MODEL = "glm-4.6"

# Claude Code 配置
ANTHROPIC_BASE_URL = "http://172.17.0.1:3000/v1"
ANTHROPIC_AUTH_TOKEN = "sk-your-gateway-api-key"

# 通用设置
API_TIMEOUT_MS = "6000000"  # 10分钟超时
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"
```

### CLI 工具测试

```bash
# 测试 Qwen CLI
echo "🤖 测试 Qwen CLI..."
mise exec -- qwen "你好，测试连接"

# 测试 Claude Code CLI
echo "🤖 测试 Claude Code CLI..."
mise exec -- claude "Hello, this is a test message"

# 测试其他工具（如果安装）
echo "🤖 测试其他 CLI 工具..."
# Factory AI Droid
mise exec -- droid "Hello, test" 2>/dev/null || echo "Droid CLI 未安装"
```

## 3. 常见问题排查

### 问题 1: 连接超时

```bash
# 解决方案 A: 增加超时时间
export API_TIMEOUT_MS="6000000"  # 10分钟

# 解决方案 B: 检查网络连接
ping -c 3 172.17.0.1

# 解决方案 C: 检查端口
telnet 172.17.0.1 3000
```

### 问题 2: 模型不可用

```bash
# 检查模型列表
curl -s "${GATEWAY_URL}/v1/models" \
  -H "Authorization: Bearer ${API_KEY}" | jq '.data[]'

# 检查特定模型配置
curl -s "${GATEWAY_URL}/v1/models" \
  -H "Authorization: Bearer ${API_KEY}" | jq '.data[] | select(.id | contains("glm"))'
```

### 问题 3: 认证失败

```bash
# 验证 API Key
curl -v "${GATEWAY_URL}/v1/models" \
  -H "Authorization: Bearer ${API_KEY}" 2>&1 | grep -E "(401|403)"

# 检查 Token 格式
echo "API Key 长度: ${#API_KEY}"
echo "API Key 前缀: ${API_KEY:0:10}"
```

### 问题 4: 格式兼容性

```bash
# 测试不同格式
echo "=== 测试 OpenAI 格式 ==="
curl "${GATEWAY_URL}/v1/chat/completions" \
  -H "Authorization: Bearer ${API_KEY}" \
  -d '{"model":"glm-4.6","messages":[{"role":"user","content":"test"}]}'

echo "=== 测试 Anthropic 格式 ==="
curl "${GATEWAY_URL}/v1/messages" \
  -H "x-api-key: ${API_KEY}" \
  -d '{"model":"claude-sonnet-4-5-20250929","messages":[{"role":"user","content":"test"}]}'
```

## 4. Gateway 特定配置

### New-API 配置示例

```yaml
# docker-compose.yml for New-API
version: '3.8'
services:
  new-api:
    image: calciumion/new-api:latest
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - SQL_DSN=sqlite:///data/new-api.db
      - REDIS_CONN_STRING=
      - SESSION_SECRET_KEY=your-secret-key
      - TZ=Asia/Shanghai
    volumes:
      - ./data:/data
      - ./logs:/logs
```

### One-API 配置示例

```yaml
# docker-compose.yml for One-API
version: '3.8'
services:
  one-api:
    image: justsong/one-api:latest
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - SQL_DSN=sqlite:///data/one-api.db
      - REDIS_CONN_STRING=
      - SESSION_SECRET_KEY=your-secret-key
      - TZ=Asia/Shanghai
    volumes:
      - ./data:/data
      - ./logs:/logs
```

### Veloera 配置示例

```yaml
# docker-compose.yml for Veloera
version: '3.8'
services:
  veloera:
    restart: unless-stopped
    image: ghcr.io/veloera/veloera:latest
    container_name: veloera
    ports:
      - "3000:3000"
    volumes:
      - ./data:/data
    environment:
      - TZ=Asia/Shanghai
```

### LiteLLM 配置示例

```yaml
# docker-compose.yml for LiteLLM
version: '3.8'
services:
  litellm:
    image: ghcr.io/berriai/litellm:main
    restart: unless-stopped
    ports:
      - "4000:4000"
    environment:
      - DATABASE_URL=sqlite:///data/litellm.db
    volumes:
      - ./data:/data
      - ./config.yaml:/app/config.yaml
    command: ["--config", "/app/config.yaml", "--port", "4000"]
```

## 5. 性能测试

```bash
# 简单性能测试
echo "=== 性能测试 ==="
time curl -s "${GATEWAY_URL}/v1/chat/completions" \
  -H "Authorization: Bearer ${API_KEY}" \
  -d '{"model":"glm-4.6","messages":[{"role":"user","content":"简单测试"}],"max_tokens":50}'

# 并发测试
echo "=== 并发测试 ==="
for i in {1..5}; do
  curl -s "${GATEWAY_URL}/v1/chat/completions" \
    -H "Authorization: Bearer ${API_KEY}" \
    -d "{\"model\":\"glm-4.6\",\"messages\":[{\"role\":\"user\",\"content\":\"测试 $i\"}],\"max_tokens\":10}" &
done
wait
```

## 6. 日志分析

### 查看 Gateway 日志

```bash
# Docker 容器日志
docker logs [container-name] --tail 50

# 实时日志
docker logs -f [container-name]

# 过滤错误日志
docker logs [container-name] 2>&1 | grep -i error

# 过滤特定时间段
docker logs --since="2024-01-01T00:00:00" --until="2024-01-01T23:59:59" [container-name]
```

### 分析常见错误

```bash
# 查找超时错误
docker logs [container-name] 2>&1 | grep -i timeout

# 查找连接错误
docker logs [container-name] 2>&1 | grep -i "connection\|network"

# 查找认证错误
docker logs [container-name] 2>&1 | grep -i "401\|403\|unauthorized"
```

## 7. 快速诊断清单

- [ ] Gateway 服务运行正常？
- [ ] 网络连接可达？(`ping` 和 `telnet`)
- [ ] API Key 有效且格式正确？
- [ ] 模型名称正确且可用？
- [ ] 端口配置正确？
- [ ] 防火墙设置允许连接？
- [ ] CLI 工具环境变量配置正确？
- [ ] 超时设置合理？
- [ ] 日志中没有错误信息？

## 8. 获取帮助

如果问题仍然存在：

1. **收集信息**:
   ```bash
   # 系统信息
   uname -a
   docker --version

   # Gateway 状态
   docker ps
   docker logs [container-name] --tail 100

   # 网络测试
   curl -v "${GATEWAY_URL}/v1/models" -H "Authorization: Bearer ${API_KEY}"
   ```

2. **检查文档**:
   - Gateway 官方文档
   - CLI 工具文档
   - 项目 README

3. **社区支持**:
   - GitHub Issues
   - Discord/Slack 社区
   - Stack Overflow

---

这个调试指南适用于所有支持 OpenAI 兼容格式的 AI Gateway。根据你使用的具体 Gateway，选择相应的配置和调试方法。