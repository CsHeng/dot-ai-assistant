# Claude Code + Mise + Veloera 完整解决方案

## 🎯 最终选型

**主要架构**: Claude Code CLI + Mise + Veloera
**备选方案**: Claude Code + CCR + Veloera (需要多配置文件切换)

## 核心架构图

```
┌─────────────────────────────────────────────────────────────┐
│                    Veloera (Provider汇集层)                │
│  ┌─────────────────┐  ┌─────────────────┐  ┌──────────────┐ │
│  │  BigGLM API    │  │ BigGLM Coding  │  │ DeepSeek API │ │
│  │  (glm-4.6)     │  │  (计费计划)     │  │ (deepseek)   │ │
│  └─────────────────┘  └─────────────────┘  └──────────────┘ │
│  ┌─────────────────┐  ┌─────────────────┐                   │
│  │   Kimi API      │  │   其他Provider  │                   │
│  │  (moonshot)     │  │                 │                   │
│  └─────────────────┘  └─────────────────┘                   │
└─────────────────────────────────────────────────────────────┘
                              │ 统一OpenAI兼容API
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Mise (环境管理层)                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐   │
│  │  开发项目 │  │  内容创作项目│  │   数据分析项目      │   │
│  │ glm-4.6     │  │  glm-4.5     │  │     glm-4.6        │   │
│  └─────────────┘  └─────────────┘  └─────────────────────┘   │
│  ┌─────────────┐  ┌─────────────┐                           │
│  │  经济模式项目│  │  其他自定义项目│                         │
│  │  kimi       │  │   自定义配置   │                         │
│  └─────────────┘  └─────────────┘                           │
└─────────────────────────────────────────────────────────────┘
                              │ 环境变量切换
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   Claude Code CLI (工具层)                  │
│                                                           │
│  claude "写一个React组件"                                   │
│  claude "分析这个数据集"                                     │
│  claude "写一篇技术文档"                                     │
│                                                           │
│  通过环境变量自动使用项目的AI配置                             │
└─────────────────────────────────────────────────────────────┘
```

## 三层架构详解

### 1. Provider汇集层 (Veloera)
- **职责**: 智能API转换和路由枢纽
- **功能**:
  - 接入多种Provider (BigGLM、DeepSeek、Kimi等)
  - API格式转换 (将不同Provider格式转换为标准OpenAI/Anthropic兼容格式)
  - 智能路由 (根据模型名称自动选择对应Provider)
  - API Key集中管理
  - BigGLM coding计费计划支持
  - 自动故障转移
  - 使用统计和成本管理
- **核心价值**: 对CLI工具透明，屏蔽底层Provider复杂性
- **配置**: Docker部署，环境变量配置，Web界面管理

### 2. 项目管理层 (Mise)
- **职责**: 根据项目目录自动切换AI配置
- **功能**:
  - 项目级环境隔离
  - 自动配置切换
  - 模型偏好管理
  - 环境配置工具
- **配置**: .mise.toml 项目配置文件

### 3. 工具层 (Claude Code CLI)
- **职责**: 主要开发工具，用户交互界面
- **功能**:
  - 通过环境变量获取AI配置
  - 自动使用项目的模型偏好
  - 无缝的项目体验
- **配置**: 通过Mise环境变量自动配置

## 核心优势

### ✅ 解决的核心问题

1. **Provider统一汇集**: Veloera统一管理BigGLM、DeepSeek、Kimi等
2. **API Key集中管理**: 一处配置，处处使用，更换key只需要改一个地方
3. **项目级自动切换**: Mise根据不同项目目录自动切换AI配置
4. **模型偏好管理**: 不同项目使用不同的模型 (glm-4.6, glm-4.5, kimi等)
5. **计费计划支持**: Veloera完美支持BigGLM coding等特殊计费方式
6. **使用透明化**: Web界面查看使用统计和成本

### ✅ 技术优势

- **极简工作流**: Claude Code CLI + 环境变量，无需额外工具
- **项目级隔离**: 不同项目完全隔离的AI配置
- **自动切换**: 进入项目目录自动应用项目配置
- **故障转移**: Veloera自动处理Provider故障
- **扩展性强**: 轻松添加新的Provider和项目类型

## 实施步骤

### 阶段1: Veloera部署 (20分钟)

```bash
# 1. 创建docker-compose.yml
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

# 2. 创建环境变量文件
# .env (根据Veloera文档配置)

# 3. 启动服务
docker-compose up -d

# 4. 配置Provider
# 通过Web界面配置: BigGLM, DeepSeek, Kimi等
```

### 阶段2: Mise全局配置 (10分钟)

