# CCR 角色定位与架构边界

## 🎯 CCR 的正确定位

**CCR 是 Claude Code 的专用路由工具，不参与通用多工具架构**

## 📋 CCR 的核心职责

### 专用功能
1. **Claude Code 协议处理**: 处理 Claude Code CLI 的特殊协议需求
2. **智能路由**: 基于 Claude Code 的使用场景进行模型选择
3. **格式转换**: 将标准格式转换为 Claude Code 需要的特定格式
4. **独立运行**: 作为独立服务，不影响其他 CLI 工具

### 与其他工具的关系
- **独立于通用架构**: CCR 不属于通用多工具架构的一部分
- **Claude Code 专用**: 仅服务于 Claude Code CLI 的特殊需求
- **不参与项目切换**: 项目级工具选择通过 Mise 直接管理
- **单实例运行**: 使用单一默认配置，不支持配置文件切换

## 🔄 架构边界清晰化

### CCR 影响范围
```
Claude Code CLI → CCR → Veloera → AI Providers
     ↑              ↑         ↑
  特殊协议      智能路由    Provider网关
```

### 通用工具架构
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

## 🚫 CCR 不涉及的领域

### 项目级管理
- ❌ 项目间工具切换：由 Mise 直接管理
- ❌ 环境变量配置：由 Mise 处理
- ❌ CLI 工具选择：由项目需求决定

### 通用工具支持
- ❌ claude CLI：直接连接 Veloera
- ❌ codex CLI：直接连接 Veloera
- ❌ qwen CLI：直接连接 Veloera
- ❌ 其他官方 CLI 工具：直接连接 Veloera

### 配置管理
- ❌ 多配置文件支持：仅使用默认配置
- ❌ 配置热切换：不支持运行时配置切换
- ❌ 项目级配置：不参与项目配置管理

## ✅ CCR 的具体使用场景

### 适用场景
1. **Claude Code 开发**: 使用 Claude Code CLI 进行开发时
2. **特殊协议需求**: 需要 Claude Code 特有的协议处理时
3. **智能模型选择**: 基于 Claude Code 使用场景自动选择模型
4. **CC 生态集成**: 需要与 Claude Code 生态深度集成时

### 配置示例
```json
{
  "LOG": true,
  "LOG_LEVEL": "debug",
  "HOST": "127.0.0.1",
  "PORT": 3456,
  "APIKEY": "your-secret-key",
  "Providers": [
    {
      "name": "veloera",
      "api_base_url": "http://10.1.1.11:3000/v1/chat/completions",
      "api_key": "sk-your-veloera-api-key",
      "models": ["glm-4.5", "glm-4.5-air", "glm-4.5v", "glm-4-long", "glm-4.6"],
      "transformer": { "use": ["OpenAI"] }
    }
  ],
  "Router": {
    "default": "veloera,glm-4.5",
    "background": "veloera,glm-4.5",
    "think": "veloera,glm-4.6",
    "longContext": "veloera,glm-4-long",
    "longContextThreshold": 200000,
    "webSearch": "veloera,glm-4.5",
    "image": "veloera,glm-4.5v"
  }
}
```

## 🔧 CCR 操作命令

### 基础操作
```bash
# 启动 CCR
ccr start

# 检查状态
ccr status

# 交互式模型选择
ccr model

# 停止 CCR
ccr stop
```

### 与 Claude Code 配合使用
```bash
# 确保 CCR 运行
ccr start

# 使用 Claude Code
claude "编写一个 React 组件"

# 切换模型 (可选)
ccr model  # 交互式选择
```

## 🎯 设计原则

### 职责单一
- CCR 专注于 Claude Code 的特殊需求
- 不承担通用工具管理的职责
- 保持架构边界的清晰性

### 独立性
- CCR 可以独立安装、配置、运行
- 不影响其他 CLI 工具的正常工作
- 不参与项目级的环境管理

### 专用性
- 仅在需要 Claude Code 特殊功能时使用
- 不强制所有项目都使用 CCR
- 保持工具选择的灵活性

## 📝 使用建议

### 何时使用 CCR
- 主要使用 Claude Code CLI 进行开发
- 需要 Claude Code 的智能路由功能
- 需要 CC 特有的协议转换

### 何时直接使用 Veloera
- 主要使用其他 CLI 工具（codex、claude、qwen）
- 需要在不同项目间切换工具
- 需要项目级的环境管理

### 混合使用策略
- Claude Code 项目：Claude Code + CCR + Veloera
- 其他项目：Mise + 官方 CLI + Veloera
- 根据项目需求选择合适的工具组合

## 🔍 故障排查

### CCR 相关问题
```bash
# 检查 CCR 状态
ccr status

# 查看 CCR 日志
ccr logs

# 重启 CCR
ccr restart

# 检查配置文件
cat ~/.claude-code-router/config.json
```

### 与其他工具的冲突
- CCR 默认端口 3456 确保不被占用
- 确认其他工具不尝试使用 CCR 协议
- 检查环境变量配置是否正确

通过明确 CCR 的角色边界，我们可以确保整个 AI 工具架构的清晰性和可维护性！