# 全局AI Provider/Model管理工具对比分析

## 需求概述

您需要一个全局工具来统一管理所有AI Provider/Model，实现：
- 多提供商统一接入
- 简单的模型切换
- 成本和性能管理
- 个人使用友好

## 主流工具对比分析

### 1. New-API (QuantumNous/new-api)

⭐ 强烈推荐

#### 核心特性
- 30+提供商支持: OpenAI, Azure, Anthropic, Google Gemini, DeepSeek, ByteDance, ChatGLM等
- **多格式支持**: OpenAI Responses格式、Claude Messages格式、Google Gemini格式、DeepSeek格式等
- OpenAI兼容API: 统一的API接口，支持第三方API的工具无缝接入
- 负载均衡: 多通道自动负载分配
- 故障转移: 自动切换到备用提供商
- 成本控制: 配额限制和计费管理

#### 技术规格
```yaml
部署方式:
  - Docker (推荐)
  - Docker Compose
  - 二进制部署
  - 多服务器支持

管理界面:
  - Web UI配置管理
  - Token管理
  - 使用统计
  - 用户和渠道分组

API特性:
  - OpenAI完全兼容
  - 流式响应支持
  - 自动重试机制
  - 模型映射
```

#### 优势
- ✅ 格式支持最全: 支持OpenAI、Claude、Gemini、DeepSeek等多种原生格式
- ✅ 功能完整: 支持几乎所有主流提供商
- ✅ 部署简单: Docker一键部署
- ✅ 管理方便: Web UI直观配置
- ✅ 稳定可靠: 生产级稳定性
- ✅ 个人免费: 完全开源免费
- ✅ 第三方API友好: 支持第三方API key使用各提供商服务

#### 劣势
- ❌ 需要Docker环境
- ❌ 配置相对复杂

#### 适用场景
- 需要统一管理多个AI提供商
- 希望有Web管理界面
- 需要成本控制和监控
- 需要使用多种消息格式
- 使用第三方API key访问各提供商

---

### 2. LiteLLM (BerriAI/litellm)

⭐ 适合开发者

#### 核心特性
- 100+提供商支持: 最全面的提供商覆盖
- **统一格式支持**: 使用OpenAI兼容格式作为统一接口，自动转换到各提供商原生格式
- Python SDK + Proxy Server: 灵活的集成方式
- 负载均衡: 智能流量分配
- 预算跟踪: 项目级别的成本控制
- 可观测性: 15+日志集成

#### 技术规格
```yaml
部署方式:
  - Python包安装
  - Docker部署
  - 云端托管服务

集成方式:
  - Python SDK
  - REST API Proxy
  - 环境变量配置

高级功能:
  - 异步支持
  - 流式响应
  - 虚拟密钥管理
  - 认证钩子
```

#### 优势
- ✅ 提供商最多: 支持100+提供商
- ✅ 格式转换能力强: 自动处理OpenAI到各提供商格式的转换
- ✅ 开发者友好: Python SDK易于集成
- ✅ 功能丰富: 企业级功能完备
- ✅ 性能优秀: <1ms延迟
- ✅ 第三方API友好: 支持各种第三方API key

#### 劣势
- ❌ 需要Python环境
- ❌ 配置复杂度较高
- ❌ 无Web UI (需要自己搭建)

#### 适用场景
- Python开发者
- 需要大量提供商支持
- 需要企业级功能
- 需要复杂的格式转换
- 使用多种第三方API

---

### 3. One-API (songquanpeng/one-api)

⭐ 稳定选择

#### 核心特性
- 25+提供商支持: OpenAI, Azure, Anthropic, Google Gemini, DeepSeek, ByteDance, ChatGLM等
- **多格式支持**: OpenAI格式、Claude格式、Gemini格式等
- OpenAI兼容API: 统一的API接口，支持第三方API的工具无缝接入
- 负载均衡: 多通道自动负载分配
- 故障转移: 自动切换到备用提供商
- 成本控制: 配额限制和计费管理

#### 技术规格
```yaml
部署方式:
  - Docker (推荐)
  - Docker Compose
  - 二进制部署
  - 多服务器支持

管理界面:
  - Web UI配置管理
  - Token管理
  - 使用统计
  - 用户和渠道分组

API特性:
  - OpenAI完全兼容
  - 流式响应支持
  - 自动重试机制
  - 模型映射
```

