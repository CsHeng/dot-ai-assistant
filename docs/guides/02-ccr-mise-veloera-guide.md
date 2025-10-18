# AI工具管理架构实施指南

## 概述

本指南详细说明如何实施统一的AI工具管理架构，支持两种主要使用模式：
1. **通用多工具架构**：Mise + 各种官方CLI工具 + Veloera
2. **Claude Code专用架构**：Claude Code + CCR + Veloera

## 架构模式

### 通用多工具架构（主要模式）
```
Project A (codex)    Project B (claude)    Project C (qwen)
       ↓                   ↓                   ↓
    Mise Env           Mise Env           Mise Env
       ↓                   ↓                   ↓
  Official CLI       Official CLI       Official CLI
       ↓                   ↓                   ↓
              Veloera (Provider Gateway + Format Conversion)
                       ↓
                Multiple AI Providers
```

### Claude Code专用架构（特殊模式）
```
Claude Code → CCR (Specialized Routing/Conversion) → Veloera → AI Providers
```

### 核心组件职责

1. **Veloera**: 通用Provider网关，API Key管理，格式转换，计费计划支持
2. **Mise**: 项目级环境管理，工具选择，配置切换
3. **官方CLI工具**: 使用原生协议直接连接Veloera
4. **CCR**: 专用路由器，仅用于Claude Code的特殊协议处理

**重要说明**: CCR是独立工具，仅与Claude Code配合使用，不影响其他CLI工具的工作流程。

## 阶段1: Veloera 部署和配置

### 1.1 Docker Compose 部署

创建 `docker-compose.yml`:

```yaml
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

### 1.2 环境变量配置

创建 `.env` 文件：

```bash
# Veloera环境变量配置
# 根据Veloera官方文档配置数据库、日志等
```

### 1.3 数据目录初始化

```bash
# 创建数据目录
mkdir -p ./data

# 启动服务
docker-compose up -d

# 检查状态
docker-compose ps
```

### 1.4 Provider 配置

通过 Veloera Web 界面或 API 配置以下 Provider：
- BigGLM (支持 regular 和 coding 计费计划)
- DeepSeek
- Kimi
- 其他需要的 Provider

## 阶段2: CCR 配置

### 2.1 基础 CCR 配置

创建 `~/.claude-code-router/config.json`:

```json
{
  "LOG": true,
  "LOG_LEVEL": "debug",
  "HOST": "127.0.0.1",
  "PORT": 3456,
  "APIKEY": "your-ccr-api-key",
  "API_TIMEOUT_MS": "600000",
  "PROXY_URL": "",
  "transformers": [],
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
        "use": ["OpenAI"]
      }
    }
  ],
  "StatusLine": {
    "enabled": false,
    "currentStyle": "default",
    "default": { "modules": [] },
    "powerline": { "modules": [] }
  },
  "Router": {
    "default": "veloera,glm-4.5",
    "background": "veloera,glm-4.5",
    "think": "veloera,glm-4.5",
    "longContext": "veloera,glm-4-long",
    "longContextThreshold": 200000,
    "webSearch": "veloera,glm-4.5",
    "image": "veloera,glm-4.5v"
  },
  "CUSTOM_ROUTER_PATH": ""
}
```

**Note**: Since CCR runs as a single instance with default configuration only, multiple configuration files are not supported. Model selection is handled through the `ccr model` command for interactive switching.

### 2.3 CCR 基础操作

```bash
# 启动 CCR
ccr start

# 检查状态
ccr status

# 交互式模型选择
ccr model

# 重启 CCR (应用新配置)
ccr restart

# 停止 CCR
ccr stop
```

## 阶段3: Mise 项目级配置

### 3.1 Mise 安装和基础配置

```bash
# macOS 安装
curl https://mise.run | sh

# 重启终端或执行
echo 'eval "$(mise activate bash)"' >> ~/.bashrc
source ~/.bashrc

# 验证安装
mise --version
```

### 3.2 Web 开发项目配置

创建项目目录下的 `.mise.toml`:

```toml
[env]
PROJECT_NAME = "{{ config_root | basename }}"
```

### 3.3 内容创作项目配置

```toml
[env]
PROJECT_NAME = "{{ config_root | basename }}"
```

### 3.4 数据分析项目配置

```toml
[env]
PROJECT_NAME = "{{ config_root | basename }}"
```

## 阶段4: 集成和测试

### 4.1 端到端测试流程

#### 测试 Web 开发项目

```bash
# 进入 Web 开发项目目录
cd ~/projects/my-web-app

