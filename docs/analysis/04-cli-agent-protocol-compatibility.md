# CLI Agent 协议兼容性分析

## 🎯 核心结论

各CLI Agent对第三方API和协议的支持程度不同，需要根据兼容性选择合适的工具组合。

## 📋 CLI Agent 协议支持矩阵

| CLI Agent | Anthropic协议 | OpenAI协议 | Gemini协议 | 第三方API支持 | 自定义端点 |
|-----------|---------------|------------|------------|---------------|------------|
| **Claude Code CLI** | ✅ 原生支持 | ✅ 兼容 | ❌ | ✅ | ✅ |
| **Qwen CLI** | ❌ | ✅ 原生支持 | ❌ | ✅ | ✅ |
| **Gemini CLI** | ❌ | ❌ | ✅ 原生支持 | ❌ | ❌ |
| **OpenAI Codex CLI** | ❌ | ✅ 原生支持 | ❌ | ❌ | ❌ |

## 🔧 详细配置方式

### Claude Code CLI (Anthropic协议 + OpenAI兼容)
```bash
# 环境变量配置
export ANTHROPIC_AUTH_TOKEN="sk-your-api-key"
export ANTHROPIC_BASE_URL="http://localhost:3000"
export ANTHROPIC_MODEL="claude-3-5-sonnet-20241022"

# 使用示例
claude "写一个React组件"
```

### Qwen CLI (OpenAI协议)
```bash
# 环境变量配置
export OPENAI_API_KEY="sk-your-api-key"
export OPENAI_BASE_URL="http://localhost:3000/v1"
export OPENAI_MODEL="glm-4.6"  # 必须指定模型

# 或命令行参数
qwen --openai-api-key "sk-your-key" --openai-base-url "http://localhost:3000/v1" -m "glm-4.6" "写一个React组件"

# 或临时环境变量
OPENAI_MODEL="glm-4.6" qwen "写一个React组件"
```

### Gemini CLI (仅官方Gemini协议)
```bash
# 仅支持API Key，无自定义端点
export GOOGLE_AI_API_KEY="your-gemini-key"

# 使用示例
gemini "写一个React组件"
```

### OpenAI Codex CLI (仅官方OpenAI协议)
```bash
# 仅支持官方登录，无环境变量配置
codex login
codex "写一个React组件"
```

## 🏗️ 架构兼容性分析

### Veloera Gateway 支持的协议格式

```bash
# Veloera支持的端点格式
/v1/messages          # Anthropic Claude Messages API
/v1/chat/completions  # OpenAI Chat Completions API
/generate             # Gemini Generate API (如果支持)
```

### 协议转换能力

| Provider协议 | Veloera转换 | Claude Code CLI | Qwen CLI | Gemini CLI | Codex CLI |
|-------------|-------------|-----------------|----------|------------|-----------|
| **Anthropic** | 原生支持 | ✅ 直接使用 | ❌ 需转换 | ❌ 需转换 | ❌ 需转换 |
| **OpenAI** | 原生支持 | ✅ 兼容格式 | ✅ 直接使用 | ❌ 需转换 | ✅ 直接使用 |
| **Gemini** | 需转换 | ❌ 需转换 | ❌ 需转换 | ✅ 直接使用 | ❌ 需转换 |

## 🎯 实施建议

### 推荐的工具组合

#### 1. 多Provider统一架构 (推荐)
```toml
# .mise.toml - 支持第三方API的工具
[env]
# Claude Code CLI配置 (Anthropic协议)
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-key"
ANTHROPIC_BASE_URL = "http://veloera:3000"

# Qwen CLI配置 (OpenAI协议)
OPENAI_API_KEY = "sk-your-veloera-key"
OPENAI_BASE_URL = "http://veloera:3000/v1"
OPENAI_MODEL = "glm-4.6"  # 必须指定模型
```

#### 2. 混合使用架构
- **主要工具**: Claude Code CLI + Qwen CLI (通过Veloera)
- **补充工具**: Gemini CLI + Codex CLI (直接使用官方API)

### 环境变量管理最佳实践

#### 支持第三方API的工具
```bash
# 可以通过Mise等工具统一管理
export ANTHROPIC_AUTH_TOKEN="sk-unified-key"
export ANTHROPIC_BASE_URL="http://veloera:3000"
export OPENAI_API_KEY="sk-unified-key"
export OPENAI_BASE_URL="http://veloera:3000/v1"
export OPENAI_MODEL="glm-4.6"  # Qwen CLI必须指定模型
```

#### 仅支持官方API的工具
```bash
# 需要独立配置和管理
export GOOGLE_AI_API_KEY="gemini-official-key"
# Codex CLI需要单独登录: codex login
```

## ✅ 正确的使用示例

### 支持Veloera的CLI工具使用流程
```bash
# 1. 进入开发项目
cd ~/projects/my-web-app

# 2. 使用Claude Code CLI (支持Veloera)
claude "写一个React登录组件，包含表单验证"

# 3. 使用Qwen CLI (支持Veloera)
qwen --openai-api-key "sk-your-key" --openai-base-url "http://10.1.1.11:3000/v1" "写一个React登录组件"

# 4. 切换到内容创作项目
cd ~/projects/my-blog

# 5. 自动使用配置的模型
claude "写一篇关于React最佳实践的文章"
qwen "写一篇关于React最佳实践的文章"
```

### Codex CLI 独立使用 (仅官方API)
```bash
# 需要单独登录OpenAI
codex login

# 直接使用官方API
codex "写一个React登录组件，包含表单验证"
```

## ❌ 限制说明

### 不支持的功能
1. **无第三方API**: Codex CLI无法连接到Veloera或其他第三方Provider
2. **无自定义端点**: 只能使用OpenAI官方API端点
3. **无统一配置**: 无法通过Mise环境变量配置Codex CLI
4. **无模型选择**: 只能使用OpenAI提供的模型

### 建议的架构选择
1. **主推Claude Code CLI**: 支持完整的Mise + Veloera方案
2. **备选Qwen CLI**: 作为OpenAI兼容格式的补充工具
3. **独立使用Codex**: 仅在需要OpenAI官方特性时使用

## 📝 最终建议

**OpenAI Codex CLI 不适合集成到 Mise + Veloera 架构中**。建议：

1. **主要使用Claude Code CLI**: 完全支持Veloera多Provider架构
2. **辅助使用Qwen CLI**: 提供OpenAI兼容格式的选择
3. **独立使用Codex CLI**: 仅在特定需要OpenAI官方功能时使用

**结论**: Codex CLI由于仅支持官方登录认证，无法与Veloera配合使用，不推荐在统一架构中使用。