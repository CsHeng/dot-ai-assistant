# Standalone API Gateway工具对比分析

## 相关工具GitHub地址

- **Veloera**: https://github.com/veloera/veloera
- **One-API**: https://github.com/songquanpeng/one-api 
- **New-API**: https://github.com/Calcium-Ion/new-api
- **Portkey Gateway**: https://github.com/Portkey-AI/gateway
- **LiteLLM**: https://github.com/BerriAI/litellm
- **CCR (Claude Code Router)**: https://github.com/musistudio/claude-code-router

## 1. 核心概念

### 1.1 Standalone API Gateway vs AI SDK
- Standalone API Gateway: 独立部署的API转发/代理服务，如Veloera、One-API、Portkey
- AI SDK: 需要在应用中集成的开发库，如OpenAI Python SDK

我们的需求: Standalone API Gateway - 本地部署，统一API接口

### 1.2 市场现状 (2025年10月)
AI API Gateway市场已经非常成熟，主要为个人和团队提供standalone API forward/proxy服务，支持本地独立部署，统一管理多个LLM Provider。

## 2. 评估标准

### 2.1 功能要求
- 独立部署能力
- 多Provider支持
- 统一API接口
- 特殊endpoint支持（如BigGLM coding）
- 配置管理能力

### 2.2 技术要求
- 部署复杂度
- 性能表现
- 稳定性
- 扩展性
- 特殊API兼容性

### 2.3 使用要求
- 中文支持
- Web管理界面
- 成本控制功能
- 学习成本

## 3. 主流工具对比

### 3.1 Veloera (★推荐)

技术规格
- 部署方式: Docker/Node.js
- GitHub Stars: 1k+ (新兴工具)
- 提供商支持: 主流提供商
- 特殊支持: ✅ BigGLM coding endpoint

核心优势:
- ✅ BigGLM coding计费计划支持: 原生支持 `https://open.bigmodel.cn/api/coding/paas/v4/chat/completions`
- ✅ 特殊endpoint兼容: 支持各厂商的非标API endpoint和不同计费计划
- ✅ 灵活配置: 可针对不同模型配置不同的endpoint和计费方式
- ✅ OpenAI兼容格式: 统一的OpenAI风格API接口
- ✅ 轻量级部署: 简单快速部署

部署复杂度: ⭐⭐⭐⭐ (Docker部署)

适合场景: 需要特殊endpoint和不同计费计划支持的用户，特别是BigGLM coding计划用户

---

### 3.2 One-API (songquanpeng/one-api)

技术规格
- 部署方式: Docker/Docker Compose/单可执行文件
- GitHub Stars: 15k+
- 提供商支持: 50+ 主流提供商
- 数据库: SQLite/MySQL/PostgreSQL

局限性:
- ❌ 特殊endpoint支持不足: 无法支持BigGLM coding等特殊计费计划的endpoint
- ❌ API格式转换限制: 对非标准API格式兼容性差
- ✅ Web管理界面完善: 直观的token管理和使用统计
- ✅ 中文生态最佳: 对BigGLM、BigGLM等国产模型支持完善
- ✅ 成本控制精细: 配额、计费、预算管理

部署复杂度: ⭐⭐⭐⭐ (Docker一键部署)

现状: 已被Veloera替代，主要问题是对特殊endpoint支持不足

---

### 3.3 New-API (Calcium-Ion/new-api) [One-API增强版]

技术规格
- 部署方式: Docker/Docker Compose
- GitHub Stars: 2k+
- 提供商支持: 50+ 主流提供商
- 特点: One-API的增强分支

核心优势:
- ✅ 改进的协议支持: 相比One-API有更好的API兼容性
- ✅ 持续更新: 活跃的维护和功能更新
- ✅ 向后兼容: 保持One-API的配置格式
- ⚠️ 特殊endpoint支持: 部分改进，但仍有限制

部署复杂度: ⭐⭐⭐⭐ (Docker部署)

适合场景: 从One-API迁移，需要更好兼容性的用户

---

### 3.4 Portkey Gateway

技术规格
- 部署方式: Npx/Docker/Node.js
- GitHub Stars: 8k+
- 提供商支持: 250+ (最全面)
- 性能: <1ms延迟，122kb体积

核心优势:
- ✅ 部署最简单: Npx一键启动 `npx @portkey-ai/gateway`
- ✅ 提供商覆盖最广: 250+ LLMs
- ✅ 性能最佳: 超低延迟
- ✅ 轻量级: 122kb，资源占用小
- ✅ AI Guardrails: 50+安全防护
- ⚠️ 特殊endpoint支持: 有限，主要通过标准OpenAI格式

部署复杂度: ⭐⭐⭐⭐⭐ (Npx一键启动)

适合场景: 需要最大模型覆盖和最佳性能的用户

---

