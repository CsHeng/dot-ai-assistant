# AI工具统一管理系统实施指南

## 1. 实施概述

### 1.1 实施目标
基于Veloera + Mise构建AI工具统一管理系统，解决Provider分散、配置复杂的痛点，实现：
- 统一的Provider管理和API Key集中管理
- 项目级自动切换和环境隔离
- 所有工具通过统一HTTP API接口访问
- 透明化的使用统计和成本管理

### 1.2 技术栈
- Veloera: Standalone API Gateway，提供Provider汇集和API转发，支持特殊endpoint
- Mise: 项目级环境管理，实现自动切换
- Docker: Veloera容器化部署
- CLI工具: Claude CLI、Qwen CLI、Gemini CLI等

### 1.3 实施时间规划
- 阶段1: Veloera部署 (20分钟)
- 阶段2: Provider配置 (25分钟，包含特殊endpoint配置)
- 阶段3: Mise配置 (15分钟)
- 阶段4: 项目配置 (10分钟)
- 阶段5: 工具配置 (20分钟)

总计: 约110分钟 (2小时)

## 2. 详细实施步骤

### 2.1 阶段1: Veloera部署 (20分钟)

#### 2.1.1 系统要求
- Docker 或 Docker Compose
- 1GB+ 可用内存
- 网络连接（用于配置Provider）

#### 2.1.2 部署Veloera

方式: Docker Compose
```bash
# 创建工作目录
mkdir -p ~/veloera
cd ~/veloera

# 创建环境变量文件
cat > .env << 'EOF'
# Veloera环境变量配置
# 具体配置项请参考Veloera官方文档
# 这里配置数据库、日志等基础设置
EOF

# 创建数据目录
mkdir -p ./data

# 创建docker-compose.yml
cat > docker-compose.yml << 'EOF'
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
EOF

# 启动服务
docker-compose up -d

# 检查状态
docker-compose ps
```

#### 2.1.3 验证部署

```bash
# 测试Veloera服务
curl -s "http://localhost:3000/v1/models" | jq

# 检查日志
docker-compose logs veloera
```

### 2.2 阶段2: Provider配置 (25分钟)

#### 2.2.1 获取API URL和Keys

准备以下Provider的API URL and Keys：
- BigGLM API URL: `https://open.bigmodel.cn/api/paas/v4`
- BigGLM Coding Plan API URL: `https://open.bigmodel.cn/api/coding/paas/v4`
- BigGLM API Key (支持coding endpoint)
- DeepSeek API URL: `https://api.deepseek.com/v1`
- DeepSeek API Key
- Kimi API URL: `https://api.moonshot.cn/v1`
- Kimi API Key

#### 2.2.2 验证Provider配置

```bash
# 测试BigGLM模型
curl -s -X POST "http://localhost:3000/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "glm-4.6",
    "messages": [{"role": "user", "content": "写一个Python的Hello World"}],
    "max_tokens": 100
  }' | jq

# 测试模型列表
curl -s "http://localhost:3000/v1/models" | jq '.data[].id'
```

### 2.3 阶段3: Mise配置 (15分钟)

#### 2.3.1 安装Mise (如果未安装)

```bash
# macOS
curl https://mise.run | sh

# 重启终端或执行
echo 'eval "$(mise activate bash)"' >> ~/.bashrc
source ~/.bashrc

# 验证安装
mise --version
```

#### 2.3.2 全局配置

```bash
# 创建全局配置目录
mkdir -p ~/.config/mise

# 创建全局配置
cat > ~/.config/mise/config.toml << 'EOF'
[env]
# Veloera全局配置
VELOERA_URL = "http://localhost:3000/v1"
VELOERA_TOKEN = "your-one-api-token-here"

# 默认配置
DEFAULT_PROVIDER = "bigglm"
DEFAULT_MODEL = "GLM-4.6"

# CLI工具默认配置
OPENAI_API_KEY = "{{ env.VELOERA_TOKEN }}"
OPENAI_BASE_URL = "{{ env.VELOERA_URL }}"
ANTHROPIC_API_KEY = "{{ env.VELOERA_TOKEN }}"
ANTHROPIC_BASE_URL = "{{ env.VELOERA_URL }}"
EOF

# 设置实际Token
mise set VELOERA_TOKEN="your-one-api-token-here"
```

### 2.4 阶段4: 项目配置 (10分钟)

#### 2.4.1 示例项目配置