#### 优势
- ✅ 功能完整: 支持几乎所有主流提供商
- ✅ 多格式支持: 支持OpenAI、Claude、Gemini等格式
- ✅ 部署简单: Docker一键部署
- ✅ 管理方便: Web UI直观配置
- ✅ 稳定可靠: 生产级稳定性，社区成熟
- ✅ 个人免费: 完全开源免费
- ✅ 第三方API友好: 支持第三方API key

#### 劣势
- ❌ 需要Docker环境
- ❌ 配置相对复杂
- ❌ 格式支持略少于New-API

#### 适用场景
- 需要统一管理多个AI提供商
- 希望有Web管理界面
- 需要成本控制和监控
- 重视稳定性和成熟度
- 使用第三方API key

---

### 4. Portkey Gateway

⭐ 轻量级选择

#### 核心特性
- 250+ LLM支持: 最大的模型覆盖
- 超轻量: 122kb大小
- 快速部署: 2分钟Npx部署
- 高性能: <1ms延迟
- 基础安全: 50+集成防护

#### 技术规格
```yaml
部署方式:
  - Npx快速部署
  - Docker
  - 云端托管

核心功能:
  - 统一API接口
  - 自动重试/故障转移
  - 负载均衡
  - 基础日志记录

高级功能(付费):
  - 语义缓存
  - 提示管理
  - 企业级安全
```

#### 优势
- ✅ 部署最简单: Npx一键部署
- ✅ 性能最好: <1ms延迟
- ✅ 最轻量: 122kb
- ✅ 免费自托管: 基础功能免费

#### 劣势
- ❌ 高级功能需要付费
- ❌ 管理界面相对简单
- ❌ 社区相对较小
- ❌ 格式支持有限: 主要支持OpenAI兼容格式

#### 适用场景
- 需要快速部署
- 对性能要求极高
- 基础API管理需求
- 主要使用OpenAI兼容格式

---

### 5. Veloera Gateway

⭐ 项目当前选择

#### 核心特性
- 30+提供商支持: BigGLM, DeepSeek, Kimi, Gemini等
- **多格式支持**: OpenAI格式、Claude格式、Gemini格式等
- **自定义endpoint**: 支持为不同模型配置不同的API endpoint
- OpenAI兼容API: 统一的API接口
- 负载均衡: 多通道自动负载分配
- 故障转移: 自动切换到备用提供商
- 成本控制: 配额限制和计费管理

#### 技术规格
```yaml
部署方式:
  - Docker Compose (推荐)
  - Docker
  - 数据库支持: SQLite/MySQL

管理界面:
  - Web UI配置管理
  - Token管理
  - 使用统计
  - Provider配置

API特性:
  - OpenAI完全兼容
  - 流式响应支持
  - 自定义endpoint支持
  - 模型映射
```

#### 优势
- ✅ 自定义endpoint最强: 支持复杂的API配置
- ✅ 多格式支持完整: 支持各种消息格式
- ✅ 配置灵活性最高: 可为不同模型配置不同endpoint
- ✅ 部署简单: Docker Compose一键部署
- ✅ 管理方便: Web UI直观配置
- ✅ 个人免费: 开源项目
- ✅ 第三方API友好: 完全支持第三方API key

#### 劣势
- ❌ 需要Docker环境
- ❌ 需要数据库配置
- ❌ 社区相对较小

#### 适用场景
- 需要复杂的API配置
- 使用特殊endpoint的提供商
- 需要自定义配置
- 项目级统一管理
- 使用多种第三方API

---

### 6. Open WebUI

⭐ 适合本地部署

#### 核心特性
- 多模型支持: Ollama + OpenAI兼容API
- RAG功能: 内置检索增强
- 本地部署: 完全离线运行
- 用户友好: Web UI界面
- 插件系统: Pipelines扩展

#### 技术规格
```yaml
部署方式:
  - Docker
  - K8s
  - PWA支持

核心功能:
  - 多模型接入
  - 文档检索(RAG)
  - 权限管理
  - 移动端支持

特色功能:
  - 模型构建器
  - 网页浏览
  - 文件上传
  - 多语言支持
```

#### 优势
- ✅ 完全本地: 数据隐私保护
- ✅ RAG功能: 内置文档检索
- ✅ 用户体验: 界面友好
- ✅ 离线运行: 不依赖外部服务

#### 劣势
- ❌ 不是API Gateway: 主要是Chat界面
- ❌ 提供商有限: 主要支持Ollama和OpenAI兼容
- ❌ 资源占用: 需要较大本地资源

#### 适用场景
- 需要本地部署
- 重视数据隐私
- 需要RAG功能

---

