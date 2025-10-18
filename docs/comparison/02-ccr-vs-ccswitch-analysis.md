# CCR vs cc-switch 详细对比分析

## 相关工具GitHub地址

- **CCR (Claude Code Router)**: https://github.com/musistudio/claude-code-router
- **cc-switch**: https://github.com/HoBeedzc/cc-switch
- **Veloera**: https://github.com/veloera/veloera
- **Mise**: https://github.com/jdx/mise

## 1. 工具定位对比

### CCR (Claude Code Router)
- **定位**: 智能路由和格式转换服务
- **核心功能**:
  - 多Provider格式统一转换
  - 基于场景的智能路由分发
  - API Gateway功能
- **架构**: 单实例路由服务

### cc-switch
- **定位**: Claude Code配置管理工具
- **核心功能**:
  - 多配置文件创建和切换
  - 项目级配置管理
  - 模板系统
- **架构**: 配置管理器

## 2. 功能对比表

| 功能特性 | CCR | cc-switch | 备注 |
|---------|-----|-----------|------|
| **多配置支持** | ❌ 单配置文件 | ✅ 多配置文件 | cc-switch专门为此设计 |
| **项目级切换** | ❌ 需要修改配置 | ✅ 原生支持 | cc-switch的核心功能 |
| **格式转换** | ✅ OpenAI/Anthropic等 | ❌ 无转换能力 | CCR的核心价值 |
| **智能路由** | ✅ 场景级模型选择 | ❌ 无路由能力 | CCR的独特功能 |
| **MCP集成** | ❌ 不支持 | ✅ 支持MCP管理 | cc-switch更现代化 |
| **GUI界面** | ❌ CLI工具 | ✅ Web界面管理 | cc-switch用户体验更好 |
| **配置模板** | ❌ 无模板系统 | ✅ 完整模板系统 | cc-switch便于标准化 |
| **多实例运行** | ❌ 单实例设计 | ✅ 理论支持 | cc-switch更适合多实例场景 |

## 3. 路由功能分析

### CCR Router 场景
```json
{
  "Router": {
    "default": "veloera,glm-4.6",        // 默认请求
    "background": "veloera,glm-4.6",     // 后台任务
    "think": "veloera,glm-4.6",          // 推理任务
    "longContext": "veloera,glm-4-long", // 长文本(>200k)
    "webSearch": "veloera,glm-4.5",      // 网络搜索
    "image": "veloera,glm-4.5v"          // 图像理解
  }
}
```

### 替代方案分析

#### 方案A: 保持CCR路由
```
Claude Code → CCR Router → 智能分发 → 不同模型
```
**优势**: 精细化路由控制
**劣势**: 架构复杂，需要多实例支持

#### 方案B: cc-switch + MCP
```
Claude Code → MCP特殊处理 → 专用API
           ↘ 普通任务 → Veloera → 标准模型
```
**优势**: 模块化，易维护
**劣势**: 需要开发MCP服务器

#### 方案C: 简化项目级配置
```
cc-switch 项目配置 → Claude Code → Veloera → 统一模型
```
**优势**: 简单实用
**劣势**: 缺少精细化路由

## 4. 使用场景评估

### 适合使用CCR的场景
- 需要根据请求类型动态选择模型
- 有复杂的格式转换需求
- 需要统一的API Gateway功能
- 单一使用场景，不需要多配置切换

### 适合使用cc-switch的场景
- 多个项目需要不同AI配置
- 需要项目级配置隔离
- 希望有图形化管理界面
- 需要配置模板和标准化

### 混合使用场景
- 大部分时候用项目级配置(cc-switch)
- 特殊功能用MCP服务器扩展
- 格式转换交给Veloera处理

## 5. 技术实现对比

### CCR实现
```bash
# 启动CCR路由服务
ccr start

# 配置路由规则
ccr model  # 交互式选择
```

### cc-switch实现
```bash
# 创建配置
cc-switch create dev

# 切换配置
cc-switch switch dev

# Web界面管理
cc-switch serve  # http://localhost:13501
```

## 6. 维护成本对比

| 维护方面 | CCR | cc-switch |
|---------|-----|-----------|
| **配置复杂度** | 高 (路由规则) | 低 (模板化) |
| **多实例管理** | 困难 | 简单 |
| **故障排查** | 复杂 (路由层) | 简单 (配置层) |
| **扩展性** | 中等 | 高 (MCP生态) |
| **社区支持** | 小众专用 | 通用工具 |

## 7. 推荐方案

### 对于大多数用户: cc-switch
- ✅ 功能满足80%的使用场景
- ✅ 维护简单，社区支持好
- ✅ 现代化的配置管理体验

### 对于特殊需求用户: CCR
- 有复杂的格式转换需求
- 需要请求级的精细路由控制
- 对性能有极致要求

### 对于高级用户: 混合方案
```
cc-switch (配置管理) + MCP (功能扩展) + Veloera (格式统一)
```

## 8. 迁移建议

### 从CCR迁移到cc-switch
1. **评估路由需求**: 确定是否真的需要精细化路由
2. **简化配置**: 将路由规则简化为项目级配置
3. **引入MCP**: 特殊功能用MCP服务器实现
4. **渐进迁移**: 先用cc-switch管理简单场景

### 保持CCR的情况
- 确实需要多Provider格式转换
- 有复杂的业务路由逻辑
- 团队已经熟悉CCR的使用

## 9. 总结

**cc-switch是更现代化、更实用的选择**，特别是在Veloera已经提供格式转换能力的情况下。CCR的路由功能虽然在某些场景下有价值，但对于大多数用户来说过于复杂。

推荐优先考虑cc-switch方案，如果发现确实需要CCR的独特功能再进行评估。