# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This project documents a unified AI tools management architecture with two main usage patterns:

1. **General Multi-Tool Architecture**: Project-level environment management through Mise + various official CLI tools (claude/qwen/codex) + Veloera as universal provider gateway
2. **Claude Code Specific Architecture**: Claude Code + CCR for specialized routing and format conversion + Veloera backend

## Core Architecture

### General Multi-Tool Pattern (Primary)
```
Project A (claude)    Project B (qwen)    Project C (codex)
       ↓                   ↓                   ↓
    Mise Env           Mise Env           Mise Env
       ↓                   ↓                   ↓
  Official CLI       Official CLI       Official CLI
       ↓                   ↓                   ↓
              Veloera (Provider Gateway + Format Conversion)
                       ↓
                Multiple AI Providers
```

### Claude Code Specific Pattern (Specialized)
```
Claude Code → CCR (Specialized Routing/Conversion) → Veloera → AI Providers
```

**Key Points**:
- CCR is independent and only used with Claude Code for CC-specific protocol handling
- Other tools use their native protocols directly with Veloera
- Mise manages project-level environments for all tools
- Veloera provides unified provider access and format conversion

## Key Commands

### Veloera Management
```bash
# Start Veloera service
docker-compose up -d

# Check service status
docker-compose ps

# View logs
docker-compose logs veloera

# Access web interface (if available)
open http://localhost:3000
```

### CCR Management
```bash
# Start CCR server
ccr start

# Check CCR status
ccr status

# Interactive model selection
ccr model
```

### Mise Environment Management
```bash
# View current AI configuration
mise env | grep -E "(ANTHROPIC_|OPENAI_|API_|BASE_URL|TIMEOUT)"

# Apply project configuration
mise env

# Test current setup with preferred CLI tool
claude "Test connection"
```

### General CLI Tools (claude/qwen/codex)
```bash
# In project with specific tool configuration
claude "Your prompt here"       # For claude projects
qwen "Your prompt here"         # For qwen projects
codex "Your prompt here"        # For codex projects
```

### Configuration Testing
```bash
# Test Veloera connectivity
curl -s "http://localhost:3000/api/status"

# Test CCR + Veloera integration
ccr code "Hello, test connection"

# Check project configuration
cd <project-dir> && mise env
```

## Document Structure

- `README.md`: Project overview and quick start guide
- `docs/analysis/`: Requirements analysis and current state assessment
- `docs/comparison/`: API Gateway tool comparisons
- `docs/solution/`: Detailed solution specifications (General CLI + Mise + Veloera based)
- `docs/architecture/`: PlantUML architecture diagrams
- `docs/guides/`: Step-by-step implementation guide
- `archive/`: Previous iterations and reference materials

## Implementation Workflow

### General Multi-Tool Setup
1. **Setup Phase**: Deploy Veloera container with environment variables
2. **Configuration Phase**: Set up Mise project-level environments for different tools
3. **Integration Phase**: Configure various CLI tools to use Veloera endpoint
4. **Testing Phase**: Verify connectivity for each tool/project combination
5. **Maintenance Phase**: Regular backups, monitoring, and updates

### Claude Code Specific Setup (Optional)
1. **Setup Phase**: Ensure Veloera is running
2. **Configuration Phase**: Set up CCR single instance configuration
3. **Integration Phase**: Configure Claude Code to use CCR
4. **Testing Phase**: Verify CCR + Veloera integration

## Configuration Files

### Veloera Configuration
- `docker-compose.yml`: Veloera service definition
- `.env`: Veloera environment variables
- `./data/`: Veloera persistent data directory

### CCR Configuration Files
- `~/.claude-code-router/config.json`: CCR configuration (single instance)

### Mise Configuration Files
- `.mise.toml`: Project-specific AI configuration (committed to version control)
- `.mise.local.toml.example`: Template file for personal configuration (committed)
- `.mise.local.toml`: Personal API tokens and private settings (created from example, not committed)
- `~/.config/mise/config.toml`: Global Mise configuration

## Provider Configuration

The system supports multiple AI providers through Veloera:
- BigGLM (GLM-4.6, GLM-4.5 with regular and coding billing plans)
- DeepSeek (deepseek-chat)
- Kimi (moonshot-v1-8k)
- Additional providers can be added via Veloera environment variables

## Project Types and Tool Preferences

### General Multi-Tool Projects

