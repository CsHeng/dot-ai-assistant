# AI工具统一管理需求

## 1. 环境现状

### 1.1 已安装的AI工具

**Agent CLI (TUI - Terminal UI)**:
- Claude Code CLI (`@anthropic-ai/claude-code`) - 主要开发工具，支持第三方API配置 (ANTHROPIC_AUTH_TOKEN + ANTHROPIC_BASE_URL)
- OpenCode - 开源编程Agent，终端直接执行
- Qwen CLI (通过brew安装) - 备用工具，支持第三方API配置 (OPENAI_API_KEY + OPENAI_BASE_URL)
- Gemini CLI (通过brew安装) - 测试工具，仅支持官方API (GEMINI_API_KEY)
- Factory AI CLI (`droid`) - 端到端开发代理，直接在终端中操作
- OpenAI Codex CLI - OpenAI官方编程Agent，仅支持官方登录认证

**Agent IDE (GUI - Graphical UI)**:
- Cursor - AI编程IDE
- Trae - AI编程IDE
- Windsurf - AI编程IDE
- Kiro - 独立AI编程IDE
- VS Code with plugins
  - Kilo Code
  - Roo Code
  - Cline
  - GitHub Copilot & Chat
  - Augment Code

**管理工具**:
- Claude Code Router (`@musistudio/claude-code-router`) - Provider/Model路由管理

### 1.2 使用模式

双轨制架构:
- 通用多工具模式: 项目级环境管理 + 各种官方CLI工具 (claude/codex/qwen/gemini) + Veloera Provider网关
- Claude Code专用模式: Claude Code + Claude Code Router + Veloera后端

实际工作流程:
- 不同项目通过 Mise 设置环境变量，自动选择合适的上游Provider和模型
- 各CLI工具使用其原生协议(OpenAI/Anthropic及其兼容格式)直接连接Veloera
- CCR 仅作为 Claude Code 的独立配套工具，处理CC特殊的协议转换需求

## 2. 管理痛点

### 2.1 Provider管理痛点
- 分散的API Key管理: 一堆环境变量，无法知道全局都有什么key和使用情况
- Provider更换困难: 某个provider换个key，都要改一堆项目配置
- 使用情况不透明: 无法统计各provider的使用情况和成本
- 缺乏统一接口: 每个provider都有不同的API格式和认证方式

### 2.2 配置管理痛点
- 当前有 `~/.claude/` 下的rules配置作为基准
- 使用 `~/.claude/sync-rules.sh` 手动同步到各工具
- 不同Agent CLI、不同IDE的Agent配置麻烦
- 项目间工具选择和模型切换缺乏自动化管理

## 3. 核心诉求
- 部署独立的HTTP API转发服务（Veloera），汇集多个Provider
- 通过Mise实现项目级环境管理，自动选择合适的工具和配置
- Veloera自动转换不同Provider的API格式，支持特殊计费计划如BigGLM coding
- 进入不同项目目录自动切换合适的工具/model/provider
- 能够查看所有provider的使用情况和统计
- 一处配置API Key，各种工具通过各自原生协议使用

## 4. 具体要解决的问题
- Provider统一汇集: 通过Veloera统一管理BigGLM、DeepSeek、Kimi等
- API Key集中管理: 一处配置，各种工具通过各自原生协议使用
- 项目隔离: 不同项目使用不同的AI工具和配置
- 工具分离: 明确区分通用CLI工具和Claude Code专用工具的使用场景
- 规则同步: 统一管理各工具的rules配置
- IDE集成: Cursor、VS Code Copilot、Kiro等IDE的Agent配置
- CCR定位: 明确CCR仅用于Claude Code的特殊协议处理，不影响其他工具

## 5. 工具分析

### 5.1 工具角色分析
- **Veloera**: Provider汇集网关，提供API转发/改写功能，支持BigGLM coding等特殊计费计划，各种工具通过各自原生协议连接
- **CCR**: Claude Code专用路由器，仅处理CC特殊协议转换，独立运行
- **Mise**: 项目级环境管理，实现工具选择和配置切换
- **通用CLI工具**: claude, codex、qwen等使用原生协议直接连接Veloera
- **Claude Code**: 通过CCR连接Veloera，使用专门的协议转换
- **Agent IDE**: Cursor、VS Code等IDE中的Agent配置

### 5.2 核心目标
- Provider汇集: 通过Veloera汇集多个Provider，支持各种原生协议接入
- API转发/改写: 将外部Provider API转换为各种工具需要的格式，支持特殊计费计划
- 项目级管理: 通过mise管理不同目录使用不同的工具和model
- 工具分离: 通用CLI工具和Claude Code使用不同的接入方式
- 协议保持: 各工具使用其原生协议，通过Veloera进行格式转换
- CCR独立: CCR作为Claude Code的专用工具，不影响其他工具的工作流程

## 6. 关键约束
- 保持现有工作方式：Claude Code CLI + CCR 继续独立使用
- 通过Veloera统一汇集层管理所有Provider
- 通过mise环境变量实现项目级工具和配置切换
- 明确工具边界：CCR仅用于Claude Code，不影响其他CLI工具
- 减少手动配置，提高自动化程度

## 7. 架构原则
- 工具分离: 通用CLI工具和Claude Code使用不同的接入路径
- 协议保持: 各工具使用原生协议，避免强制统一
- 项目自治: 每个项目可以选择最适合的工具和配置
- Provider统一: 所有Provider通过Veloera统一管理和访问
- 配置集中: API Key等敏感信息在Veloera集中管理

帮我分析思考，规划如何实现或重构，以便更好的维护