```bash
# 1. 安装Mise
curl https://mise.run | sh
echo 'eval "$(mise activate bash)"' >> ~/.bashrc
source ~/.bashrc

# 2. 全局配置 ~/.config/mise/config.toml
[env]
# Veloera全局配置
VELOERA_URL = "http://172.17.0.1:3000/v1"
VELOERA_TOKEN = "sk-your-api-key"

# 默认配置
DEFAULT_PROVIDER = "bigglm"
DEFAULT_MODEL = "glm-4.6"

# Claude Code CLI配置
ANTHROPIC_API_KEY = "{{ env.VELOERA_TOKEN }}"
ANTHROPIC_BASE_URL = "{{ env.VELOERA_URL }}"
```

### 阶段3: 项目级配置 (15分钟)

#### 开发项目配置

```toml
# ~/projects/my-web-app/.mise.toml
[env]
# Claude Code CLI配置
ANTHROPIC_API_KEY = "sk-your-api-key"
ANTHROPIC_BASE_URL = "http://172.17.0.1:3000/v1"
CLAUDE_MODEL = "glm-4.6"

# OpenAI Codex CLI配置 (完全兼容)
OPENAI_API_KEY = "sk-your-api-key"
OPENAI_BASE_URL = "http://172.17.0.1:3000/v1"
OPENAI_MODEL = "glm-4.6"
```

#### 内容创作项目配置

```toml
# ~/projects/my-blog/.mise.toml
[env]
# Claude Code CLI配置
ANTHROPIC_API_KEY = "sk-your-api-key"
ANTHROPIC_BASE_URL = "http://172.17.0.1:3000/v1"
CLAUDE_MODEL = "glm-4.5"

# OpenAI Codex CLI配置 (完全兼容)
OPENAI_API_KEY = "sk-your-api-key"
OPENAI_BASE_URL = "http://172.17.0.1:3000/v1"
OPENAI_MODEL = "glm-4.5"
```

#### 数据分析项目配置

```toml
# ~/projects/my-data-analysis/.mise.toml
[env]
# Claude Code CLI配置
ANTHROPIC_API_KEY = "sk-your-api-key"
ANTHROPIC_BASE_URL = "http://172.17.0.1:3000/v1"
CLAUDE_MODEL = "glm-4.6"

# OpenAI Codex CLI配置 (完全兼容)
OPENAI_API_KEY = "sk-your-api-key"
OPENAI_BASE_URL = "http://172.17.0.1:3000/v1"
OPENAI_MODEL = "glm-4.6"
```

#### 经济模式项目配置

```toml
# ~/projects/personal-projects/.mise.toml
[env]
# Claude Code CLI配置
ANTHROPIC_API_KEY = "sk-your-api-key"
ANTHROPIC_BASE_URL = "http://172.17.0.1:3000/v1"
CLAUDE_MODEL = "moonshot-v1-8k"

# OpenAI Codex CLI配置 (完全兼容)
OPENAI_API_KEY = "sk-your-api-key"
OPENAI_BASE_URL = "http://172.17.0.1:3000/v1"
OPENAI_MODEL = "moonshot-v1-8k"
```

### 阶段4: 验证和测试 (10分钟)

```bash
# 1. 测试开发项目
cd ~/projects/my-web-app
mise init  # 如果还没有.mise.toml
echo "🤖 Development AI Configuration"
echo "==================================="
echo "📁 Project: $(basename "$(pwd)")"
echo "🎯 Claude Code: glm-4.6"
echo "🎯 OpenAI Codex: glm-4.6"
echo "🔗 Veloera: http://172.17.0.1:3000/v1"

# 2. 测试内容创作项目
cd ~/projects/my-blog
echo "🤖 Content Creation AI Configuration"
echo "===================================="
echo "📁 Project: $(basename "$(pwd)")"
echo "🎯 Model: glm-4.5 (Balanced Mode)"
echo "🔗 Endpoint: http://172.17.0.1:3000/v1"
claude "写一篇关于AI工具管理的技术博客"

# 3. 测试数据分析项目
cd ~/projects/my-data-analysis
echo "🤖 Data Analysis AI Configuration"
echo "================================="
echo "📁 Project: $(basename "$(pwd)")"
echo "🎯 Model: glm-4.6 (Enhanced Reasoning)"
echo "🔗 Endpoint: http://172.17.0.1:3000/v1"
claude "分析这个CSV文件的数据趋势"

# 4. 测试项目切换
cd ~/projects/my-web-app
claude "创建一个React组件"
cd ~/projects/my-blog
claude "写一篇技术文档"
# 自动使用不同模型，无需手动切换
```

## 备选方案: Claude Code + CCR + Veloera