#### Development Projects
- **Preferred Tool**: Claude (for development)
- **Mise Configuration**: Set appropriate API endpoints and keys

#### Content Creation Projects
- **Preferred Tool**: Qwen (for content generation)
- **Mise Configuration**: Set OpenAI-compatible environment variables

#### Data Analysis Projects
- **Preferred Tool**: Qwen (for analytical tasks)
- **Mise Configuration**: OpenAI-compatible settings with extended timeout

#### General Projects
- **Preferred Tool**: Any CLI tool based on preference
- **Mise Configuration**: Basic API endpoint configuration

### Claude Code Specific Projects

When using Claude Code + CCR:
- Model selection handled via `ccr model` command
- Single CCR instance with default configuration
- Specialized routing for CC protocol requirements

## Development Notes

### Architecture Principles
- **Veloera** serves as the universal provider gateway for all tools
- **Mise** manages project-level environments and tool selection
- **Official CLI tools** use their native protocols with Veloera
- **CCR** is specialized and independent, only for Claude Code usage
- **API keys** are managed centrally in Veloera

### Tool Separation
- General tools (claude/qwen/codex) connect directly to Veloera
- Claude Code connects through CCR for CC-specific protocol handling
- Each project can use different tools via Mise environment configuration
- No interference between different tool workflows

### Configuration Management
- Project-level tool selection via Mise environments
- Environment variables provide automatic configuration switching
- No Mise tasks complexity - only environment variable management

## Project-Level Mise Configuration

Mise loads configuration from both `.mise.toml` and `.mise.local.toml` files, where `.mise.local.toml` overrides settings in `.mise.toml`. This allows for:

- **Shared configuration** in `.mise.toml` (committed to version control)
- **Personal tokens** in `.mise.local.toml` (private, not committed)

### Quick Start with .mise.local.toml.example

To get started quickly:

1. **Copy the example file**:
   ```bash
   cp .mise.local.toml.example .mise.local.toml
   ```

2. **Edit .mise.local.toml** with your actual API keys:
   ```toml
   [env]
   # Claude Code personal
   #ANTHROPIC_BASE_URL = "https://open.bigmodel.cn/api/anthropic"
   #ANTHROPIC_AUTH_TOKEN = "your-bigmodel-api-key"

   # Veloera API
   ANTHROPIC_BASE_URL = "http://10.1.1.11:3000/"
   ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
   ```

3. **Your .mise.local.toml should contain your real tokens** (not committed to git)

### General Multi-Tool Project Examples

#### Shared Project Configuration (.mise.toml)
```toml
[env]
# Claude CLI configuration (template - actual token in .mise.local.toml)
ANTHROPIC_BASE_URL = "http://localhost:3000/v1"

# Qwen CLI configuration (template - actual token in .mise.local.toml)
OPENAI_BASE_URL = "http://localhost:3000/v1"

# Codex CLI configuration (template - actual token in .mise.local.toml)
OPENAI_API_KEY = "sk-template-token"
OPENAI_BASE_URL = "http://localhost:3000/v1"
```

#### Personal Private Configuration (.mise.local.toml)
```toml
[env]
# Personal API tokens (overrides .mise.toml settings)
ANTHROPIC_AUTH_TOKEN = "sk-your-personal-token"
OPENAI_API_KEY = "sk-your-personal-token"

# Alternative direct API configuration (commented out)
# ANTHROPIC_BASE_URL = "https://open.bigmodel.cn/api/anthropic"
# ANTHROPIC_AUTH_TOKEN = "your-direct-api-token"
```

### Development Project Example

#### Project's .mise.toml (committed)
```toml
[env]
# Development project uses Claude CLI
ANTHROPIC_BASE_URL = "http://localhost:3000/v1"
```

#### Your .mise.local.toml (private, not committed)
```toml
[env]
# Personal token for Claude CLI
ANTHROPIC_AUTH_TOKEN = "sk-your-personal-token-here"

# Or use Veloera gateway
ANTHROPIC_BASE_URL = "http://192.168.8.100:3000/"
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key-here"
```

### Content Creation Project Example

#### Project's .mise.toml (committed)
```toml
[env]
# Content creation project uses Qwen CLI
OPENAI_BASE_URL = "http://localhost:3000/v1"
```

#### Your .mise.local.toml (private, not committed)
```toml
[env]
# Personal token for Qwen CLI
OPENAI_API_KEY = "sk-your-personal-openai-token-here"
```