# 初始化 Mise
mise init

# 创建 Web 开发配置 (.mise.toml)
# (使用上面的 Web 开发项目配置)

# 使用 Claude Code
ccr code "写一个 React 的 Hello World 组件"
```

#### 测试内容创作项目

```bash
# 进入内容创作项目目录
cd ~/projects/my-blog

# 初始化 Mise
mise init

# 创建内容创作配置 (.mise.toml)
# (使用上面的内容创作项目配置)

# 使用 Claude Code
ccr code "写一篇关于人工智能的博客文章"
```

#### 测试数据分析项目

```bash
# 进入数据分析项目目录
cd ~/projects/my-data-analysis

# 初始化 Mise
mise init

# 创建数据分析配置 (.mise.toml)
# (使用上面的数据分析项目配置)

# 使用 Claude Code
ccr code "分析这个数据集的主要趋势"
```

### 4.2 项目切换测试

```bash
# 测试项目间切换
cd ~/projects/my-web-app
echo "Current: Development (glm-4.6)"

cd ~/projects/my-blog
echo "Current: Content Creation (glm-4.5)"

cd ~/projects/my-data-analysis
echo "Current: Data Analysis (glm-4.6)"
```

### 4.3 故障排查

#### CCR 相关问题

```bash
# 检查 CCR 状态
ccr status

# 检查 CCR 日志
ccr logs

# 重启 CCR
ccr restart

# 检查 CCR 配置
cat ~/.claude-code-router/config.json
```

#### Veloera 相关问题

```bash
# 检查 Veloera 容器状态
docker-compose ps

# 查看 Veloera 日志
docker-compose logs veloera

# 测试 Veloera 连接
curl -s "http://10.1.1.11:3000/health"

# 检查 Veloera 配置
curl -s "http://10.1.1.11:3000/models"
```

#### Mise 相关问题

```bash
# 检查 Mise 环境
mise env

# 检查项目配置
cat .mise.toml

# 重新加载环境
eval "$(mise activate bash)"
```

## 阶段5: 日常使用和维护

### 5.1 日常使用命令

```bash
# 使用 Claude Code
ccr code "你的问题或指令"

# 交互式模型选择
ccr model
```

### 5.2 维护任务

#### 定期检查 (每周)

```bash
# 检查 Veloera 服务状态
docker-compose ps

# 检查 CCR 状态
ccr status

# 检查各项目配置
for project in ~/projects/*/; do
    echo "=== $(basename "$project") ==="
    cd "$project" && echo "Project: $(basename "$project")" 2>/dev/null || echo "No Mise config"
done
```

#### 配置备份 (每月)

```bash
# 备份 CCR 配置
mkdir -p ~/backup/$(date +%Y%m)
cp ~/.claude-code-router/*.json ~/backup/$(date +%Y%m)/

# 备份 Mise 配置
cp ~/.config/mise/config.toml ~/backup/$(date +%Y%m)/

# 备份项目配置
find ~/projects -name ".mise.toml" -exec cp {} ~/backup/$(date +%Y%m)/ \;
```

### 5.3 性能优化

#### CCR 优化

```json
{
  "API_TIMEOUT_MS": "300000",
  "LOG_LEVEL": "info",
  "StatusLine": {
    "enabled": true
  }
}
```

#### Mise 优化

```toml
# 全局配置 ~/.config/mise/config.toml
[experimental]
mps = true
```

## 总结

通过这个完整的实施指南，你可以：

1. ✅ 部署 Veloera 作为 Provider 汇聚层
2. ✅ 配置多个 CCR 配置文件支持不同项目
3. ✅ 使用 Mise 实现项目级配置自动切换
4. ✅ 通过 Claude Code 获得统一的 AI 开发体验
5. ✅ 支持 glm-4.5、glm-4.6 等模型的智能选择

这个架构完美解决了：
- Provider 分散管理问题
- 项目级配置切换问题
- 模型偏好和API格式转换问题
- 计费计划支持问题

现在你可以在不同项目中自动获得最适合的 AI 配置，大大提升开发效率！