如果需要更复杂的路由策略，可以使用CCR作为中间层：

```
Claude Code → CCR (智能路由) → Veloera (Provider汇集) → 各Provider APIs
```

**CCR优势**:
- 智能场景路由 (编程/分析/创作等)
- 模型间自动切换
- 更精细的格式转换

**CCR劣势**:
- 需要维护多个CCR配置文件
- 增加了一层复杂性
- CCR配置文件切换需要额外工具支持

## 日常使用

### 基本工作流

```bash
# 1. 进入任何项目目录
cd ~/projects/my-web-app

# 2. 查看当前AI配置 (可选)
echo "🤖 Current AI Configuration"
echo "============================"
echo "📁 Project: $(basename "$(pwd)")"
echo "🎯 Model: ${CLAUDE_MODEL:-未设置}"
echo "🔗 Endpoint: ${ANTHROPIC_BASE_URL:-未设置}"

# 3. 直接使用Claude Code
claude "写一个完整的React登录组件，包含表单验证"
# 自动使用项目配置的glm-4.6模型

# 4. 切换到其他项目
cd ~/projects/my-blog
claude "写一篇关于React最佳实践的文章"
# 自动使用项目配置的glm-4.5模型
```

### 管理命令

```bash
# 查看当前项目配置
echo "🤖 Current AI Configuration"
echo "============================"
echo "📁 Project: $(basename "$(pwd)")"
echo "🎯 Model: ${CLAUDE_MODEL:-未设置}"
echo "🔗 Endpoint: ${ANTHROPIC_BASE_URL:-未设置}"

# 测试AI连接
curl -s "${ANTHROPIC_BASE_URL}/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: ${ANTHROPIC_API_KEY}" \
  -d '{"model": "'"$CLAUDE_MODEL"'", "max_tokens": 10, "messages": [{"role": "user", "content": "Test"}]}' | grep -q "content" && echo "✅ 连接成功" || echo "❌ 连接失败"

# 查看Mise环境变量
mise env | grep -E "(ANTHROPIC|OPENAI)"

# 检查Veloera状态
docker-compose ps
docker-compose logs veloera

# 更换API Key (在Veloera Web界面)
# 访问: http://172.17.0.1:3000
```

## 故障排查

### 常见问题

1. **Claude Code无法连接**
```bash
# 检查环境变量
mise env | grep ANTHROPIC

# 检查Veloera状态
curl -s "http://172.17.0.1:3000/health"

# 测试连接
claude "Hello, test connection"
```

2. **项目配置不生效**
```bash
# 重新加载Mise环境
eval "$(mise activate bash)"

# 检查项目配置
cat .mise.toml

# 调试模式
echo "🤖 Debug: AI Configuration"
echo "=========================="
echo "CLAUDE_MODEL: ${CLAUDE_MODEL:-未设置}"
echo "ANTHROPIC_API_KEY: ${ANTHROPIC_API_KEY:0:8}..."
echo "ANTHROPIC_BASE_URL: ${ANTHROPIC_BASE_URL:-未设置}"
```

3. **Veloera问题**
```bash
# 检查容器状态
docker-compose ps

# 查看日志
docker-compose logs veloera

# 重启服务
docker-compose restart veloera
```

## 扩展架构：多CLI工具支持

### 支持的CLI工具
- **Claude Code CLI** (Anthropic格式)
- **OpenAI Codex CLI** (OpenAI格式)
- **任何标准OpenAI/Anthropic格式的AI CLI工具**

### 统一配置示例
```bash
# Claude Code CLI
ANTHROPIC_API_KEY="sk-your-veloera-api-key"
ANTHROPIC_BASE_URL="http://10.1.1.11:3000/v1"

# OpenAI Codex CLI
OPENAI_API_KEY="sk-your-veloera-api-key"
OPENAI_BASE_URL="http://10.1.1.11:3000/v1"

# 两个CLI都通过Veloera使用相同的Provider和模型
```

## 总结

这个 **Claude Code CLI + Mise + Veloera** 解决方案：

✅ **智能转换枢纽**: Veloera作为API转换和路由中心，屏蔽Provider复杂性
✅ **项目级切换**: 自动根据项目目录切换AI配置和模型偏好
✅ **多CLI支持**: 同时支持Claude Code CLI、OpenAI Codex CLI等标准格式工具
✅ **模型偏好**: 不同项目使用最适合的模型 (glm-4.6, glm-4.5, kimi等)
✅ **集中管理**: API Key和Provider统一管理，支持特殊计费计划
✅ **使用简单**: 只需要使用标准CLI命令，自动应用项目配置

这个方案完美解决了AI工具管理的所有痛点，提供了最佳的开发体验！