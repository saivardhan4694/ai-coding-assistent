# AI Coding Assistant

> A powerful, extensible AI agent framework with advanced tool-calling capabilities, context management, and Model Context Protocol (MCP) integration.

[![Python Version](https://img.shields.io/badge/python-3.14%2B-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Architecture](#architecture)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Usage](#usage)
- [Built-in Tools](#built-in-tools)
- [Advanced Features](#advanced-features)
- [Contributing](#contributing)

## Overview

AI Coding Assistant is a sophisticated AI agent framework designed to interact with code, files, and external services through a powerful tool-calling system. Built with Python and leveraging state-of-the-art LLMs (Google Gemini, OpenAI, OpenRouter), it provides an intelligent CLI interface for developers to automate tasks, analyze codebases, and build AI-powered workflows.

### What Makes This Different?

- **MCP Integration**: Full support for Model Context Protocol servers with stdio and HTTP/SSE transports
- **Advanced Context Management**: Intelligent context compression and token management
- **Safety-First Design**: Multiple approval policies and dangerous command detection
- **Session Persistence**: Save, resume, and checkpoint conversations
- **Rich Terminal UI**: Beautiful, interactive terminal interface with real-time streaming
- **Extensible Tool System**: Easy-to-extend plugin architecture for custom tools
- **Subagent Support**: Specialized subagents for complex, multi-step tasks

## Key Features

### Core Functionality

| Feature | Description |
|---------|-------------|
| **Interactive & Single-Run Modes** | Use as an interactive REPL or for one-off command execution |
| **Streaming Responses** | Real-time text streaming with rich formatting |
| **Multi-Turn Conversations** | Maintains full conversation context across turns |
| **Configurable Models** | Support for multiple LLM providers (Gemini, OpenAI, OpenRouter) |
| **Dynamic Temperature Control** | Adjust model creativity on-the-fly |

### 🛠️ Built-in Tool Suite

The agent comes with 12+ production-ready tools:

- **File Operations**: `read_file`, `write_file`, `edit_file`
- **Directory Management**: `list_dir`, `glob` (pattern matching)
- **Search Capabilities**: `grep` (regex pattern search)
- **Shell Execution**: `shell` (execute system commands)
- **Web Access**: `web_search`, `web_fetch`
- **Memory System**: `memory` (persistent knowledge storage)
- **Task Management**: `todo` (task list management)

### Advanced Systems

#### Context Management
- **Automatic Context Compression**: Intelligently manages token limits
- **Tool Output Pruning**: Keeps conversation history relevant
- **Token Usage Tracking**: Monitor API consumption in real-time
- **Smart Message Prioritization**: Retains critical information

#### Safety & Approval System
Multiple approval policies for different use cases:

| Policy | Behavior |
|--------|----------|
| `on-request` | Agent requests approval for each mutating operation |
| `auto` | Auto-approves safe operations, requests for dangerous ones |
| `auto-edit` | Auto-approves file edits, requests for shell commands |
| `never` | Always requests approval |
| `yolo` | Never requests approval (use with caution) |

#### Session Management
- **Save Sessions**: Persist entire conversation state
- **Resume Sessions**: Continue from where you left off
- **Checkpoints**: Create restore points within conversations
- **Session History**: Browse and restore previous sessions

#### MCP (Model Context Protocol) Integration
- **Multi-Server Support**: Connect to multiple MCP servers simultaneously
- **Stdio Transport**: Launch local MCP servers as subprocesses
- **HTTP/SSE Transport**: Connect to remote MCP servers
- **Dynamic Tool Discovery**: Automatically discover and use MCP-provided tools
- **Health Monitoring**: Real-time server connection status

#### Loop Detection
- Automatically detects repetitive agent behavior
- Prevents infinite loops and wasted API calls
- Configurable detection thresholds

#### Hooks System
Execute custom scripts at key lifecycle points:

- `before_agent`: Run before agent processing
- `after_agent`: Run after agent completes
- `before_tool`: Run before tool execution
- `after_tool`: Run after tool execution
- `on_error`: Handle errors gracefully

#### Subagent Architecture
- **Specialized Agents**: Delegate complex tasks to focused subagents
- **Built-in Subagents**: Code reviewer, codebase investigator
- **Custom Subagents**: Define your own with custom prompts and tools
- **Resource Limits**: Control token usage and execution time per subagent

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                    CLI Interface                    │
│                   (main.py, ui/)                    │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│                  Agent Core                         │
│              (agent/agent.py)                       │
│  ┌──────────────┐  ┌──────────────┐                 │
│  │   Session    │  │Context Mgr   │                 │
│  │  Management  │  │& Compression │                 │
│  └──────────────┘  └──────────────┘                 │
│  ┌──────────────┐  ┌──────────────┐                 │
│  │Loop Detector │  │  Persistence │                 │
│  └──────────────┘  └──────────────┘                 │
└──────────────────────┬──────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
┌───────▼────────┐ ┌──▼────────┐ ┌──▼────────┐
│  Tool Registry │ │LLM Client │ │MCP Manager│
│   (tools/)     │ │(client/)  │ │ (tools/)  │
└───────┬────────┘ └───────────┘ └──┬────────┘
        │                            │
┌───────▼────────────────────────────▼────────┐
│            External Services                │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │File Ops  │  │  Shell   │  │MCP Server│   │
│  │          │  │  Executor│  │          │   │
│  └──────────┘  └──────────┘  └──────────┘   │
│  ┌──────────┐  ┌──────────┐                 │
│  │  Web     │  │  Memory  │                 │
│  │  Access  │  │  Storage │                 │
│  └──────────┘  └──────────┘                 │
└─────────────────────────────────────────────┘
```

### Component Overview

| Component | Purpose |
|-----------|---------|
| **CLI Interface** | User interaction, command processing, rich terminal output |
| **Agent Core** | Orchestrates agent execution, manages event loop |
| **Session Management** | Tracks conversation state, manages persistence |
| **Context Manager** | Handles message history, compression, token limits |
| **Loop Detector** | Identifies and prevents repetitive behavior |
| **Tool Registry** | Discovers, registers, and manages available tools |
| **LLM Client** | Abstracts API calls to various LLM providers |
| **MCP Manager** | Manages connections to MCP servers |
| **Hooks System** | Executes custom scripts at lifecycle events |

## Installation

### Prerequisites

- **Python 3.14+** (required)
- **uv** (recommended) or pip
- An API key from one of the supported providers:
  - Google AI (Gemini)
  - OpenAI
  - OpenRouter

### Step 1: Clone the Repository

```bash
git clone https://github.com/yourusername/ai-coding-assistent.git
cd ai-coding-assistent
```

### Step 2: Set Up Python Environment

#### Using `uv` (Recommended)

```bash
# Install uv if you haven't already
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install dependencies
uv sync
```

#### Using pip with venv

```bash
# Create virtual environment
python -m venv .venv

# Activate virtual environment
# On Windows:
.venv\Scripts\activate
# On macOS/Linux:
source .venv/bin/activate

# Install dependencies
pip install -e .
```

### Step 3: Configure Environment Variables

```bash
# Copy the example environment file
cp .env.example .env

# Edit .env and add your API key
# For Google Gemini:
API_KEY=your_gemini_api_key_here
BASE_URL=https://generativelanguage.googleapis.com/v1beta/openai/

# For OpenAI:
# API_KEY=your_openai_api_key_here
# BASE_URL=https://api.openai.com/v1

# For OpenRouter:
# API_KEY=your_openrouter_api_key_here
# BASE_URL=https://openrouter.ai/api/v1
```

### Step 4: Verify Installation

```bash
# Run in interactive mode
python main.py

# Or with a single prompt
python main.py "What files are in the current directory?"
```

## Quick Start

### Interactive Mode

Launch the agent in interactive mode for a conversational experience:

```bash
python main.py
```

**Example Session:**

```
AI Agent
────────────────────────────────────────
model: openrouter/pony-alpha
cwd: /your/working/directory
commands: /help /config /approval /model /exit

[user]> List all Python files in the current directory

[assistant]> I'll search for Python files in the current directory.

[tool: glob]
pattern: *.py
max_depth: 1

Found 5 Python files:
- main.py
- setup.py
- config.py
...

[user]> Read the main.py file and summarize its purpose

[assistant]> ...
```

### Single Command Mode

Execute a single task and exit:

```bash
python main.py "Create a README.md file with project documentation"
```

### Specifying Working Directory

```bash
python main.py --cwd /path/to/your/project "Analyze the codebase structure"
```

## Configuration

The agent can be configured via:

1. **`.agent/config.toml`** - Project-specific configuration
2. **Environment variables** - API keys and base URLs
3. **Runtime commands** - Dynamic configuration changes

### Configuration File Example

Create `.agent/config.toml` in your project:

```toml
# Model Configuration
[model]
name = "google/gemini-2.0-flash-exp"
temperature = 0.7
context_window = 256000

# Approval Policy
approval = "auto"  # Options: on-request, auto, auto-edit, never, yolo

# Maximum conversation turns
max_turns = 100

# Tool Allowlist (optional - if set, only these tools are available)
# allowed_tools = ["read_file", "write_file", "list_dir", "shell"]

# Shell Environment
[shell_environment]
ignore_default_excludes = false
exclude_patterns = ["*KEY*", "*TOKEN*", "*SECRET*", "*PASSWORD*"]

[shell_environment.set_vars]
PYTHONPATH = "/custom/path"

# Hooks Configuration
hooks_enabled = true

[[hooks]]
name = "run_tests"
trigger = "after_tool"
command = "pytest tests/"
timeout_sec = 60
enabled = true

# MCP Server Configuration
[mcp_servers.filesystem]
enabled = true
command = "uvx"
args = ["mcp-server-filesystem", "/allowed/path"]
startup_timeout_sec = 10

[mcp_servers.github]
enabled = true
command = "npx"
args = ["-y", "@modelcontextprotocol/server-github"]
env = { GITHUB_TOKEN = "your_token_here" }

# Instructions for the agent
[instructions]
developer = """
You are a senior software engineer specialized in Python.
Always follow PEP 8 guidelines and write comprehensive docstrings.
"""

user = """
Prefer functional programming patterns.
Write unit tests for all new functions.
"""
```

### Runtime Configuration Commands

| Command | Description | Example |
|---------|-------------|---------|
| `/config` | Show current configuration | `/config` |
| `/model <name>` | Change active model | `/model google/gemini-2.0-flash-exp` |
| `/approval <policy>` | Change approval policy | `/approval auto` |
| `/tools` | List available tools | `/tools` |
| `/mcp` | Show MCP server status | `/mcp` |
| `/stats` | Display session statistics | `/stats` |
| `/clear` | Clear conversation history | `/clear` |

## Usage

### Available Commands

| Command | Description |
|---------|-------------|
| `/help` | Show all available commands |
| `/config` | Display current configuration |
| `/model [name]` | View or change the active model |
| `/approval [policy]` | View or change approval policy |
| `/tools` | List all available tools |
| `/mcp` | Show MCP server connections and status |
| `/stats` | Display session statistics (tokens, turns, etc.) |
| `/save` | Save the current session |
| `/sessions` | List all saved sessions |
| `/resume <id>` | Resume a saved session |
| `/checkpoint` | Create a checkpoint in the current session |
| `/restore <id>` | Restore to a previous checkpoint |
| `/clear` | Clear conversation history |
| `/exit` or `/quit` | Exit the agent |

### Example Use Cases

#### 1. Codebase Analysis

```bash
[user]> Analyze the structure of this Python project and create a dependency graph
```

#### 2. Automated Refactoring

```bash
[user]> Refactor all functions in utils.py to use type hints and add docstrings
```

#### 3. Documentation Generation

```bash
[user]> Generate API documentation for all public functions in the src/ directory
```

#### 4. Testing Workflows

```bash
[user]> Create unit tests for the database.py module with 100% coverage
```

#### 5. Code Review

```bash
[user]> Review the latest changes in main.py and suggest improvements
```

## 🔧 Built-in Tools

### File Operations

| Tool | Description | Example |
|------|-------------|---------|
| `read_file` | Read file contents | Read `config.py` |
| `write_file` | Create or overwrite files | Create a new `README.md` |
| `edit_file` | Make targeted edits | Replace function in `utils.py` |

### Directory Operations

| Tool | Description | Example |
|------|-------------|---------|
| `list_dir` | List directory contents | List files in `src/` |
| `glob` | Pattern-based file search | Find all `*.py` files |

### Search & Analysis

| Tool | Description | Example |
|------|-------------|---------|
| `grep` | Search file contents | Find all TODO comments |

### Execution

| Tool | Description | Example |
|------|-------------|---------|
| `shell` | Execute system commands | Run `pytest tests/` |

### Web Access

| Tool | Description | Example |
|------|-------------|---------|
| `web_search` | Search the web (DuckDuckGo) | Search for "Python async best practices" |
| `web_fetch` | Fetch web page content | Fetch documentation from URL |

### Productivity

| Tool | Description | Example |
|------|-------------|---------|
| `memory` | Store/retrieve information | Remember project requirements |
| `todo` | Manage task lists | Add task to TODO list |

## 🎓 Advanced Features

### MCP Server Integration

Connect to external MCP servers to extend functionality:

```toml
[mcp_servers.database]
enabled = true
command = "npx"
args = ["-y", "mcp-server-postgres"]
env = { DATABASE_URL = "postgresql://..." }

[mcp_servers.remote_api]
enabled = true
url = "https://api.example.com/mcp"
startup_timeout_sec = 15
```

### Subagent System

Define specialized subagents for complex tasks:

```python
# Example: Custom code reviewer subagent
{
    "name": "security_reviewer",
    "description": "Reviews code for security vulnerabilities",
    "system_prompt": "You are a security expert...",
    "allowed_tools": ["read_file", "grep"],
    "max_turns": 10
}
```

### Hooks for CI/CD Integration

```toml
[[hooks]]
name = "lint_code"
trigger = "after_tool"
command = "ruff check ."
enabled = true

[[hooks]]
name = "format_code"
trigger = "before_agent"
script = ".agent/hooks/format.sh"
enabled = true
```

### Session Management Workflow

```bash
# Start working on a feature
[user]> Let's implement user authentication

# Create a checkpoint before making changes
[user]> /checkpoint

# Make changes...

# Save the session
[user]> /save

# Later, resume the session
$ python main.py
[user]> /sessions
[user]> /resume <session-id>

# If something went wrong, restore checkpoint
[user]> /restore <checkpoint-id>
```

## Contributing

Contributions are welcome! Here's how you can help:

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/amazing-feature`
3. **Commit your changes**: `git commit -m 'Add amazing feature'`
4. **Push to the branch**: `git push origin feature/amazing-feature`
5. **Open a Pull Request**

### Development Setup

```bash
# Clone your fork
git clone https://github.com/yourusername/ai-coding-assistent.git
cd ai-coding-assistent

# Install development dependencies
uv sync --all-extras

# Run tests
pytest tests/

# Format code
ruff format .

# Lint
ruff check .
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Built with [OpenAI API](https://openai.com/api/) and [Google Gemini](https://ai.google.dev/)
- MCP integration powered by [Model Context Protocol](https://modelcontextprotocol.io/)
- Terminal UI with [Rich](https://rich.readthedocs.io/)
- CLI framework using [Click](https://click.palletsprojects.com/)

## Support

- **Issues**: [GitHub Issues](https://github.com/yourusername/ai-coding-assistent/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/ai-coding-assistent/discussions)

---

<div align="center">
Made by developers, for developers
</div>
