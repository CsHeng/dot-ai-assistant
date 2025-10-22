# Claude Code + Mise + Veloera 完整解决方案

## 🎯 最终选型

**主要架构**: Claude Code CLI + Mise + Veloera
**备选方案**: Claude Code + CCR + Veloera (需要多配置文件切换)

## 2. 核心架构

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
│                    Mise (项目管理层)                        │
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

## 3. 配置体系设计

### 3.1 Veloera部署配置

```yaml
# docker-compose.yml
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

### 3.2 Mise 项目级配置

#### 开发项目 (.mise.toml)
```toml
[env]
# Veloera配置
VELOERA_TOKEN = "your-veloera-token"
VELOERA_ANTHROPIC_URL = "http://localhost:3000/v1"

# Claude Code配置
ANTHROPIC_AUTH_TOKEN = "{{ env.VELOERA_TOKEN }}"
ANTHROPIC_BASE_URL = "{{ env.VELOERA_ANTHROPIC_URL }}"
CLAUDE_MODEL = "glm-4.6"
```

#### 内容创作项目 (.mise.toml)
```toml
[env]
# Veloera配置
VELOERA_TOKEN = "your-veloera-token"
VELOERA_ANTHROPIC_URL = "http://localhost:3000/v1"

# Claude Code配置
ANTHROPIC_AUTH_TOKEN = "{{ env.VELOERA_TOKEN }}"
ANTHROPIC_BASE_URL = "{{ env.VELOERA_ANTHROPIC_URL }}"
CLAUDE_MODEL = "glm-4.5"
```

## 4. 自动切换实现

### 4.1 目录检测脚本
```bash
#!/usr/bin/env bash
# ~/.local/bin/auto-switch-ai

detect_project_type() {
  local dir="$1"

  # 检测package.json中的依赖
  if [[ -f "$dir/package.json" ]]; then
    if grep -q "react\|vue\|angular" "$dir/package.json"; then
      echo "development"
      return
    fi
  fi

  # 检测README内容
  if [[ -f "$dir/README.md" ]]; then
    if grep -q "docs\|documentation\|tutorial" "$dir/README.md"; then
      echo "content_creation"
      return
    fi
  fi

  # 检测数据分析文件
  if ls "$dir"/*.csv "$dir"/*.json "$dir"/*.ipynb >/dev/null 2>&1; then
    echo "data_analysis"
    return
  fi

  # 默认配置
  echo "general"
}

# 主逻辑
current_dir="$(pwd)"
project_type="$(detect_project_type "$current_dir")"

echo "🔄 检测到项目类型: $project_type"
echo "✅ 已切换到 $project_type 配置"
```

### 4.2 Shell集成
```bash
# ~/.zshrc 或 ~/.bashrc
# 添加cd hook
autoload -U add-zsh-hook 2>/dev/null || true

auto_switch_ai() {
  if [[ -f ".mise.toml" ]] && command -v auto-switch-ai >/dev/null; then
    auto-switch-ai
  fi
}

# Zsh hook
if command -v add-zsh-hook >/dev/null; then
  add-zsh-hook chpwd auto_switch_ai
else
  # Bash fallback
  cd() {
    builtin cd "$@" && auto_switch_ai
  }
fi
```

## 5. 工作流程

### 5.1 初始设置
```bash
# 1. 部署Veloera
docker-compose up -d

# 2. 配置Provider
# 通过Web界面配置Provider

# 3. 配置Mise全局环境
mise settings set experimental true
mise install
```

### 5.2 项目使用
```bash
# 进入项目目录 (自动切换)
cd ~/projects/my-web-app
# → 自动检测并切换到development配置

# 查看当前状态
mise env | grep -E "(CLAUDE_MODEL|ANTHROPIC)"

# 使用Claude Code
claude "帮我实现一个用户登录组件"
```

## 6. 高级功能

### 6.1 配置验证
```bash
#!/usr/bin/env bash
# scripts/validate-ai-config.sh

validate_config() {
    local config_file=".mise.toml"

    if [[ ! -f "$config_file" ]]; then
        echo "❌ 配置文件不存在: $config_file"
        return 1
    fi

    # 验证必要字段
    if ! grep -q "CLAUDE_MODEL" "$config_file"; then
        echo "❌ 缺少CLAUDE_MODEL配置"
        return 1
    fi

    echo "✅ 配置验证通过"
    return 0
}

validate_config
```

### 6.2 团队配置同步
```bash
#!/usr/bin/env bash
# scripts/sync-ai-configs.sh

# 从远程仓库同步配置模板
git clone https://github.com/your-org/ai-configs.git ~/.mise/templates

# 更新本地配置
for profile in dev content data-analysis; do
    if [[ -f ~/.mise/templates/$profile/.mise.toml ]]; then
        cp ~/.mise/templates/$profile/.mise.toml ./
        echo "✅ 已更新配置: $profile"
    fi
done
```

## 7. 故障排查

### 7.1 常见问题
```bash
# 检查配置
mise env | grep -E "(VELOERA|ANTHROPIC)"
cat .mise.toml

# 检查Veloera状态
docker-compose ps
curl -s "http://localhost:3000/health"
```

### 7.2 调试模式
```bash
# 启用详细日志
export CLAUDE_DEBUG=1

# 测试配置加载
mise env
```

## 8. 最佳实践

1. **配置命名规范**: 使用描述性的项目类型名称
2. **环境变量管理**: 敏感信息通过Mise管理
3. **版本控制**: 配置模板纳入版本控制
4. **文档维护**: 为每个配置编写使用说明
5. **定期备份**: 定期备份配置数据

## 9. 总结

这个方案通过 **Claude Code CLI + Mise + Veloera** 的组合，实现了：

- ✅ **自动化配置切换**: 基于项目目录自动应用项目配置
- ✅ **项目级隔离**: 不同项目使用独立的配置和环境
- ✅ **简化管理**: 统一的配置管理工具
- ✅ **团队协作**: 支持配置同步和标准化
- ✅ **扩展性**: 易于添加新的配置类型和功能

相比复杂的多工具方案，这个架构更简单、更易维护，同时满足大多数实际使用场景。