### 3.5 LiteLLM (Standalone Proxy模式)

技术规格
- 部署方式: Docker Compose/命令行启动
- GitHub Stars: 12k+
- 提供商支持: 150+
- 数据库: PostgreSQL (可选)

核心优势:
- ✅ 提供商覆盖广: 150+ LLM APIs
- ✅ Web UI管理: 内置 `/ui` 管理界面
- ✅ 企业级功能: 观测性、监控、日志集成
- ✅ 数据库支持: PostgreSQL持久化存储
- ⚠️ 特殊endpoint支持: 需要自定义配置

部署复杂度: ⭐⭐⭐ (需要Docker Compose)

适合场景: 需要数据库支持和企业级功能的用户

---

## 4. 关键对比维度

### 4.1 特殊endpoint和计费计划支持对比

| 工具 | BigGLM coding计费计划 | 自定义endpoint | 配置灵活性 |
|------|----------------------|----------------|------------|
| Veloera | ✅ 原生支持 | ✅ 完全支持 | ⭐⭐⭐⭐⭐ |
| One-API | ❌ 不支持 | ⚠️ 有限支持 | ⭐⭐ |
| New-API | ⚠️ 部分支持 | ⚠️ 有限支持 | ⭐⭐⭐ |
| Portkey | ❌ 不支持 | ⚠️ 有限支持 | ⭐⭐⭐ |
| LiteLLM | ⚠️ 需自定义 | ✅ 支持 | ⭐⭐⭐⭐ |

### 4.2 综合对比

| 工具 | 部署复杂度 | 提供商数量 | 特殊endpoint/计费计划 | Web界面 | 中文支持 | 推荐指数 |
|------|------------|------------|---------------------|--------|----------|----------|
| Veloera | ⭐⭐⭐⭐ | 30+ | ✅ | ✅ | ✅ | ⭐⭐⭐⭐⭐ |
| One-API | ⭐⭐⭐⭐ | 50+ | ❌ | ✅ | ✅ | ⭐⭐ |
| New-API | ⭐⭐⭐⭐ | 50+ | ⚠️ | ✅ | ✅ | ⭐⭐⭐ |
| Portkey | ⭐⭐⭐⭐⭐ | 250+ | ❌ | ✅ | ⚠️ | ⭐⭐⭐⭐ |
| LiteLLM | ⭐⭐⭐ | 150+ | ⚠️ | ✅ | ⚠️ | ⭐⭐⭐⭐ |

## 5. 特殊功能分析

### 5.1 Claude Code Router vs Veloera

CCR独有功能 (Veloera无法替代):
- Claude Code深度集成和 `/model` 命令实时切换
- 智能场景路由 (默认/后台/思考/长上下文/搜索)
- 编程助手优化和容错处理

Veloera优势 (CCR无法替代):
- 统一Provider管理和API Key集中管理
- 特殊endpoint和不同计费计划支持，特别是BigGLM coding计费计划
- 多工具支持，不仅限于Claude Code
- 更灵活的API格式转换
- ~/.claude-code-router/config.json 示例:
   ```json
   {
     "providers": [
      // standard API, consume API credits/tokens
       {
         "name": "bigglm",
         "type": "openai",
         "baseURL": "https://open.bigmodel.cn/api/paas/v4",
         "apiKey": "your-api-key",
         "models": ["glm-4.6", "glm-4.5"]
       },
       // coding plan API, consume coding plan credits/tokens
       {
         "name": "bigglm-coding-plan",
         "type": "openai",
         "baseURL": "https://open.bigmodel.cn/api/coding/paas/v4",
         "apiKey": "your-api-key",
         "models": ["glm-4.6", "glm-4.5"]
       }
     ]
   }
   ```

### 5.2 推荐架构更新

```
Veloera (Provider汇集层) ← 管理所有API Key，支持特殊endpoint和不同计费计划
    ↓ 统一接口
Mise (项目层) ← 项目级切换
    ↓ 环境变量
Claude Code + CCR (工具层) ← Claude Code专用优化
其他CLI工具 (统一接口) ← 直接使用Veloera
```

## 6. 最终推荐

### 6.1 针对您的需求
**强烈推荐 Veloera** ⭐⭐⭐⭐⭐

理由:
- 完美支持BigGLM coding计费计划的endpoint
- 解决了One-API的核心痛点
- 保持现有工作流不变
- 部署简单，配置灵活

### 6.2 备选方案
1. New-API ⭐⭐⭐ - 如果Veloera有问题，作为备选
2. Portkey Gateway ⭐⭐⭐⭐ - 如果需要更多provider选择

### 6.3 不推荐
- **One-API**: 特殊endpoint和计费计划支持不足，已被Veloera替代

Veloera是目前唯一完美解决BigGLM coding等特殊计费计划endpoint问题的API网关，强烈推荐作为首选方案。