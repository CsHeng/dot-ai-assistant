# QWEN.md

This file provides context and guidance for Qwen Code (and other AI assistants) when working with this repository.

## Project Overview

This is an **AI Tools Management System** designed to solve the complexity of managing multiple AI CLI tools and their configurations through a unified architecture. The project implements a **multi-tool CLI + Mise + Veloera Gateway** pattern for streamlined AI development workflows.

### Core Architecture

```
Project Directory → Mise Environment Management → Official CLI Tools → Veloera API Gateway → AI Providers
```

**Alternative Architecture (Claude Code specific):**
```
Claude Code CLI → Claude Code Router (CCR) → Veloera → AI Providers
```

## Key Components

### 1. Veloera API Gateway
- **Purpose**: Unified API aggregation and routing service
- **Function**: Collects multiple AI providers (BigGLM, DeepSeek, Kimi, etc.) and provides standardized API endpoints
- **Deployment**: Docker-based deployment with web management interface
- **Key Features**: API format conversion, provider abstraction, cost management, special billing plans (e.g., BigGLM coding)

### 2. Mise Environment Management
- **Purpose**: Project-level environment switching
- **Function**: Automatically applies different AI configurations based on directory
- **Configuration**: `.mise.toml` files in project directories
- **Key Benefit**: Seamless tool and model switching between projects

### 3. AI CLI Tools
- **Claude Code CLI**: Primary development tool (Anthropic API format)
- **Qwen CLI**: Alternative tool (OpenAI-compatible API format)
- **OpenAI Codex CLI**: Official OpenAI tool (OpenAI API format)
- **Other Tools**: Gemini CLI, Factory AI CLI, etc.

## Project Structure

```
├── .mise.toml.sample              # Configuration template (committed)
├── .mise.toml                     # Personal configuration (git-ignored)
├── README.md                      # Project overview and quick start
├── CLAUDE.md                      # Claude Code specific guidance
├── QWEN.md                        # This file - Qwen Code guidance
├── docs/
│   ├── analysis/                  # Requirements and analysis documents
│   │   ├── 01-requirements.md
│   │   ├── 02-gateway-analysis.md
│   │   └── ...
│   ├── solution/                  # Solution specifications
│   │   ├── 00-final-claude-mise-veloera-solution.md
│   │   └── ...
│   ├── architecture/              # Architecture diagrams (PlantUML)
│   ├── guides/                    # Implementation and configuration guides
│   ├── comparison/                # Tool comparisons
│   └── references/                # Reference materials
└── .claude/                       # Claude Code configurations (git-ignored)
```

## Quick Start

### 1. Initial Setup

```bash
# Copy configuration template
cp .mise.toml.sample .mise.toml

# Edit configuration with your API keys
vim .mise.toml
```

### 2. Start Services

```bash
# Start Veloera gateway
docker-compose up -d

# Verify service status
docker-compose ps

# Apply environment configuration
mise env
```

### 3. Test Tools

```bash
# Test Claude Code CLI
claude "Hello, test connection"

# Test Qwen CLI
qwen "你好，测试连接"

# Test OpenAI Codex CLI
codex "Write a Python function"
```

## Configuration Management

### Primary Configuration File: `.mise.toml`

The template `.mise.toml.sample` contains all configuration options:

```toml
[env]

# Global AI settings
API_TIMEOUT_MS = "3000000"
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"

# Claude Code API Configuration (via Veloera)
ANTHROPIC_BASE_URL = "http://10.1.1.11:3000/"
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"

# Optional: Direct BigGLM API (alternative)
# ANTHROPIC_BASE_URL = "https://open.bigmodel.cn/api/anthropic"
# ANTHROPIC_AUTH_TOKEN = "your-bigmodel-api-key"

# Qwen CLI Configuration (OpenAI-compatible)
OPENAI_BASE_URL = "http://10.1.1.11:3000/v1"
OPENAI_API_KEY = "sk-your-veloera-api-key"

# Model preferences
ANTHROPIC_DEFAULT_SONNET_MODEL = "glm-4.6"
ANTHROPIC_DEFAULT_HAIKU_MODEL = "glm-4.5-air"
```

### Project-Level Configuration

Each project can have its own `.mise.toml` for specific AI configurations:

**Development Project Example:**
```toml
[env]
ANTHROPIC_BASE_URL = "http://10.1.1.11:3000/"
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_DEFAULT_SONNET_MODEL = "glm-4.6"
```

**Content Creation Project Example:**
```toml
[env]
ANTHROPIC_BASE_URL = "http://10.1.1.11:3000/"
ANTHROPIC_AUTH_TOKEN = "sk-your-veloera-api-key"
ANTHROPIC_DEFAULT_SONNET_MODEL = "glm-4.5"
```

## Supported AI Providers