## 对比总结表

| 工具 | 部署复杂度 | 提供商数量 | 格式支持 | Web UI | 自定义endpoint | 第三方API | 推荐指数 |
|------|------------|------------|----------|--------|----------------|-----------|----------|
| **New-API** | ⭐⭐⭐⭐ | 30+ | ⭐⭐⭐⭐⭐ | ✅ | ⭐⭐⭐⭐ | ✅ | ⭐⭐⭐⭐⭐ |
| **LiteLLM** | ⭐⭐ | 100+ | ⭐⭐⭐⭐ | ❌ | ⭐⭐⭐ | ✅ | ⭐⭐⭐⭐ |
| **One-API** | ⭐⭐⭐⭐ | 25+ | ⭐⭐⭐⭐ | ✅ | ⭐⭐⭐ | ✅ | ⭐⭐⭐⭐ |
| **Veloera** | ⭐⭐⭐⭐ | 30+ | ⭐⭐⭐⭐⭐ | ✅ | ⭐⭐⭐⭐⭐ | ✅ | ⭐⭐⭐⭐⭐ |
| **Portkey** | ⭐⭐⭐⭐⭐ | 250+ | ⭐⭐ | ✅ | ⭐⭐ | ✅ | ⭐⭐⭐ |
| **Open WebUI** | ⭐⭐⭐ | 有限 | ⭐⭐ | ✅ | ❌ | ⚠️ | ⭐⭐⭐ |

## 推荐方案

### 🥇 首选：New-API

**更新后的强烈推荐**：

推荐理由：
- **格式支持最全面**: 支持OpenAI、Claude、Gemini、DeepSeek等所有主流格式
- **第三方API友好**: 完全支持使用第三方API key访问各提供商
- **功能完整**: 支持您需要的所有提供商(BigGLM, DeepSeek, Kimi, Gemini等)
- **部署简单**: Docker一键部署，Web UI管理方便
- **社区活跃**: 更新频繁，兼容性好

### 🥈 同等推荐：Veloera

**项目当前选择，仍然推荐**：

推荐理由：
- **自定义endpoint最强**: 支持复杂的API配置，适合特殊需求
- **多格式支持完整**: 支持各种消息格式转换
- **配置灵活性最高**: 可为不同模型配置不同endpoint
- **项目已验证**: 当前项目正在使用，稳定可靠
- **完全免费**: 开源项目，无功能限制

### 🥉 备选：LiteLLM

**适合开发者的选择**：

推荐理由：
- **提供商最多**: 支持100+提供商
- **格式转换能力强**: 自动处理各种格式转换
- **开发者友好**: Python SDK易于集成
- **性能优秀**: <1ms延迟

**不推荐原因**：
- ❌ 无Web UI: 需要自己搭建管理界面
- ❌ 需要Python环境: 增加了部署复杂度

---

## CLI 工具兼容性分析

### 重要发现：大部分 CLI 工具都支持第三方 API

基于重新调研，**好消息**是大部分主流 CLI 工具都支持第三方 API 配置：

| CLI 工具 | 第三方API支持 | 自定义endpoint | 环境变量配置 | 推荐使用 |
|----------|---------------|----------------|-------------|----------|
| **Claude Code CLI** | ✅ | ✅ | ✅ | ⭐⭐⭐⭐⭐ |
| **Qwen CLI** | ✅ | ✅ | ✅ | ⭐⭐⭐⭐⭐ |
| **Factory AI Droid** | ✅ | ✅ | ✅ | ⭐⭐⭐⭐ |
| **Gemini CLI** | ⚠️ | ⚠️ | ⚠️ | ⭐⭐⭐ |
| **Codex CLI** | ❌ | ❌ | ❌ | ⭐ |

### 关键结论

1. **Gateway 方案完全可行**: 所有主流 Gateway (New-API, Veloera, LiteLLM) 都可以通过 OpenAI 兼容接口为这些 CLI 工具提供服务

2. **第三方 API 普遍支持**: Gemini 等提供商可以通过第三方 API key + Gateway 的方式使用

3. **统一配置成为可能**: 通过 Mise + Gateway 的组合，可以实现项目级别的统一 AI 工具管理

### 配置示例

所有支持第三方 API 的 CLI 工具都可以这样配置：

```bash
# 通过 Gateway 使用任意提供商
export OPENAI_BASE_URL="http://localhost:3000/v1"  # Gateway 地址
export OPENAI_API_KEY="sk-your-gateway-token"     # Gateway Token
export OPENAI_MODEL="gemini-pro"                   # 目标模型

# 或 Claude 格式
export ANTHROPIC_BASE_URL="http://localhost:3000/"
export ANTHROPIC_AUTH_TOKEN="sk-your-gateway-token"
```

