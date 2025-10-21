# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AI tools management system with unified configuration architecture:

**Primary Pattern**: Multi-Tool CLI + Mise + Veloera Gateway
**Alternative**: Claude Code + CCR + Veloera (for specialized routing)

## Quick Start

### 1. Setup Configuration

```bash
# Copy configuration template
cp .mise.toml.sample .mise.toml

# Edit with your API keys
vim .mise.toml
```

### 2. Start Services

```bash
# Start Veloera gateway
docker-compose up -d

# Apply environment configuration
mise env
```

### 3. Test Tools

```bash
# Test Claude Code
claude "Hello, test connection"

# Test other tools
qwen "你好，测试连接"
codex "Write a Python function"
```

## Configuration Files

### Mise Configuration
- `.mise.toml.sample`: Template with all options (committed)
- `.mise.toml`: Personal config with API keys (private, git ignored)

### CCR Configuration
- `~/.claude-code-router/config.json`: CCR settings (single instance)

## Configuration Template

```toml
[env]
# Global AI settings
API_TIMEOUT_MS = "3000000"
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"

# Claude Code model settings (optional)
#ANTHROPIC_DEFAULT_HAIKU_MODEL = "glm-4.5-air"
#ANTHROPIC_DEFAULT_SONNET_MODEL = "glm-4.6"

# Claude Code API configuration
ANTHROPIC_BASE_URL = "http://10.1.1.11:3000/"
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"

# Qwen CLI (OpenAI-compatible)
OPENAI_BASE_URL = "http://10.1.1.11:3000/v1"
OPENAI_API_KEY = "sk-your-veloera-api-key"
OPENAI_MODEL = "glm-4.6"  # 必须指定模型
```

## Common Commands

### Veloera Management
```bash
docker-compose up -d          # Start services
docker-compose ps             # Check status
docker-compose logs veloera   # View logs
```

### Configuration Testing
```bash
mise env | grep -E "(ANTHROPIC_|OPENAI_)"  # View AI config
curl -s "http://localhost:3000/api/status"  # Test Veloera
```

### CCR Management (Optional)
```bash
ccr start      # Start CCR server
ccr model      # Interactive model selection
ccr status     # Check CCR status
```

## Architecture Patterns

### General Multi-Tool Pattern (Primary)
```
Project Directory → Mise Env → Official CLI → Veloera → AI Providers
```

### Claude Code Specific Pattern (Alternative)
```
Claude Code → CCR → Veloera → AI Providers
```

## Key Points

- **Single Configuration**: One `.mise.toml` file contains all tool settings
- **Project-Level**: Mise automatically switches configuration based on directory
- **Universal Gateway**: Veloera provides unified API access and billing
- **Tool Agnostic**: Supports Claude CLI, Qwen CLI, Codex CLI, and more
- **Simple Setup**: Copy template, edit keys, start services

## Document Structure

- `README.md`: Project overview and quick start
- `docs/analysis/`: Requirements and current state
- `docs/solution/`: Solution specifications
- `docs/architecture/`: Architecture diagrams
- `docs/guides/`: Implementation guides

## Provider Support

Veloera supports multiple AI providers:
- BigGLM (GLM-4.6, GLM-4.5 with billing plans)
- DeepSeek (deepseek-chat)
- Kimi (moonshot-v1-8k)
- Additional providers via environment variables