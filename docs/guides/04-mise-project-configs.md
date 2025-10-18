# Mise项目配置模板

## 📋 标准配置模板

Since CCR runs as a single instance with default configuration, these templates focus on environment management and task organization rather than configuration switching.

### 1. 开发项目 (.mise.toml)

```toml
[env]
# Claude Code + Veloera配置
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"  # 仅服务器地址，不包含路径
API_TIMEOUT_MS = "3000000"
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"

# 注意：
# - BASE_URL 只包含 Veloera 服务器地址
# - Claude Code 会自动追加 /v1/messages 路径
# - 完整请求：http://192.168.8.100:3000/v1/messages
```

### 2. 内容创作项目 (.mise.toml)

```toml
[env]
# Claude Code + Veloera配置
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"  # 仅服务器地址，不包含路径
API_TIMEOUT_MS = "3000000"
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"

# 注意：
# - BASE_URL 只包含 Veloera 服务器地址
# - Claude Code 会自动追加 /v1/messages 路径
# - 完整请求：http://192.168.8.100:3000/v1/messages

# 内容创作特定配置
AI_TEMPERATURE = "0.7"
AI_MAX_TOKENS = "4096"
```

### 3. 数据分析项目 (.mise.toml)

```toml
[env]
# Claude Code + Veloera配置
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"
API_TIMEOUT_MS = "6000000"  # 更长超时
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"

# 数据分析特定配置
AI_MAX_TOKENS = "16384"
```

## 🚀 快速初始化脚本

### 创建项目脚本
```bash
#!/usr/bin/env bash
# ~/.local/bin/init-ai-project

PROJECT_TYPE="$1"
PROJECT_NAME="$2"

if [[ -z "$PROJECT_TYPE" ]]; then
  echo "用法: init-ai-project <project-type> [project-name]"
  echo "项目类型: web, content, data"
  exit 1
fi

case "$PROJECT_TYPE" in
  "web")
    cat > .mise.toml << 'EOF'
[env]
# Claude Code + Veloera配置
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"
API_TIMEOUT_MS = "3000000"
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"
EOF
    echo "✅ 已创建开发项目配置"
    ;;

  "content")
    cat > .mise.toml << 'EOF'
[env]
# Claude Code + Veloera配置
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"
API_TIMEOUT_MS = "3000000"
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"
AI_TEMPERATURE = "0.7"
EOF
    echo "✅ 已创建内容创作项目配置"
    ;;

  "data")
    cat > .mise.toml << 'EOF'
[env]
# Claude Code + Veloera配置
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"
API_TIMEOUT_MS = "6000000"
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"
AI_MAX_TOKENS = "16384"
EOF
    echo "✅ 已创建数据分析项目配置"
    ;;

  *)
    echo "❌ 不支持的项目类型: $PROJECT_TYPE"
    echo "支持的类型: web, content, data"
    exit 1
    ;;
esac

# 激活环境
mise env

# 测试连接
echo "🧪 测试AI连接..."
echo "⚠️ 请先配置正确的API Token"

echo "🎉 项目初始化完成！"
echo "现在可以运行: claude \"开始工作\""
```

## 📖 使用示例

### 创建新项目
```bash
# 创建开发项目
mkdir ~/projects/my-web-app
cd ~/projects/my-web-app
init-ai-project web

# 创建内容创作项目
mkdir ~/projects/my-blog
cd ~/projects/my-blog
init-ai-project content

# 创建数据分析项目
mkdir ~/projects/my-analysis
cd ~/projects/my-analysis
init-ai-project data
```

### 项目切换
```bash
# 项目A：开发 (glm-4-6)
cd ~/projects/my-web-app
mise env
claude "帮我实现一个用户登录组件"

# 项目B：内容创作 (glm-4.5-air)
cd ~/projects/my-blog
mise env
claude "写一篇关于前端性能优化的技术文章"

# 项目C：数据分析 (glm-4-6)
cd ~/projects/my-analysis
mise env
claude "分析这个销售数据，给出趋势预测"
```

## 🔧 自定义配置

### 添加自定义模型映射
```toml
[env]

# 可用的模型映射：
# claude-sonnet-4-5-20250929 → glm-4-6
# claude-3-5-haiku-20241022 → glm-4.5-air
# claude-opus-4-20250514 → glm-4-6
```

## 🎯 最佳实践

1. **统一配置管理**：所有项目使用相同格式的.mise.toml
2. **模型选择策略**：根据项目复杂度选择合适的模型
3. **环境隔离**：每个项目独立的配置，互不干扰
4. **API Token安全**：使用环境变量，避免硬编码

这个配置系统让你可以在不同项目间无缝切换，每个项目使用最适合的AI模型和配置！