部署示例：
```bash
# Docker Compose部署
services:
  veloera:
    restart: unless-stopped
    image: ghcr.io/veloera/veloera:latest
    container_name: veloera
    ports:
      - "3000:3000"
    volumes:
      - ./data:/data
    environment:
      - TZ=Asia/Shanghai
```

**注意**: Veloera使用数据库（SQLite/MySQL）存储配置，不是JSON配置文件。需要通过Web界面或API进行Provider配置。

使用配置：
```bash
# 配置环境变量
export OPENAI_API_KEY="your-veloera-token"
export OPENAI_BASE_URL="http://localhost:3000/v1"

# Claude CLI使用
export ANTHROPIC_AUTH_TOKEN="your-veloera-token"
export ANTHROPIC_BASE_URL="http://localhost:3000/v1"

# 所有AI工具都使用统一的配置
```

### 🥉 备选：Portkey Gateway

**不推荐原因**：
- ❌ 无法配置自定义API路径

适用场景：
- 不需要特殊endpoint的用户
- 需要最快的部署速度
- 对性能要求极高

部署示例：
```bash
# Npx快速部署
npx @portkey-ai/gateway

# 或Docker部署
docker run -p 8000:8000 portkey-ai/gateway
```

## 实施建议

### 基于Veloera的完整方案

1. 部署Veloera：
   ```bash
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

2. 配置提供商：
   - 通过Web界面或API配置Provider
   - 添加提供商：BigGLM, DeepSeek, Kimi
   - 配置不同模型的endpoint和API密钥

3. 集成到Mise：
   ```toml
   # .mise.toml
   [env]
   OPENAI_API_KEY = "sk-your-veloera-api-key"
   OPENAI_BASE_URL = "http://172.17.0.1:3000/v1"
   ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
   ANTHROPIC_BASE_URL = "http://172.17.0.1:3000/v1"
   ```

### 使用流程

```bash
# 查看所有可用模型
curl -s http://172.17.0.1:3000/v1/models | jq -r '.data[].id'

# 检查配置状态
echo "Veloera: http://172.17.0.1:3000 | API Key: configured"

# 测试API连接
curl -s http://172.17.0.1:3000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-veloera-api-key" \
  -d '{"model":"glm-4.6","messages":[{"role":"user","content":"写一个Python函数"}]}'
```

## 结论

### 🎯 重新评估后的最终推荐

基于深入调研，**您的统一 AI 工具管理方案完全可行**：

#### 🥇 **最佳选择：New-API**
- **格式支持最全面**: 支持 OpenAI、Claude、Gemini、DeepSeek 等所有主流格式
- **第三方 API 完美兼容**: 可以使用第三方 API key 访问任何提供商
- **CLI 工具兼容性最好**: 与所有支持第三方 API 的 CLI 工具完美配合
- **部署管理简单**: Docker + Web UI，易于使用

#### 🥈 **同等推荐：Veloera (继续使用)**
- **项目已验证**: 当前正在使用，稳定可靠
- **自定义配置最强**: 适合复杂的 API endpoint 需求
- **多格式支持完整**: 不存在格式兼容性问题
- **完全免费**: 开源项目，无功能限制

### 🔥 关键发现

1. **所有主流 Gateway 都支持多格式转换**: OpenAI、Claude、Gemini 等格式都可以相互转换

2. **大部分 CLI 工具支持第三方 API**: Claude Code CLI、Qwen CLI、Factory AI Droid 都可以通过 Gateway 使用任何 AI 提供商

3. **第三方 API key 普遍可用**: 包括 Gemini 在内的所有主流提供商都可以通过第三方 API key 使用

4. **统一配置完全实现**: Mise + Gateway 的组合可以完美解决您的 AI 工具管理需求

### 🚀 推荐架构

```
项目目录 → Mise 环境配置 → CLI 工具 → Gateway (New-API/Veloera) → AI 提供商
```

这个架构能够实现：
- ✅ 全局 Provider/Model 管理
- ✅ 简单的模型切换
- ✅ 成本和性能管理
- ✅ 个人使用友好
- ✅ 项目级配置隔离
- ✅ 第三方 API 支持
- ✅ 多格式兼容

**您的 AI 工具统一管理方案不仅可行，而且有多种优秀的选择！**