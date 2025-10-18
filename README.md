# AI工具统一管理系统

## 项目概述

本项目旨在设计并实现一个基于**Claude Code CLI + Mise + Veloera**的AI工具统一管理系统，解决Provider分散、配置复杂的痛点。支持多种CLI工具，包括Claude CLI、Qwen CLI、OpenAI Codex CLI等标准格式的AI CLI工具。

## 🎯 最终选型

**主要架构**: 通用CLI工具 + Mise + Veloera (三层智能架构) ⭐
**备选方案**: Claude Code + CCR + Mise + Veloera (四层智能路由架构)

## 核心目标

- 部署Veloera作为智能API转换和路由枢纽，统一管理API Key和计费计划
- 通过Mise实现项目级配置切换，不同目录使用不同的模型和工具配置
- 支持多种CLI工具：Claude CLI、Qwen CLI、OpenAI Codex CLI等标准格式工具
- 支持BigGLM coding等不同计费计划
- 一处配置，处处使用，大幅减少配置维护工作量

## 文档结构

### 📋 需求分析
- `docs/analysis/01-requirements.md` - 核心需求总结
- `docs/analysis/02-gateway-analysis.md` - 全局AI网关分析

### 🔍 工具对比
- `docs/comparison/01-api-gateway-comparison.md` - API Gateway工具对比分析

### 🏗️ 解决方案
- `docs/solution/00-final-claude-mise-veloera-solution.md` - **最终方案** ⭐
- `docs/solution/01-veloera-solution.md` - Veloera核心解决方案
- `docs/solution/02-hybrid-solution.md` - 混合架构方案
- `docs/solution/03-practical-config.md` - CCR + Mise 备选方案
- `docs/solution/04-corrected-practical.md` - 修正后的实用方案

### 📐 架构设计
- `docs/architecture/01-current-architecture.puml` - **当前架构：现状分析 + 解决方案** ⭐
- `docs/architecture/02-veloera-solution-architecture.puml` - Veloera方案架构
- `docs/architecture/03-alternative-ccr-veloera-architecture.puml` - CCR备选架构
- `docs/architecture/04-dual-track-architecture.puml` - 双轨制架构

### 📖 实施指南
- `docs/guides/01-implementation-guide.md` - Veloera实施指导

## 推荐阅读顺序

按照现状分析 → 工具对比 → 架构设计 → 解决方案 → 实施指导的逻辑顺序：

1. 需求分析: `docs/analysis/01-requirements.md` - 理解当前环境现状和管理痛点
2. 工具对比: `docs/comparison/01-api-gateway-comparison.md` - 了解主流API Gateway工具对比
3. **最终方案**: `docs/solution/00-final-claude-mise-veloera-solution.md` - **通用CLI + Mise + Veloera 完整方案** ⭐
4. **当前架构**: `docs/architecture/01-current-architecture.puml` - **现状分析 + 解决方案架构图** ⭐
5. 其他方案参考:
   - `docs/architecture/03-alternative-ccr-veloera-architecture.puml` - CCR备选架构
6. 实施指导: `docs/guides/01-implementation-guide.md` - 详细的分阶段实施步骤

## 核心方案

### 最终架构概览

```
通用CLI工具 → Mise (项目级配置) → Veloera (Provider汇集) → 各Provider APIs
                   ↑                           ↑                              ↑
           环境变量自动切换               全局配置                        计费计划支持
           不同项目不同工具              Web界面                       API Key管理
```

### 三层核心架构

1. **工具层 (通用CLI工具)**: 各种AI编程工具(Claude/Qwen/Codex等)，通过环境变量自动获取项目配置
2. **管理层 (Mise)**: 项目级环境管理，根据目录自动切换AI配置和工具偏好
3. **汇集层 (Veloera)**: Provider统一管理、API Key集中、计费计划支持

### 关键优势

- ✅ **极简架构**: 三层清晰，无额外复杂性
- ✅ **项目级切换**: Mise根据不同项目目录自动切换AI工具和配置 (claude, qwen, codex等)
- ✅ **自动配置**: 通用CLI工具通过环境变量自动使用项目的工具偏好
- ✅ **计费计划支持**: Veloera完美支持BigGLM coding等不同计费方式
- ✅ **配置集中**: 一处管理API Key，处处使用，大幅减少维护工作量

### 备选方案 (如果需要智能路由)

**四层架构**: 通用CLI工具 → CCR → Mise → Veloera → Provider APIs
- **优势**: 智能场景路由、模型间自动切换、高级格式转换
- **劣势**: 增加复杂性、需要维护多个CCR配置文件

## 快速开始

1. **理解需求**: 阅读 `docs/analysis/01-requirements.md`
2. **查看最终方案**:
   - `docs/solution/00-final-claude-mise-veloera-solution.md` - **完整实施方案** ⭐
   - `docs/architecture/01-current-architecture.puml` - **当前架构图** ⭐
3. **开始实施**: 按照最终方案文档执行三阶段实施

## 技术栈

- **通用CLI工具**: Claude CLI, Qwen CLI, OpenAI Codex CLI等多种AI编程工具
- **Mise**: 项目级环境管理，配置自动切换
- **Veloera**: Provider汇集层，API Key和计费计划管理
- **Docker**: Veloera部署
- **PlantUML**: 架构图设计

## 联系信息

如有问题或建议，请查看相关文档或提交Issue。