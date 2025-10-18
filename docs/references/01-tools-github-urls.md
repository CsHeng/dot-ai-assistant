# AI工具GitHub地址汇总

## 配置管理和切换工具

### cc-switch
- **GitHub**: https://github.com/HoBeedzc/cc-switch
- **描述**: Claude Code配置管理工具，支持多配置文件切换
- **功能**: 创建、切换、管理Claude Code配置，提供Web界面

### CCR (Claude Code Router)
- **GitHub**: https://github.com/musistudio/claude-code-router
- **描述**: Claude Code智能路由和格式转换服务
- **功能**: 多Provider格式统一、智能场景路由、API Gateway

### CC Mate
- **官网**: https://randynamic.org/ccmate
- **描述**: Claude Code图形化管理界面
- **功能**: GUI配置管理、MCP服务器管理、令牌监控

## API Gateway工具

### Veloera
- **GitHub**: https://github.com/veloera/veloera
- **描述**: 优秀的AI API网关系统 (new-api增强版)
- **特点**: 原生Anthropic格式支持、特殊endpoint支持、高性能

### New-API
- **GitHub**: https://github.com/Calcium-Ion/new-api
- **描述**: One-API的增强分支
- **特点**: 改进的协议支持、持续更新、向后兼容

### One-API 
- **GitHub**: https://github.com/songquanpeng/one-api
- **描述**: 经典的AI API网关
- **状态**: 已被Veloera等工具替代

### Portkey Gateway
- **GitHub**: https://github.com/Portkey-AI/gateway
- **描述**: 企业级AI网关
- **特点**: 250+ LLMs支持、超低延迟、AI Guardrails

### LiteLLM
- **GitHub**: https://github.com/BerriAI/litellm
- **描述**: 统一的LLM API接口
- **特点**: 150+ providers、企业级功能、数据库支持

## 环境管理工具

### Mise
- **GitHub**: https://github.com/jdx/mise
- **描述**: 现代化的开发环境管理工具
- **功能**: 多语言版本管理、项目级环境变量、任务运行

## MCP服务器

### Context7
- **描述**: 文档读取和上下文管理MCP服务器
- **用途**: 增强Claude Code的文档理解能力

### DeepWiki
- **描述**: 知识库和文档集成MCP服务器
- **用途**: 集成wiki和知识库到AI工作流

## 官方工具

### Claude Code
- **官网**: https://claude.ai/code
- **描述**: Anthropic官方AI编程助手
- **类型**: 商业工具，非开源

### Anthropic SDK
- **GitHub**: https://github.com/anthropics/anthropic-sdk-python
- **描述**: Anthropic官方Python SDK
- **用途**: 集成Claude API到Python应用

## 相关项目

### Docker Compose配置示例
- **仓库**: 本项目 (`/Users/csheng/workspace/playground/dot-ai-assistants`)
- **描述**: 统一AI工具管理系统的完整实现
- **包含**: Veloera部署、Mise配置、直接CLI工具集成等

## 工具选择建议

### 主要推荐组合
1. **直接CLI工具 + Mise + Veloera**
   - 最适合项目级配置切换
   - 通过环境变量管理配置
   - 简化的AI工具管理方案

2. **Veloera单独使用**
   - 仅需API统一管理时
   - 多工具集成场景

### 特殊场景选择
1. **需要企业级功能**: Portkey Gateway
2. **需要数据库支持**: LiteLLM
3. **从One-API迁移**: New-API或Veloera

## 重要配置发现

### Veloera + BigGLM Anthropic协议 (2025-10-16)

**关键配置**：
- **Veloera地址**: `http://192.168.8.100:3000/` (仅服务器地址，不包含路径)
- **客户端配置**: BASE_URL设置只包含服务器地址
- **实际请求**: Claude Code自动追加 `/v1/messages` 路径
- **完整URL**: `http://192.168.8.100:3000/v1/messages`
- **BigGLM端点**: 使用BigGLM的Anthropic协议端点 `https://open.bigmodel.cn/api/paas/v4`
- **模型映射**:
  ```json
  {
    "claude-sonnet-4-5-20250929": "glm-4-6",
    "claude-3-5-haiku-20241022": "glm-4.5-air",
    "claude-opus-4-20250514": "glm-4.6"
  }
  ```
- **协议**: Anthropic Messages API格式
- **完美兼容**: Claude Code原生支持，无需修改

## 更新日志

- 2025-10-16: 初始版本，包含主要工具GitHub地址
- 2025-10-16: 添加Veloera + BigGLM Anthropic协议配置指南
- 持续更新中...