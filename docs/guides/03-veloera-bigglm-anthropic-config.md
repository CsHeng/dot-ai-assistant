# Veloera + BigGLM Anthropic协议配置指南

## ⭐ 关键发现

**Veloera可以通过Anthropic协议直接接入BigGLM的Anthropic端点，并通过模型映射实现完美兼容！**

## 🔧 配置步骤

### 1. 重要：URL配置原理

**客户端配置 vs 实际请求路径：**

- **客户端配置**: `ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"`
  - 只包含Veloera服务器地址，不包含任何路径
  - 这是Claude Code配置中的BASE_URL

- **实际请求**: `GET/POST http://192.168.8.100:3000/v1/messages`
  - Claude Code自动追加Anthropic API路径：`/v1/messages`
  - Veloera接收到完整请求后进行模型映射和转发

### 2. Veloera Provider配置

在Veloera中添加BigGLM Coding Plan OpenAI/Anthropic 渠道配置

### 3. 关键：模型映射配置

针对Claude Code发起的请求，在Veloera Anthropic类型的渠道中配置以下模型映射：

```json
{
  "claude-sonnet-4-5-20250929": "glm-4-6",
  "claude-3-5-haiku-20241022": "glm-4.5-air",
  "claude-opus-4-20250514": "glm-4.6"
}
```

### 3. Mise配置文件

```toml
# .mise.toml
[env]
# Claude Code配置 - 使用Veloera Anthropic端点
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"  # Veloera地址，不包含路径
#
# 重要说明：
# - 客户端配置中的 BASE_URL 不包含任何路径
# - Claude Code 请求时会自动追加路径：/v1/messages
# - 完整请求地址：http://192.168.8.100:3000/v1/messages
API_TIMEOUT_MS = "3000000"
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"
```

## 🎯 模型映射表

| Claude模型名 | 映射到GLM模型 | 用途 |
|-------------|--------------|------|
| `claude-sonnet-4-5-20250929` | `glm-4-6` | 主要开发任务 |
| `claude-3-5-haiku-20241022` | `glm-4.5-air` | 轻量级任务 |
| `claude-opus-4-20250514` | `glm-4.6` | 复杂推理任务 |

## 🚀 使用方法

### 不同项目的配置示例

#### 开发项目
```toml
[env]
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"
```

#### 内容创作项目
```toml
[env]
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"
```

#### 复杂分析项目
```toml
[env]
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"
```

## ✅ 验证步骤

```bash
# 1. 创建测试项目
mkdir ~/projects/bigglm-test
cd ~/projects/bigglm-test

# 2. 创建.mise.toml配置文件

# 3. 激活环境
mise env

# 4. 测试连接
echo "Testing AI connection..."

# 5. 在不同目录测试不同模型
# 项目A: 使用claude-sonnet-4-5-20250929
# 项目B: 使用claude-3-5-haiku-20241022
```

## 🔍 故障排查

### 常见问题

1. **503错误**：模型映射配置不正确
2. **无响应**：使用了错误的协议端点
3. **认证失败**：API Key配置错误

### 调试命令

```bash
# 检查Veloera日志
docker logs veloera --tail 20

# 测试直接API调用
curl -v "http://192.168.8.100:3000/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: sk-your-veloera-api-key" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5-20250929",
    "messages": [{"role": "user", "content": "Hello"}],
    "max_tokens": 50
  }'
```

## 🎉 优势

1. **完美兼容**：Claude Code无需修改，原生支持
2. **模型映射**：透明的模型名称转换
3. **项目隔离**：每个项目可以使用不同的"虚拟Claude模型"
4. **统一管理**：所有配置通过mise管理
5. **成本控制**：可以选择不同的GLM模型平衡性能和成本

## 📝 总结

这个配置方案完美解决了：
- ✅ Claude Code + BigGLM集成
- ✅ 多项目配置切换
- ✅ 模型名称映射
- ✅ Anthropic格式支持

**关键**：使用Veloera的Anthropic协议 + 模型映射功能！