# 实用的多提供商AI工具配置方案

## 设计理念

基于您的实际需求，设计一个以CCR为核心 + mise辅助的实用架构：

- CCR作为统一路由: 处理所有提供商切换和智能分发
- Mise管理环境配置: 提供项目级的环境隔离和配置切换
- 简化操作流程: 一键切换，智能优化
- 高可用性: 自动故障转移，保证工作连续性

## 核心优势对比

| 方案 | CCR + Mise | 纯Mise环境变量 |
|------|------------|----------------|
| 切换便利性 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| 智能路由 | ⭐⭐⭐⭐⭐ | ⭐⭐ |
| 故障转移 | ⭐⭐⭐⭐⭐ | ⭐ |
| 学习成本 | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| 配置复杂度 | ⭐⭐⭐ | ⭐⭐⭐⭐ |

结论: CCR + Mise 组合在切换便利性和功能完整性上更有优势

## 配置结构

```
project-root/
├── .mise.toml                    # mise环境配置
├── .ai-config/                   # AI配置目录
│   ├── providers.yaml           # 提供商配置
│   ├── strategies.yaml          # 策略配置
│   └── projects.yaml            # 项目配置
└── .claude/
    └── rules/                  # 项目规则
```

## 配置文件设计

### .mise.toml

```toml
[env]
# 项目默认配置
AI_PROJECT_NAME = "{{ config_root | basename }}"
AI_DEFAULT_PROVIDER = "bigglm"
AI_AUTO_SWITCH = "false"

# CCR配置
CCR_CONFIG_FILE = "{{ config_root }}/.ai-config/ccr-config.yaml"
CCR_AUTO_MODE = "false"

```

### .ai-config/providers.yaml

```yaml
# 提供商配置
providers:
  bigglm:
    name: "BigGLM"
    models:
      - name: "GLM-4.6"
        api_key: "{{ env.BIGGLM_API_KEY }}"
        base_url: "https://api.bigglm/v1"
        cost_per_token: 0.0001
        speed: "very_fast"
        capabilities: ["code", "text", "creative"]
      - name: "GLM-4.5"
        api_key: "{{ env.BIGGLM_API_KEY }}"
        base_url: "https://api.bigglm/v1"
        cost_per_token: 0.00008
        speed: "fast"
        capabilities: ["text", "analysis"]

  bigglm:
    name: "BigGLM"
    models:
      - name: "GLM-4.6"
        api_key: "{{ env.BIGGLM_API_KEY }}"
        base_url: "https://open.bigmodel.cn/api/paas/v4"
        cost_per_token: 0.00012
        speed: "fast"
        capabilities: ["code", "math", "analysis"]
      - name: "GLM-4.5"
        api_key: "{{ env.BIGGLM_API_KEY }}"
        base_url: "https://open.bigmodel.cn/api/paas/v4"
        cost_per_token: 0.0001
        speed: "medium"
        capabilities: ["text", "translation"]

  deepseek:
    name: "DeepSeek"
    models:
      - name: "deepseek-chat"
        api_key: "{{ env.DEEPSEEK_API_KEY }}"
        base_url: "https://api.deepseek.com/v1"
        cost_per_token: 0.00014
        speed: "medium"
        capabilities: ["code", "reasoning", "math"]

  kimi:
    name: "Kimi"
    models:
      - name: "moonshot-v1-8k"
        api_key: "{{ env.KIMI_API_KEY }}"
        base_url: "https://api.moonshot.cn/v1"
        cost_per_token: 0.00006
        speed: "medium"
        capabilities: ["text", "translation", "summary"]

# 默认策略
default_strategies:
  economy: ["kimi", "bigglm", "deepseek"]
  performance: ["bigglm", "deepseek", "kimi"]
  balanced: ["bigglm", "deepseek", "kimi"]
```

### .ai-config/strategies.yaml

```yaml
# 智能策略配置
strategies:
  task_based:
    coding:
      preferred: ["bigglm", "deepseek", "bigglm"]
      fallback: ["kimi"]

    creative_writing:
      preferred: ["bigglm", "bigglm"]
      fallback: ["deepseek", "kimi"]

    analysis:
      preferred: ["bigglm", "bigglm", "deepseek"]
      fallback: ["kimi"]

    translation:
      preferred: ["kimi", "bigglm", "bigglm"]
      fallback: ["deepseek"]

  cost_optimization:
    threshold_per_hour: 1.0  # 每小时成本阈值
    switch_to_economy: true
    preferred_cheapest: ["kimi", "bigglm"]

  performance_optimization:
    response_time_threshold: 2.0  # 秒
    preferred_fastest: ["bigglm", "deepseek"]

# 项目类型映射
project_types:
  web_development:
    default_provider: "bigglm"
    preferred_models: ["GLM-4.6"]

  data_analysis:
    default_provider: "bigglm"
    preferred_models: ["GLM-4.6"]

  content_creation:
    default_provider: "bigglm"
    preferred_models: ["GLM-4.6", "GLM-4.5"]
```

### .ai-config/projects.yaml

```yaml
# 项目级配置
projects:
  my-web-app:
    type: "web_development"
    default_provider: "bigglm"
    auto_switch: true
    cost_limit: 5.0  # 每日成本限制

  my-blog:
    type: "content_creation"
    default_provider: "bigglm"
    auto_switch: false

  data-analysis:
    type: "data_analysis"
    default_provider: "bigglm"
```

## 使用示例

### 基本使用

```bash
# 查看当前状态
ccr status

# 快速切换提供商
ccr switch bigglm
ccr switch deepseek
ccr switch kimi

# 启用经济模式
ccr economy

# 启用性能模式
ccr performance

# 启用智能模式
ccr auto
```

### 项目级配置

```bash
# 项目A - 开发
cd ~/projects/web-app
echo "default_provider: bigglm" > .ai-config/project.yaml
ccr switch bigglm

# 项目B - 内容创作
cd ~/projects/blog
echo "default_provider: bigglm" > .ai-config/project.yaml
ccr switch bigglm
```

### 直接使用CCR命令

```bash
# 列出所有提供商
ccr list

# 切换提供商
ccr switch bigglm

# 启用智能模式
ccr auto

# 查看统计
ccr stats
```

## 扩展性设计

### 添加新提供商

1. 在 `providers.yaml` 中添加配置
2. 配置相应的API密钥环境变量
3. 使用CCR命令直接切换到新提供商

```bash
# 添加新提供商示例 (如 Ollama)
# 1. 更新 providers.yaml
ollama:
  name: "Ollama"
  models:
    - name: "llama3"
      base_url: "http://localhost:11434"
      cost_per_token: 0
      speed: "fast"

# 2. 设置环境变量
export OLLAMA_API_KEY="your-key"

# 3. 使用CCR切换
ccr switch ollama
echo "✅ 已切换到 Ollama"
```

### 团队配置共享

```bash
# 导出项目配置
ai-export > team-config.yaml

# 导入团队配置
ai-import team-config.yaml
```

## 总结

这个方案的核心优势：

- 切换极其方便: 一条命令即可切换任何提供商
- 智能优化: 自动选择最优提供商平衡成本和性能
- 高可用性: 自动故障转移，保证工作连续性
- 项目隔离: 不同项目可以有不同配置
- 易于扩展: 添加新提供商非常简单
- 统一管理: CCR作为唯一入口，简化操作

相比纯mise环境变量方案，这个CCR+mise的组合在使用便利性和功能完整性上更有优势，特别适合您这样需要频繁切换多个提供商的场景。

您觉得这个方案如何？需要调整哪些部分？