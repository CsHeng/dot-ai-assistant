# 纠正后的实用多提供商AI工具方案

## 核心认知更新

经过分析，CCR（Claude Code Router）的实际功能是：
- 仅为Claude Code提供路由服务
- 没有独立的CLI切换命令
- 切换方式：在Claude Code内使用 `/model provider,model`
- 不能统一管理Qwen/Gemini等其他CLI工具

## 重新设计的实用方案

### 方案1: Mise环境变量 + 脚本封装（推荐）

这是最实用和灵活的方案：

#### 配置结构
```
project-root/
├── .mise.toml                    # mise主配置
├── .ai-config/                   # AI配置目录
│   ├── providers.yaml           # 提供商配置
│   └── profiles.yaml            # 预设配置文件
└── .claude/
    └── rules/                  # Claude规则
```

#### .mise.toml 配置
```toml
[env]
# 默认配置
AI_CURRENT_PROVIDER = "bigglm"
AI_CURRENT_MODEL = "GLM-4.6"
AI_MODE = "balanced"

# 提供商API配置（环境变量）
BIGGLM_API_KEY = "{{ env.BIGGLM_API_KEY }}"
DEEPSEEK_API_KEY = "{{ env.DEEPSEEK_API_KEY }}"
KIMI_API_KEY = "{{ env.KIMI_API_KEY }}"

# Claude CLI配置
ANTHROPIC_API_KEY = "{{ env.BIGGLM_API_KEY }}"  # 默认使用BigGLM
CLAUDE_MODEL = "{{ env.AI_CURRENT_MODEL }}"
ANTHROPIC_BASE_URL = "https://api.bigglm/v1"
```

### 使用示例

#### 基本切换
```bash
# 切换到不同提供商
ai-switch bigglm
ai-switch bigglm GLM-4.5
ai-switch deepseek
ai-switch kimi

# 使用预设配置
ai-profile economy
ai-profile performance
ai-profile coding

# 查看当前状态
ai-status
```

#### 项目级配置
```bash
# 项目A - 开发
cd ~/projects/web-app
cat >> .mise.toml << EOF
[env]
AI_CURRENT_PROVIDER = "bigglm"
AI_CURRENT_MODEL = "GLM-4.6"
EOF
ai-switch bigglm

# 项目B - 内容创作
cd ~/projects/blog
cat >> .mise.toml << EOF
[env]
AI_CURRENT_PROVIDER = "bigglm"
AI_CURRENT_MODEL = "GLM-4.6"
EOF
ai-switch bigglm
```

### 方案2: 混合方案（保留CCR用于Claude Code）

如果确实需要使用Claude Code的高级功能：

1. 日常CLI使用: 用方案1的mise环境变量方案
2. Claude Code使用: 配置CCR，在Claude Code内用 `/model` 切换

#### CCR配置示例 (~/.claude-code-router/config.json)
```json
{
  "APIKEY": "your-secret-key",
  "Providers": [
    {
      "name": "bigglm",
      "api_base_url": "https://open.bigmodel.cn/api/paas/v4",
      "api_key": "$BIGGLM_API_KEY",
      "models": ["GLM-4.6", "GLM-4.5"]
    }
  ],
  "Router": {
    "default": "bigglm,GLM-4.6",
    "coding": "bigglm,GLM-4.6"
  }
}
```

## 推荐方案

基于您的实际需求，我强烈推荐方案1（Mise环境变量 + 脚本封装）：

### 优势：
- 统一管理: 一个命令管理所有AI工具
- 使用简单: `ai-switch bigglm` 很直观
- 项目隔离: 每个项目可以有不同配置
- 扩展性强: 轻松添加新提供商
- 无额外依赖: 只需要mise和bash脚本

### 与CCR对比：
| 功能 | Mise方案 | CCR方案 |
|------|----------|---------|
| 统一CLI管理 | ✅ | ❌ |
| 项目级配置 | ✅ | ❌ |
| 使用便利性 | ✅ | ⚠️ |
| Claude Code集成 | ⚠️ | ✅ |
| 学习成本 | 低 | 中等 |

您觉得这个纠正后的方案如何？更符合您的实际需求吗？