### Via Veloera Gateway
- **BigGLM**: glm-4.6, glm-4.5-air (with special coding billing plans)
- **DeepSeek**: deepseek-chat
- **Kimi**: moonshot-v1-8k
- **Additional providers**: Configurable through Veloera web interface

### Direct API Support
- **Anthropic Claude**: Direct API access
- **OpenAI**: Direct API access
- **Any OpenAI-compatible providers**: Through standardized endpoints

## Common Development Commands

### Service Management
```bash
# Start all services
docker-compose up -d

# Check service status
docker-compose ps

# View Veloera logs
docker-compose logs veloera

# Restart services
docker-compose restart
```

### Configuration Testing
```bash
# Apply mise environment
mise env

# Check current AI configuration
mise env | grep -E "(ANTHROPIC_|OPENAI_)"

# Test Veloera connectivity
curl -s "http://localhost:3000/api/status"

# Test Claude Code
claude "Test connection and model availability"
```

### Project Switching
```bash
# Switch to different project (automatically applies project config)
cd ~/projects/my-web-app
claude "Write a React component"  # Uses glm-4.6

cd ~/projects/my-content
claude "Write a blog post"        # Uses glm-4.5
```

## Development Patterns

### Multi-Tool Support Pattern
The system supports multiple CLI tools simultaneously:

```bash
# All tools work through the same Veloera gateway
claude "Write Python code"    # Anthropic format
qwen "写Python代码"           # OpenAI format  
codex "Generate function"     # OpenAI format
```

### Project-Specific Configuration Pattern
Different projects automatically use different AI models and configurations:

```bash
# Development project uses high-performance model
cd ~/projects/web-app
# Claude Code automatically uses glm-4.6

# Content project uses cost-effective model  
cd ~/projects/blog
# Claude Code automatically uses glm-4.5
```

## Troubleshooting

### Common Issues

1. **Connection Problems**
```bash
# Check Veloera status
docker-compose ps
curl -s "http://localhost:3000/health"

# Verify environment variables
mise env | grep ANTHROPIC
```

2. **Configuration Not Applied**
```bash
# Reload mise environment
eval "$(mise activate bash)"

# Check project configuration
cat .mise.toml
```

3. **Tool-Specific Issues**
```bash
# Test each tool individually
claude "Hello"      # Test Claude Code
qwen "你好"         # Test Qwen CLI
codex "Write code" # Test Codex CLI
```

## Documentation Structure

### Recommended Reading Order

**For Quick Start:**
1. `README.md` - Project overview and setup
2. `docs/solution/00-final-claude-mise-veloera-solution.md` - Complete solution
3. Current file - Practical usage guidance

**For Deep Understanding:**
1. `docs/analysis/` - Requirements and technical analysis
2. `docs/solution/` - Solution design and alternatives
3. `docs/architecture/` - Architecture diagrams and design
4. `docs/guides/` - Detailed implementation guides

### Key Documents

- **Requirements**: `docs/analysis/01-requirements.md`
- **Final Solution**: `docs/solution/00-final-claude-mise-veloera-solution.md`
- **Architecture Diagram**: `docs/architecture/01-current-architecture.puml`
- **Implementation Guide**: `docs/guides/01-implementation-guide.md`
- **Tool Comparisons**: `docs/comparison/`

## Best Practices

### Configuration Management
- Keep `.mise.toml.sample` in version control
- Never commit `.mise.toml` (contains API keys)
- Use descriptive model names for different project types
- Test configuration changes in a safe project first

### Project Organization
- Create project-specific `.mise.toml` files
- Use consistent naming conventions for models
- Document project-specific AI requirements
- Regular review of model usage and costs

### Development Workflow
- Always verify Veloera is running before starting work
- Test tool connections after configuration changes
- Use project-specific configurations for optimal cost/performance
- Monitor usage through Veloera web interface

## Integration Notes

### Claude Code Router (CCR) Integration
For advanced Claude Code usage, CCR can be added as an intermediate layer:
```
Claude Code CLI → CCR → Veloera → AI Providers
```
This provides additional routing and model switching capabilities for Claude Code specifically.

### IDE Integration
The same configuration can be used with AI-powered IDEs:
- **Cursor**: Use same environment variables
- **VS Code with Cline/Copilot**: Compatible with OpenAI format
- **Other AI IDEs**: Generally support OpenAI/Anthropic formats

## Security Considerations

- API keys are stored in `.mise.toml` (git-ignored)
- Veloera provides centralized key management
- Use different API keys for different environments
- Regular rotation of API keys recommended
- Monitor usage through Veloera analytics

## Performance Optimization

- Use appropriate models for different task types
- Leverage project-specific configurations for cost management
- Monitor response times and adjust provider selection
- Use Veloera's load balancing and failover capabilities