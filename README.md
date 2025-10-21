# AI工具统一管理系统

## 项目概述

本项目旨在设计并实现一个基于**CLI工具 + Mise + AI Gateway**的统一管理系统，解决Provider分散、配置复杂的痛点。支持多种主流AI Gateway（New-API、Veloera、LiteLLM等）和CLI工具（Claude Code CLI、Qwen CLI、Factory AI Droid等）。

## 🎯 核心特性

- **单一配置文件**: 使用 `.mise.toml.sample` 模板，复制为 `.mise.toml` 即可使用
- **多工具支持**: Claude Code、Qwen CLI 等支持第三方API的工具统一管理
- **项目级环境**: 通过 Mise 实现不同项目目录的配置自动切换
- **API网关**: Veloera 作为统一的Provider管理和计费中心
- **极简部署**: 一个命令启动所有服务

## 🚀 快速开始

### 1. 配置设置

```bash
# 复制配置模板
cp .mise.toml.sample .mise.toml

# 编辑配置文件，添加你的API密钥
vim .mise.toml
```

### 2. 启动服务

```bash
# 启动 Veloera 网关
docker-compose up -d

# 验证服务状态
docker-compose ps
```

### 3. 开始使用

```bash
# 应用配置
mise env

# 测试 Claude Code
claude "Hello, test connection"

# 测试其他工具
OPENAI_MODEL="glm-4.6" qwen "你好，测试连接"

# 注意：仅支持官方API的工具需要独立使用
# codex "Write a Python function"  # 需要官方登录，无法使用Veloera
```

## 📁 配置文件结构

```
.mise.toml.sample    # 配置模板 (提交到版本控制)
.mise.toml          # 你的个人配置 (包含API密钥，不提交)
```

### 配置示例

```toml
[env]
# 全局设置
API_TIMEOUT_MS = "3000000"
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"

# Claude Code 配置
ANTHROPIC_BASE_URL = "http://10.1.1.11:3000/"
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"

# Qwen CLI 配置 (OpenAI兼容)
OPENAI_BASE_URL = "http://10.1.1.11:3000/v1"
OPENAI_API_KEY = "sk-your-veloera-api-key"
OPENAI_MODEL = "glm-4.6"
```

## 🏗️ 架构概览

```
项目目录 (支持第三方API的CLI工具)
    ↓
Mise 环境管理 (自动切换配置)
    ↓
AI Gateway (New-API/Veloera/LiteLLM等)
    ↓
各大AI提供商 (BigGLM/DeepSeek/Kimi/Gemini等)

重要发现：大部分CLI工具都支持第三方API配置！
包括：Claude Code CLI、Qwen CLI、Factory AI Droid等
仅少数工具如Codex CLI需要独立使用官方API
```

## 📚 文档结构

### 📋 需求分析 (Analysis)
- `docs/analysis/01-requirements.md` - 核心需求总结
- `docs/analysis/02-gateway-analysis.md` - 全局AI网关分析
- `docs/analysis/03-gateway-architecture.md` - API网关架构分析
- `docs/analysis/04-cli-agent-protocol-compatibility.md` - CLI Agent协议兼容性分析
- `docs/analysis/05-ccr-role-boundary.md` - CCR角色边界分析

### 🔍 工具对比 (Comparison)
- `docs/comparison/01-api-gateway-comparison.md` - API Gateway工具对比分析
- `docs/comparison/02-ccr-vs-ccswitch-analysis.md` - CCR vs CCSwitch对比分析

### 🏗️ 解决方案 (Solutions)
- `docs/solution/00-final-claude-mise-veloera-solution.md` - **最终方案** ⭐
- `docs/solution/01-veloera-solution.md` - Veloera核心解决方案
- `docs/solution/02-ccswitch-mise-claude-solution.md` - CCSwitch + Mise + Claude方案
- `docs/solution/03-practical-config.md` - CCR + Mise 备选方案
- `docs/solution/04-corrected-practical.md` - 修正后的实用方案

### 📐 架构设计 (Architecture)
- `docs/architecture/01-current-architecture.puml` - **当前架构：现状分析 + 解决方案** ⭐
- `docs/architecture/02-veloera-solution-architecture.puml` - Veloera方案架构
- `docs/architecture/03-alternative-ccr-veloera-architecture.puml` - CCR备选架构
- `docs/architecture/04-dual-track-architecture.puml` - 双轨制架构

### 📖 实施指南 (Guides)
- `docs/guides/01-implementation-guide.md` - Veloera实施指导
- `docs/guides/02-ccr-mise-veloera-guide.md` - CCR + Mise + Veloera配置指南
- `docs/guides/03-veloera-bigglm-anthropic-config.md` - Veloera BigGLM Anthropic配置指南
- `docs/guides/04-mise-project-configs.md` - Mise项目配置指南
- `docs/guides/debug-gateway.md` - **Gateway 调试指南** ⭐ (适用于所有主流 Gateway)

### 📚 参考资料 (References)
- `docs/references/01-tools-github-urls.md` - 工具GitHub URL汇总

## 🎯 推荐阅读顺序

### 🔰 新手入门顺序
1. **需求分析**: `docs/analysis/01-requirements.md` - 理解当前痛点
2. **最终方案**: `docs/solution/00-final-claude-mise-veloera-solution.md` - 完整解决方案 ⭐
3. **架构图**: `docs/architecture/01-current-architecture.puml` - 可视化架构 ⭐
4. **快速开始**: 直接使用本页顶部的快速开始指南

### 🔧 深度研究顺序
1. **所有需求分析**: `docs/analysis/` - 全面了解需求和技术细节
2. **工具对比**: `docs/comparison/` - 了解各工具优缺点
3. **解决方案**: `docs/solution/` - 研究所有可能的解决方案
4. **架构设计**: `docs/architecture/` - 深入理解架构设计
5. **实施指南**: `docs/guides/` - 详细实施步骤和配置

### 📋 特定场景阅读
- **CCR集成**: `docs/guides/02-ccr-mise-veloera-guide.md`
- **BigGLM配置**: `docs/guides/03-veloera-bigglm-anthropic-config.md`
- **项目配置**: `docs/guides/04-mise-project-configs.md`
- **Gateway调试**: `docs/guides/debug-gateway.md` ⭐ (连接问题、格式兼容性等)
- **工具URL**: `docs/references/01-tools-github-urls.md`

## 🔧 核心优势

- ✅ **极简配置**: 一个模板文件，一键复制使用
- ✅ **多工具统一**: Claude、Qwen 等支持第三方API的工具统一管理
- ✅ **项目级切换**: 不同目录自动使用不同工具和配置
- ✅ **API集中管理**: Veloera统一管理所有Provider和计费
- ✅ **开箱即用**: 3分钟完成配置，立即开始使用

## 🛠️ 技术栈

- **CLI工具**: Claude CLI, Qwen CLI (支持第三方API)
- **其他工具**: OpenAI Codex CLI, Gemini CLI (仅支持官方API，需独立使用)
- **环境管理**: Mise (项目级配置自动切换)
- **API网关**: Veloera (Provider汇集和计费管理)
- **容器化**: Docker (一键部署)
- **配置管理**: TOML格式 (简洁易读)

## 📞 支持与反馈

如有问题或建议，请查看相关文档或提交Issue。