开发项目
```bash
cd ~/projects/my-web-app

# 初始化mise
mise init

# 创建项目配置
cat > .mise.toml << 'EOF'
[env]
# 工具配置
OPENAI_MODEL = "GLM-4.6"
CLAUDE_MODEL = "GLM-4.6"
EOF

# 应用配置
eval "$(mise env bash)"
echo "✅ 已切换到模型: GLM-4.6"
```

数据分析项目
```bash
cd ~/projects/my-data-analysis

mise init

cat > .mise.toml << 'EOF'
[env]
OPENAI_MODEL = "GLM-4.6"
CLAUDE_MODEL = "GLM-4.6"
EOF

eval "$(mise env bash)"
```

### 2.5 阶段5: 工具配置 (20分钟)

#### 2.5.1 Claude CLI配置

```bash
# 检查安装
which claude || npm install -g @anthropic-ai/claude-cli

# 配置环境变量 (mise自动管理)
# 无需手动设置，通过mise环境变量自动配置
```

#### 2.5.2 Qwen CLI配置

```bash
# 检查安装
which qwen || npm install -g @qwen/cli

# 测试
qwen --version
```

#### 2.5.3 Claude Code + CCR配置

```bash
npx @musistudio/claude-code-router@latest

alias ccr="npx @musistudio/claude-code-router@latest"
```

#### 2.5.4 IDE配置

Cursor配置
```bash
# Cursor会自动使用环境变量配置
# 无需额外配置
```

VS Code Copilot配置
```bash
# Copilot使用GitHub账户，与Veloera独立
# 如需统一，可考虑使用其他AI助手
```

## 3. 验证测试

### 3.1 Veloera连接测试

```bash
# 测试Veloera服务
curl -s "http://localhost:3000/v1/models" | jq

# 测试API调用
echo "Testing AI connection..."
```

### 3.2 工具测试

```bash
# 测试Claude CLI
claude "写一个Hello World程序"

# 测试Qwen CLI
qwen "分析这个时间复杂度"

# 测试Claude Code + CCR
ccr code
# 在Claude Code中测试 /model 命令
```

### 3.3 项目切换测试

```bash
# 测试项目A
cd ~/projects/my-web-app
echo "Current project: Development"
claude "这是一个开发项目"

# 测试项目B
cd ~/projects/my-data-analysis
echo "Current project: Data Analysis"
claude "这是一个数据分析项目"
```

## 4. 故障排查

### 4.1 Veloera问题

服务无法启动
```bash
# 检查Docker状态
docker ps | grep veloera

# 检查端口占用
lsof -i :3000

# 查看日志
docker-compose logs veloera
```

Provider连接失败
```bash
# 检查API Key
echo $YOUR_API_KEY

# 测试API连通性
curl -H "Authorization: Bearer $YOUR_API_KEY" \
     "https://api.bigglm/v1/models"

# 检查Veloera渠道配置
# 访问 http://localhost:3000 → 渠道管理
```

### 4.2 Mise问题

环境变量未生效
```bash
# 检查mise配置
mise env | grep -E "(VELOERA|OPENAI|ANTHROPIC)"

# 重新加载环境
eval "$(mise activate bash)"

# 检查配置文件
cat ~/.config/mise/config.toml
cat .mise.toml
```

### 4.3 工具问题

Claude CLI无法使用
```bash
# 检查安装
which claude
claude --version

# 检查配置
env | grep -E "(ANTHROPIC|CLAUDE)"

# 重新安装
npm uninstall -g @anthropic-ai/claude-cli
npm install -g @anthropic-ai/claude-cli
```

CCR配置问题
```bash
# 检查配置文件
cat ~/.claude-code-router/config.json

# 测试CCR
ccr model
ccr restart
```

## 5. 安全建议

- API Key管理: 定期轮换API Keys，不要在代码中硬编码
- 网络安全: Veloera仅内网访问，配置防火墙规则
- 访问控制: 为不同用户创建不同的Token和配额
- 日志审计: 定期检查Veloera访问日志
- 备份加密: 敏感数据备份进行加密存储

---

## 6. 实施总结

通过以上实施步骤，您将获得：

- 统一的Provider管理: Veloera汇集所有Provider，一处配置处处使用
- 项目级自动切换: Mise实现不同项目的AI配置自动切换
- 简化的工具使用: 所有CLI工具通过统一HTTP API接口使用
- 透明化的使用情况: Web界面查看使用统计和成本
- 高可用的服务: 自动故障转移和负载均衡

这个方案彻底解决了Provider分散、配置复杂的痛点，大大提升了AI工具的使用效率！