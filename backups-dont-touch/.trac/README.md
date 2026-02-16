# TRAC SOFTWARE'S GITHUB COPILOT

This document provides comprehensive information about implementing and using GitHub Copilot across all environments.

## FILE LOCATIONS & DEFAULT CONFIGURATIONS

### UNDERSTANDING PATH TYPES

**Paths with `~` (Tilde) - Personal/User-Level**
- **What it means**: `~` is Unix/Linux shorthand for the current user's home directory
- **Actual location**: `~/.copilot/` = `/home/username/.copilot/` on Linux/Mac, `C:\Users\username\.copilot\` on Windows
- **Scope**: **Personal** - applies to ALL projects for that user across the entire system
- **Storage**: On your local machine, NOT in any repository
- **Git**: Should NEVER be committed to version control
- **When to use**: Personal preferences, coding style, personal agents, cross-project configurations

**Repository Paths (`.github/`, `.trac/`, etc.) - Project-Level**
- **Location**: Inside your project/repository directory (e.g., `/workspaces/trac.labs.CascadeTRAC/.github/`)
- **Scope**: **Repository** - only applies to this specific project
- **Storage**: In the repository, shared with all team members via git
- **Git**: SHOULD be committed and pushed to share with team
- **When to use**: Project-specific settings, team standards, project agents, shared configurations

**Quick Decision Guide**

| Use `~/.copilot/` (Personal) | Use `.github/` (Repository) |
|------------------------------|-----------------------------|
| My coding style preferences | Project build commands |
| My personal shortcuts | Project structure documentation |
| My custom agents (all projects) | Team coding standards |
| My MCP servers | Project-specific agents |
| My preferred settings | Team MCP configuration |
| Cross-project skills | Project-specific skills |

### CONFIGURATION FILE HIERARCHY

GitHub Copilot uses a hierarchical configuration system with the following priority (highest to lowest):

1. **Personal/User-level** (`~/.copilot/` or `~/.claude/`) - Your home directory, all projects
2. **Custom directories** (via `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`) - Additional personal/team locations
3. **Repository-level** (`.github/`, `.trac/`, or project root) - This repository only
4. **Organization/Enterprise-level** (`.github-private` repository) - All org repositories
5. **System-level** (GitHub defaults) - Built-in fallbacks

### COMPLETE DIRECTORY STRUCTURE

**User-Level Configuration** (`~/.copilot/` or `$XDG_CONFIG_HOME/copilot/`)
```
~/.copilot/
├── config.json                    # Main CLI configuration
├── copilot-instructions.md        # Personal instructions (highest priority)
├── mcp-config.json               # MCP server configuration
├── agents/                        # Personal custom agents (CLI only)
│   ├── my-agent.agent.md
│   └── another-agent.agent.md
├── skills/                        # Personal skills
│   ├── skill-name/
│   │   ├── SKILL.md
│   │   └── resources/
│   └── another-skill/
├── session-state/                 # CLI session persistence
│   ├── [session-id]/
│   │   ├── plan.md
│   │   ├── files/
│   │   └── state.json
└── logs/                          # CLI logs
```

**User-Level Configuration** (`~/.claude/` - Claude-specific)
```
~/.claude/
├── copilot-instructions.md        # Claude-specific personal instructions
└── skills/                        # Claude-specific personal skills
    └── skill-name/
        └── SKILL.md
```

**Repository-Level Configuration** (Standard - use `.github/`)
```
.github/
├── copilot-instructions.md        # Repository-wide instructions (COMMIT THIS)
├── agents/                        # Repository custom agents (COMMIT THIS)
│   ├── reviewer.agent.md
│   ├── tester.agent.md
│   └── deployer.agent.md
├── skills/                        # Repository skills (COMMIT THIS)
│   ├── debugging/
│   │   ├── SKILL.md
│   │   └── scripts/
│   └── deployment/
├── instructions/                  # Path-specific instructions (COMMIT THIS)
│   ├── api.instructions.md
│   ├── frontend.instructions.md
│   └── backend.instructions.md
└── workflows/
    └── copilot-setup-steps.yml   # Agent environment setup (COMMIT THIS)

[Project Root]/
├── AGENTS.md                      # General agent instructions (any directory)
├── CLAUDE.md                      # Claude-specific instructions (root only)
├── GEMINI.md                      # Gemini-specific instructions (root only)
├── .agent.md                      # Agent instructions (searched up tree)
└── .claude/                       # Alternative to .github for Claude
    └── skills/
        └── skill-name/
            └── SKILL.md
```

**Repository-Level Configuration** (Custom - alternative to `.github/`)
```
.trac/                             # Custom configuration directory
├── copilot-instructions.md        # Alternative location (requires env var)
├── agents/                        # Custom agents location
│   └── custom-agent.agent.md
└── skills/                        # Custom skills location
    └── skill-name/
        └── SKILL.md

# To use .trac/ instead of .github/, set environment variable:
# export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac"
# OR for user-level: export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac"
```

**Organization/Enterprise Configuration** (`.github-private` repository)
```
.github-private/
└── agents/
    ├── org-standard.agent.md
    ├── security-review.agent.md
    └── compliance.agent.md
```

**Custom Directory Configuration** (User or Repository)
```
# User-level custom directory
~/.trac/                           # Personal TRAC-specific config
├── copilot-instructions.md        # Personal TRAC instructions
├── agents/
│   └── trac-agent.agent.md
└── skills/
    └── trac-skill/
        └── SKILL.md

# Repository-level custom directory
/path/to/project/.trac/            # Project TRAC-specific config
├── copilot-instructions.md        # Project TRAC instructions
├── agents/
│   └── project-agent.agent.md
└── skills/
    └── project-skill/
        └── SKILL.md

# Enable via environment variable (add to ~/.bashrc or ~/.zshrc):
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$PWD/.trac"
# Multiple directories separated by colon (:)
# Priority: left to right (leftmost = highest priority)
```

**Repository Settings** (GitHub.com Settings)
```
Settings > Copilot
├── Code review settings
├── Coding agent settings
│   ├── MCP configuration (JSON editor)
│   └── Environment secrets
└── Environments > copilot
    └── Environment secrets (COPILOT_MCP_*)
```

### DEFAULT CONFIGURATION FILES

**`.github/copilot-instructions.md`** (Repository Instructions)
```markdown
# Copilot Instructions for [Project Name]

## Build & Test Commands
- Build: `npm run build`
- Test: `npm test`
- Lint: `npm run lint`

## Project Structure
- `/src` - Source code
- `/tests` - Test files
- `/docs` - Documentation

## Coding Standards
- Use TypeScript strict mode
- Follow ESLint configuration
- Write tests for all new features

## CI/CD
- Tests run on all PRs
- Deploy to staging on merge to develop
- Deploy to production on merge to main

## Known Issues
- [Issue #123] Database migration requires manual step
- [Issue #456] API rate limiting on external service
```

**`~/.copilot/copilot-instructions.md`** (Personal Instructions)
```markdown
# Personal Copilot Instructions

## Preferences
- Always add detailed comments
- Prefer functional programming patterns
- Use async/await over promises
- Write comprehensive error handling

## Code Style
- 2-space indentation
- Single quotes for strings
- Semicolons always
- Max line length: 100 characters

## Testing
- Write unit tests first (TDD)
- Use Jest for JavaScript/TypeScript
- Aim for 80%+ code coverage
```

**`.github/agents/custom-agent.agent.md`** (Custom Agent)
```markdown
---
name: custom-agent
description: Specialized agent for specific task domain
tools:
  - view
  - edit
  - create
  - bash
  - grep
model: claude-sonnet-4
target: vscode
mcp-servers:
  server-name:
    type: stdio
    command: node
    args: ["/path/to/server.js"]
    tools: ["*"]
---

# Custom Agent Instructions

You are a specialized agent focused on [specific domain].

## Your Responsibilities
- [Responsibility 1]
- [Responsibility 2]

## Guidelines
- [Guideline 1]
- [Guideline 2]

## Examples
[Provide examples of expected behavior]
```

**`.github/instructions/api.instructions.md`** (Path-Specific)
```markdown
---
applyTo: "src/api/**/*.ts"
excludeAgent: ["code-review"]
---

# API Code Instructions

## API Standards
- All endpoints require authentication
- Use Zod for request validation
- Return consistent error formats
- Log all requests with correlation IDs

## Error Handling
```typescript
{
  error: {
    code: "ERROR_CODE",
    message: "Human readable message",
    details: {}
  }
}
```
```

**`.github/skills/debugging/SKILL.md`** (Skill Definition)
```markdown
---
name: debugging
description: Advanced debugging techniques for Node.js applications including memory leaks, performance profiling, and async issues
license: MIT
---

# Debugging Skill

## When to Use
- Investigating memory leaks
- Profiling performance bottlenecks
- Debugging async/await issues
- Analyzing heap dumps

## Tools
- Node.js built-in inspector
- Chrome DevTools
- clinic.js for performance
- heapdump for memory analysis

## Procedures

### Memory Leak Investigation
1. Enable heap snapshot: `node --inspect app.js`
2. Take baseline snapshot
3. Trigger suspected leak
4. Take comparison snapshot
5. Analyze retained objects

### Performance Profiling
```bash
clinic doctor -- node app.js
# Run load test
# Ctrl+C to stop
# Opens HTML report
```

## Common Patterns
[Include debugging patterns and solutions]
```

**`~/.copilot/mcp-config.json`** (MCP Configuration)
```json
{
  "mcpServers": {
    "github": {
      "type": "stdio",
      "command": "node",
      "args": ["/path/to/github-mcp-server"],
      "env": {
        "GITHUB_TOKEN": "COPILOT_MCP_GITHUB_TOKEN"
      },
      "tools": ["*"]
    },
    "sentry": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sentry"],
      "env": {
        "SENTRY_TOKEN": "COPILOT_MCP_SENTRY_TOKEN",
        "SENTRY_ORG": "my-org"
      },
      "tools": ["list-issues", "get-issue", "list-projects"]
    },
    "notion": {
      "type": "http",
      "url": "https://api.notion-mcp.example.com",
      "headers": {
        "Authorization": "Bearer ${COPILOT_MCP_NOTION_TOKEN}"
      },
      "tools": ["*"]
    }
  }
}
```

**`~/.copilot/config.json`** (CLI Configuration)
```json
{
  "model": "claude-sonnet-4",
  "theme": "dark",
  "experimental": false,
  "autoCompact": true,
  "allowedDirectories": [
    "/home/user/projects",
    "/workspace"
  ],
  "defaultAgent": "custom-agent"
}
```

**`.github/workflows/copilot-setup-steps.yml`** (Agent Setup)
```yaml
name: Copilot Setup Steps

on:
  workflow_dispatch:

permissions:
  id-token: write
  contents: read

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    environment: copilot
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install MCP dependencies
        run: |
          npm install -g uv pipx
          pipx install mcp-server-tools
      
      - name: Configure Azure Auth
        uses: azure/login@v1
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      
      - name: Setup database
        run: |
          npm run db:migrate
          npm run db:seed
```

### ENVIRONMENT VARIABLES

**System Environment Variables**
```bash
# Custom instruction directories (CRITICAL for using non-standard paths)
# Use this to enable .trac/ or any custom directory structure
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="/path/to/custom:/another/path"

# Examples:
# Single custom directory (user-level)
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac"

# Multiple directories (colon-separated, highest priority first)
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$HOME/.copilot:/workspace/.trac"

# Project-specific (use in project .envrc or run before copilot command)
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac"

# Combined personal + project
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$PWD/.trac:$HOME/.copilot"

# XDG Base Directory support (Linux standard)
export XDG_CONFIG_HOME="$HOME/.config"
export XDG_DATA_HOME="$HOME/.local/share"
export XDG_CACHE_HOME="$HOME/.cache"
# When set, Copilot uses $XDG_CONFIG_HOME/copilot/ instead of ~/.copilot/

# Copilot CLI settings
export COPILOT_MODEL="claude-sonnet-4"
export COPILOT_THEME="dark"

# MCP secret prefix (repository secrets - set in GitHub Settings)
COPILOT_MCP_GITHUB_TOKEN="ghp_..."
COPILOT_MCP_SENTRY_TOKEN="sntrys_..."
COPILOT_MCP_NOTION_TOKEN="secret_..."
```

**Setting Up Custom Directories Permanently**

```bash
# For Bash (add to ~/.bashrc)
echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac"' >> ~/.bashrc
source ~/.bashrc

# For Zsh (add to ~/.zshrc)
echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac"' >> ~/.zshrc
source ~/.zshrc

# For Fish (add to ~/.config/fish/config.fish)
echo 'set -x COPILOT_CUSTOM_INSTRUCTIONS_DIRS "$HOME/.trac"' >> ~/.config/fish/config.fish
source ~/.config/fish/config.fish

# Per-project with direnv (create .envrc in project root)
cat > .envrc << 'EOF'
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac:$HOME/.trac"
EOF
direnv allow
```

**GitHub Environment Secrets** (Settings > Environments > copilot)
- `COPILOT_MCP_GITHUB_TOKEN` - GitHub API token
- `COPILOT_MCP_SENTRY_TOKEN` - Sentry API token
- `COPILOT_MCP_AZURE_CREDENTIALS` - Azure credentials
- `COPILOT_MCP_NOTION_TOKEN` - Notion integration token
- `COPILOT_MCP_SLACK_TOKEN` - Slack bot token
- `COPILOT_MCP_*` - Any custom MCP server secrets

### FILE NAMING CONVENTIONS

**Agent Files**
- Format: `[name].agent.md`
- Location: `.github/agents/` or `~/.copilot/agents/`
- Example: `code-reviewer.agent.md`, `test-generator.agent.md`

**Instruction Files**
- Repository-wide: `copilot-instructions.md`
- Path-specific: `*.instructions.md` (with frontmatter)
- Agent-specific: `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`
- Hidden: `.agent.md` (searched up directory tree)

**Skill Files**
- Format: Directory with `SKILL.md` file
- Location: `.github/skills/[skill-name]/` or `~/.copilot/skills/[skill-name]/`
- Main file: Always named `SKILL.md` (uppercase)

**Configuration Files**
- CLI config: `~/.copilot/config.json`
- MCP config: `~/.copilot/mcp-config.json`
- Session state: `~/.copilot/session-state/[session-id]/`

### SEARCH ORDER & PRECEDENCE

**Instruction File Priority** (with custom directories)
1. `~/.copilot/copilot-instructions.md` (personal user config, **highest priority**)
2. **Custom directories** from `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` (left to right)
   - Example: If `COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$PWD/.trac"`
   - First: `$HOME/.trac/copilot-instructions.md`
   - Then: `$PWD/.trac/copilot-instructions.md`
3. `.github/copilot-instructions.md` (standard repository location)
4. `.github/instructions/*.instructions.md` (path-specific instructions)
5. Organization instructions (configured by admin in `.github-private`)
6. `AGENTS.md`, `CLAUDE.md`, `GEMINI.md` (searched up directory tree)
7. System defaults (GitHub built-in behavior)

**Important**: Files with higher priority override conflicting instructions from lower priority files

**Agent Definition Priority** (with custom directories)
1. System built-in agents (explore, task, plan, code-review) - **cannot be overridden**
2. Repository agents (`.github/agents/*.agent.md`)
3. Custom directory agents (from `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`)
   - `.trac/agents/*.agent.md` (if configured)
4. Organization agents (`.github-private/agents/*.agent.md`)
5. Personal agents (`~/.copilot/agents/*.agent.md`, CLI only)

**Note**: Agent names must be unique. First match wins. Built-in names (explore, task, plan, code-review) cannot be overridden by custom agents.

**Skill Discovery** (with custom directories)
1. Repository skills (`.github/skills/` or `.claude/skills/`)
2. Custom directory skills (from `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`)
   - `.trac/skills/` (if configured)
3. Personal skills (`~/.copilot/skills/` or `~/.claude/skills/`)
4. Organization skills (coming soon)

**Note**: Skills are matched by name in description. Multiple skills can be loaded simultaneously if relevant to the task.

**Configuration Resolution** (with custom directories)
1. Command-line flags (`--model`, `--agent`, etc.) - **highest priority**
2. Session-specific settings (`/model`, `/theme`, etc.)
3. User config (`~/.copilot/config.json`)
4. Custom directory configs (from `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`)
   - `.trac/config.json` (if exists and configured)
5. Repository settings (GitHub.com Settings > Copilot)
6. Organization defaults
7. System defaults - **lowest priority**

**Priority Rule**: More specific always wins. Command-line > Session > User > Custom > Repository > Organization > System

### INITIALIZATION & AUTOMATIC FILE CREATION

**What Gets Created Automatically**

**CLI First Run** (`copilot` command)
- Creates `~/.copilot/` directory
- Creates `~/.copilot/config.json` with defaults
- Prompts to trust current directory
- Creates session state directory on first chat
- No files created in repository

**Coding Agent First Assignment**
- No automatic file creation in repository
- Agent can create `.github/copilot-instructions.md` if prompted with `/init`
- Creates branch and draft PR for its work
- Session logs stored on GitHub servers

**IDE Extension First Use**
- No automatic file creation
- Uses existing repository structure
- Stores settings in IDE configuration
- Local chat history in IDE workspace storage

**What You Must Create Manually**

**Repository Setup** (Recommended for all projects)
```bash
# Create GitHub directory structure
mkdir -p .github/agents
mkdir -p .github/skills
mkdir -p .github/instructions
mkdir -p .github/workflows

# Create base instruction file
touch .github/copilot-instructions.md

# Create setup workflow (if using MCP or special setup)
touch .github/workflows/copilot-setup-steps.yml
```

**Personal Setup** (One-time per user)
```bash
# Create personal config directory
mkdir -p ~/.copilot/agents
mkdir -p ~/.copilot/skills

# Create personal instructions
touch ~/.copilot/copilot-instructions.md

# Create MCP config (if using MCP servers)
touch ~/.copilot/mcp-config.json

# OR for XDG compliance
mkdir -p $XDG_CONFIG_HOME/copilot
```

**Agent Setup** (When creating custom agents)
```bash
# Create agent file
touch .github/agents/my-agent.agent.md

# OR personal agent (CLI only)
touch ~/.copilot/agents/my-agent.agent.md
```

**Skill Setup** (When adding new skills)
```bash
# Create skill directory and file
mkdir -p .github/skills/my-skill
touch .github/skills/my-skill/SKILL.md

# Add any resources
mkdir -p .github/skills/my-skill/scripts
```

**Quick Start Commands**

**Initialize Repository for Copilot (Standard `.github/` approach)**
```bash
# Comprehensive setup with standard .github/ directory
mkdir -p .github/{agents,skills,instructions,workflows}
cat > .github/copilot-instructions.md << 'EOF'
# Copilot Instructions

## Build Commands
- Build: [your build command]
- Test: [your test command]
- Lint: [your lint command]

## Project Structure
Describe your project structure here.

## Coding Standards
List your coding standards and conventions.
EOF

# Create basic setup workflow
cat > .github/workflows/copilot-setup-steps.yml << 'EOF'
name: Copilot Setup Steps
on: workflow_dispatch
permissions:
  id-token: write
  contents: read
jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    environment: copilot
    steps:
      - uses: actions/checkout@v4
      - name: Setup
        run: echo "Add your setup commands here"
EOF

git add .github
git commit -m "Add Copilot configuration"
```

**Initialize Repository with Custom `.trac/` Directory**
```bash
# Setup with custom .trac/ directory (requires env var)
mkdir -p .trac/{agents,skills,instructions}
cat > .trac/copilot-instructions.md << 'EOF'
# TRAC Copilot Instructions

## Build Commands
- Build: [your build command]
- Test: [your test command]

## TRAC-specific Configuration
Add TRAC-specific instructions here.
EOF

# Create environment setup for this project
cat > .envrc << 'EOF'
# Enable .trac directory for Copilot
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac:$HOME/.trac"
EOF

# If using direnv
direnv allow

# OR add to your shell startup file
echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac:$HOME/.trac"' >> ~/.bashrc

git add .trac .envrc
git commit -m "Add TRAC Copilot configuration"
```

**Initialize Both Standard and Custom (Belt and Suspenders)**
```bash
# Use both .github/ (standard, always works) and .trac/ (custom, requires env var)
mkdir -p .github/{agents,skills,instructions,workflows}
mkdir -p .trac/{agents,skills}

# Standard location (works without configuration)
cat > .github/copilot-instructions.md << 'EOF'
# Standard Copilot Instructions

Basic project instructions here.
EOF

# TRAC-specific location (higher priority when env var set)
cat > .trac/copilot-instructions.md << 'EOF'
# TRAC-Enhanced Copilot Instructions

TRAC-specific enhanced instructions here.
These take priority when COPILOT_CUSTOM_INSTRUCTIONS_DIRS is set.
EOF

cat > .envrc << 'EOF'
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac:$HOME/.trac"
EOF

git add .github .trac .envrc
git commit -m "Add Copilot configuration (standard + TRAC)"
```

**Initialize Personal Copilot Config (Standard)**
```bash
# Setup standard personal config
mkdir -p ~/.copilot/{agents,skills}
cat > ~/.copilot/copilot-instructions.md << 'EOF'
# Personal Copilot Instructions

## My Preferences
- [Your preferences]

## Code Style
- [Your style preferences]
EOF

# Setup basic MCP configuration
cat > ~/.copilot/mcp-config.json << 'EOF'
{
  "mcpServers": {}
}
EOF
```

**Initialize Personal TRAC Config (Custom)**
```bash
# Setup custom TRAC personal config
mkdir -p ~/.trac/{agents,skills}
cat > ~/.trac/copilot-instructions.md << 'EOF'
# Personal TRAC Copilot Instructions

## My TRAC Preferences
- TRAC-specific personal preferences
- Custom TRAC workflows

## TRAC Code Style
- TRAC-specific style preferences
EOF

# Enable TRAC directory permanently
echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$HOME/.copilot"' >> ~/.bashrc
source ~/.bashrc

# Verify it's set
echo $COPILOT_CUSTOM_INSTRUCTIONS_DIRS
```

**Using Copilot to Initialize** (Easiest method)
```bash
# Let Copilot create instructions via CLI
copilot

# In chat, prompt:
# "Create a comprehensive .github/copilot-instructions.md file 
#  for this project based on the codebase structure"

# OR via coding agent with /init command
# Assign agent to issue with description:
# "/init - Initialize Copilot configuration for this repository"
```

**Validation Commands**

**Check Current Configuration (Standard + Custom)**
```bash
# Check environment variable
echo $COPILOT_CUSTOM_INSTRUCTIONS_DIRS

# View active instructions (all locations)
ls -la .github/copilot-instructions.md    # Standard repo
ls -la .trac/copilot-instructions.md      # Custom repo (if using)
ls -la ~/.copilot/copilot-instructions.md # Standard personal
ls -la ~/.trac/copilot-instructions.md    # Custom personal (if using)

# View agents (all locations)
ls -la .github/agents/       # Standard repo
ls -la .trac/agents/         # Custom repo (if using)
ls -la ~/.copilot/agents/    # Standard personal
ls -la ~/.trac/agents/       # Custom personal (if using)

# View skills (all locations)
ls -la .github/skills/       # Standard repo
ls -la .trac/skills/         # Custom repo (if using)
ls -la ~/.copilot/skills/    # Standard personal
ls -la ~/.trac/skills/       # Custom personal (if using)

# View MCP config
cat ~/.copilot/mcp-config.json
cat ~/.trac/mcp-config.json  # If using custom location

# Check CLI config
cat ~/.copilot/config.json
cat ~/.trac/config.json      # If using custom location

# Find all copilot instruction files
find . -name "copilot-instructions.md" 2>/dev/null
find ~ -name "copilot-instructions.md" 2>/dev/null
```

**Test Configuration** (CLI)
```bash
# Start Copilot and check what's loaded
copilot

# In chat, use commands:
# /session              # View session info and loaded config
# /context              # View token usage and context
# /mcp show             # View MCP servers (if configured)
```

**Repository Configuration Checklist (Standard `.github/`)**
- [ ] `.github/copilot-instructions.md` exists with build/test commands
- [ ] `.github/agents/` directory exists (if using custom agents)
- [ ] `.github/skills/` directory exists (if using skills)
- [ ] `.github/workflows/copilot-setup-steps.yml` exists (if using MCP)
- [ ] GitHub environment `copilot` exists with secrets (if using MCP)
- [ ] MCP secrets prefixed with `COPILOT_MCP_`
- [ ] Instructions include project structure and conventions
- [ ] Instructions include known issues and workarounds
- [ ] Files committed to git and pushed to remote

**Repository Configuration Checklist (Custom `.trac/`)**
- [ ] `.trac/copilot-instructions.md` exists with TRAC-specific instructions
- [ ] `.trac/agents/` directory exists (if using TRAC agents)
- [ ] `.trac/skills/` directory exists (if using TRAC skills)
- [ ] `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` environment variable set
- [ ] `.envrc` file created with correct export statement (if using direnv)
- [ ] Environment variable added to shell startup file (~/.bashrc, ~/.zshrc)
- [ ] Files committed to git and pushed to remote
- [ ] Team members informed about required environment variable

**Personal Configuration Checklist (Standard `~/.copilot/`)**
- [ ] `~/.copilot/` directory exists
- [ ] `~/.copilot/config.json` exists (created automatically by CLI)
- [ ] `~/.copilot/copilot-instructions.md` exists with personal preferences
- [ ] `~/.copilot/mcp-config.json` exists (if using MCP locally)
- [ ] `~/.copilot/agents/` directory exists (if using personal agents)
- [ ] `~/.copilot/skills/` directory exists (if using personal skills)

**Personal Configuration Checklist (Custom `~/.trac/`)**
- [ ] `~/.trac/` directory exists
- [ ] `~/.trac/copilot-instructions.md` exists with TRAC personal preferences
- [ ] `~/.trac/agents/` directory exists (if using personal TRAC agents)
- [ ] `~/.trac/skills/` directory exists (if using personal TRAC skills)
- [ ] `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` includes `$HOME/.trac`
- [ ] Environment variable persists across shell sessions
- [ ] Tested with `echo $COPILOT_CUSTOM_INSTRUCTIONS_DIRS`

### HIDDEN FILES & SYSTEM FILES

**Copilot Working Files** (Do not modify manually)
```
~/.copilot/
├── .copilot.lock              # Lock file for concurrent sessions
├── session-state/             # Active and past session states
│   ├── [session-id]/
│   │   ├── state.json         # Session state data
│   │   ├── plan.md            # Current plan (if in plan mode)
│   │   ├── files/             # Session artifacts
│   │   └── .checkpoints/      # Session checkpoints
│   └── .active                # Currently active session marker
├── logs/                      # CLI operation logs
│   ├── copilot.log
│   ├── mcp-[server].log
│   └── [date].log
├── cache/                     # Cached data
│   ├── models.json            # Available models cache
│   ├── completions/           # Completion cache
│   └── embeddings/            # Embedding cache
└── .trusted-dirs              # List of trusted directories
```

**IDE-Specific Files**
```
# VS Code
.vscode/
├── settings.json              # May contain Copilot settings
└── extensions/                # Extension storage
    └── github.copilot/
        ├── cache/
        └── state/

# JetBrains
.idea/
├── copilot/                   # Copilot plugin data
└── workspace.xml              # May contain Copilot settings

# Xcode
*.xcodeproj/
└── xcuserdata/
    └── [user].xcuserdatad/
        └── copilot/           # Copilot data
```

**Git Integration Files**
```
.git/
├── hooks/                     # Can contain Copilot-generated hooks
│   ├── pre-commit            # Copilot can help create these
│   ├── pre-push
│   └── commit-msg
└── copilot/                   # Copilot git metadata (if used)
```

**Temporary & Cache Files** (Safe to delete)
- `~/.copilot/cache/*` - Cached model responses and embeddings
- `~/.copilot/logs/*` - Historical log files (keep recent for debugging)
- `~/.copilot/session-state/[old-sessions]/` - Completed session states
- IDE temporary files in workspace `.vscode/` or `.idea/`

**Files to Include in `.gitignore`**
```gitignore
# Personal Copilot state (NEVER commit these)
.copilot-local/
.claude-local/

# IDE-specific Copilot data (NEVER commit)
.vscode/extensions/github.copilot/
.idea/copilot/

# Session artifacts generated in repo (NEVER commit)
copilot-session-*/

# Personal config if accidentally placed in repo (NEVER commit)
~/.copilot/
~/.claude/
~/.trac/

# Note: DO commit these repo-level configs:
# .github/copilot-instructions.md - YES, commit
# .github/agents/ - YES, commit
# .github/skills/ - YES, commit
# .trac/copilot-instructions.md - YES, commit if using custom dirs
# .trac/agents/ - YES, commit if using custom dirs
# .envrc - YES, commit if using direnv with COPILOT_CUSTOM_INSTRUCTIONS_DIRS
```

**Hidden Agent Instruction Files** (Searched up directory tree)
```
./AGENTS.md                    # Current directory
./CLAUDE.md                    # Current directory  
./GEMINI.md                    # Current directory
./.agent.md                    # Current directory
../.agent.md                   # Parent directory (searched up)
../../AGENTS.md                # Parent's parent (search continues)
```

**Environment-Specific Files**
```
# Docker/Devcontainer
.devcontainer/
├── devcontainer.json          # May specify Copilot extensions
└── copilot-setup.sh           # Custom setup script

# GitHub Codespaces
.devcontainer/
└── devcontainer.json          # Copilot pre-installed in Codespaces

# CI/CD
.github/workflows/
└── copilot-*.yml              # Copilot-related workflows
```

**Backup Files** (Created by Copilot during edits)
```
# Automatic backups (some IDEs)
file.ts.copilot-backup
file.ts~

# Rollback points
.copilot/rollback/
└── [timestamp]/
```

**Permission & Trust Files**
```
~/.copilot/
├── .trusted-dirs              # Directories approved for file access
├── .allowed-tools             # Tools approved for session
└── .allowed-urls              # URLs approved for fetching
```

**MCP Server State Files**
```
~/.copilot/mcp-state/
├── [server-name]/
│   ├── state.json             # Server-specific state
│   └── cache.json             # Server response cache
└── .mcp.lock                  # MCP server lock file
```

**Cleaning Up Copilot Files**

**Safe Cleanup** (Won't affect configuration)
```bash
# Remove old session states (keep last 10)
cd ~/.copilot/session-state
ls -t | tail -n +11 | xargs rm -rf

# Clear cache
rm -rf ~/.copilot/cache/*

# Clear old logs (keep last 30 days)
find ~/.copilot/logs -type f -mtime +30 -delete

# Remove temporary files
find ~/.copilot -name "*.tmp" -delete
find ~/.copilot -name "*.lock" -delete
```

**Complete Reset** (Removes all configuration)
```bash
# WARNING: This removes ALL personal configuration!
# Backup first if you have custom agents/instructions
mv ~/.copilot ~/.copilot.backup
mv ~/.claude ~/.claude.backup

# Copilot will recreate defaults on next run
copilot
```

**File Size Management**
```bash
# Check Copilot directory size
du -sh ~/.copilot

# Find large files
find ~/.copilot -type f -size +10M -exec ls -lh {} \;

# Check session state size
du -sh ~/.copilot/session-state/*

# Check log size
du -sh ~/.copilot/logs
```

## AGENTS

### GITHUB COPILOT AGENTS

GitHub Copilot provides specialized built-in agents for different tasks:

**Copilot Coding Agent**
- Autonomously works on GitHub issues and creates pull requests
- Can be assigned directly to issues for automatic implementation
- Executes tasks with access to repository context
- Supports MCP (Model Context Protocol) integration for extended capabilities

**Agent Management**
- View agent progress in real-time through the session viewer
- Monitor agent activities via timeline events in pull requests
- Use `/tasks` command (CLI) to view and manage background agents
- Review agent work before merging via draft pull requests

**Default Built-in Agents (CLI)**
- **Explore**: Quick codebase analysis and question answering
- **Task**: Executes commands (tests, builds, lints) with summarized output
- **Plan**: Creates implementation plans analyzing dependencies and structure
- **Code-review**: Reviews changes focusing on genuine issues only

### CUSTOM AGENTS

Custom agents are specialized versions of Copilot tailored to specific workflows and use cases.

**Agent Profile Structure**
- Defined in `.agent.md` files with YAML frontmatter + Markdown content
- Located in `.github/agents` directory (repository-level)
- Can also be stored in `~/.copilot/agents` (user-level, CLI only)
- Organization/enterprise-level: `/agents` directory in `.github-private` repository

**Creating Custom Agents**
1. Navigate to github.com/copilot/agents or use IDE agent configuration
2. Click "Create an agent" and select target repository
3. Configure agent profile with required properties:
   - `name`: Unique identifier for the agent
   - `description`: What the agent does (required)
   - `tools`: List of available tools (optional, defaults to all)
   - `mcp-servers`: MCP server configurations (org/enterprise only)
   - `model`: AI model to use (IDE only)
   - `target`: Environment restriction (`vscode` or `github-copilot`)
4. Write agent prompt in Markdown below frontmatter (max 30,000 chars)
5. Commit to repository default branch

**Using Custom Agents**
- **CLI**: Use `/agent` command or reference in prompts
- **IDE**: Select from agent dropdown in Chat window
- **GitHub.com**: Choose from dropdown when assigning agent to issues
- **Command-line**: `copilot --agent=agent-name --prompt "task"`

**Priority Order** (naming conflicts): System > Repository > Organization

### BACKGROUND AGENTS

Background agents execute tasks asynchronously in separate context windows.

**Task Agent Types** (via `/agent` or task tool):
- Runs in subprocess with separate context
- Executes sequentially (one at a time)
- Each agent is stateless - provide complete context
- Returns results in single message

**Managing Background Agents**
- Use `/tasks` command to view active background agents
- Monitor shell sessions and subagent progress
- Stop tasks as needed through task management interface
- Background processes in CLI use `detach: true` for persistence

## PROMPT FILES

GitHub Copilot supports multiple types of instruction files for context provision.

**Agent Instructions Files**
- `AGENTS.md`: General agent instructions (any directory)
- `CLAUDE.md`: Claude-specific instructions (root only)
- `GEMINI.md`: Gemini-specific instructions (root only)
- Nearest file in directory tree takes precedence

**Repository-Wide Instructions**
- File: `.github/copilot-instructions.md`
- Applies to all Copilot requests in repository
- Contains natural language guidance in Markdown format
- Used by Copilot Chat, coding agent, and code review
- Can be auto-generated by coding agent via `/init` or custom prompt

**Path-Specific Instructions**
- Files: `.github/instructions/**/*.instructions.md`
- Apply to files matching specified glob patterns
- Frontmatter specifies `applyTo` with glob syntax
- Can exclude specific agents via `excludeAgent` property
- Example: `applyTo: "app/models/**/*.rb"`

**Personal Instructions**
- File: `$HOME/.copilot/copilot-instructions.md`
- Applies across all projects for the user
- Takes highest priority when conflicts occur

**Environment Variable**
- `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`: Additional instruction directories

**Best Practices**
- Include build/test commands and their proper sequence
- Document framework versions and runtime requirements
- Describe project architecture and key file locations
- List validation steps and CI/CD pipeline details
- Note workarounds for known issues
- Keep instructions under 2 pages for optimal performance

## SKILLS

Agent Skills enhance specialized task performance through reusable instruction sets.

**What are Skills?**
- Folders containing instructions, scripts, and resources
- Open standard used by multiple AI agents (agentskills.org)
- Loaded automatically when relevant to current task
- Supported by: Coding agent, CLI, VS Code Insiders

**Skill Locations**
- **Project skills**: `.github/skills/` or `.claude/skills/` in repository
- **Personal skills**: `~/.copilot/skills/` or `~/.claude/skills/` (coding agent & CLI only)
- Organization/enterprise skills: Coming soon

**Creating a Skill**
1. Create skill directory: `.github/skills/skill-name/`
2. Create `SKILL.md` file with YAML frontmatter:
   ```markdown
   ---
   name: skill-name
   description: When and how to use this skill
   license: Optional license info
   ---
   
   Detailed instructions, examples, and guidelines...
   ```
3. Optionally add scripts, examples, or other resources to directory
4. Skill is automatically available when relevant

**How Copilot Uses Skills**
- Automatically selected based on prompt and skill description
- SKILL.md injected into agent context when triggered
- Agent follows instructions and can access included scripts/resources

**Skills vs Custom Instructions**
- **Custom instructions**: Simple, always-relevant guidance (coding standards)
- **Skills**: Detailed, task-specific instructions loaded when needed

**Example Use Cases**
- Debugging GitHub Actions workflows
- Converting image formats
- Database migration patterns
- Testing strategies for specific frameworks

## INSTRUCTIONS AND RULES

Instructions customize Copilot behavior across different scopes and priorities.

**Instruction Priority** (highest to lowest):
1. Personal instructions (`~/.copilot/copilot-instructions.md`) - **User home directory**
2. Custom directory instructions (via `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`) - **e.g., `~/.trac/` or `$PWD/.trac/`**
3. Repository instructions (`.github/copilot-instructions.md`) - **In repository, committed to git**
4. Organization instructions (configured by org admins) - **Org-wide**

**Supported Locations**
- `.github/copilot-instructions.md` (repository-wide, **COMMIT THIS**)
- `.github/instructions/**/*.instructions.md` (path-specific, **COMMIT THIS**)
- `AGENTS.md`, `CLAUDE.md`, `GEMINI.md` (agent-specific, searched up tree, **CAN COMMIT**)
- `~/.copilot/copilot-instructions.md` (personal, **NEVER COMMIT**)
- Custom directories via `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` (flexible, **depends on location**)
  - If in `~/.trac/` → **NEVER COMMIT**
  - If in `.trac/` (repo root) → **COMMIT THIS** + set env var for team

**Setting Up Custom Instruction Directories**

```bash
# Example 1: Personal TRAC instructions (not in any repository)
mkdir -p ~/.trac
cat > ~/.trac/copilot-instructions.md << 'EOF'
# My Personal TRAC Instructions
EOF
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac"

# Example 2: Repository TRAC instructions (in this project)
mkdir -p /workspaces/trac.labs.CascadeTRAC/.trac
cat > /workspaces/trac.labs.CascadeTRAC/.trac/copilot-instructions.md << 'EOF'
# Project TRAC Instructions
EOF
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac"

# Example 3: Both personal and project (colon-separated)
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$PWD/.trac:$HOME/.copilot"

# Make permanent (add to ~/.bashrc or ~/.zshrc)
echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$PWD/.trac"' >> ~/.bashrc
```

**Enabling/Disabling Instructions**
- Code review: Toggle in repository Settings > Copilot > Code review
- Personal choice affects whether repo instructions are used
- Enabled by default for all features

**Viewing Active Instructions**
- Check "References" in Copilot Chat responses
- Instruction files listed when used in response generation
- Click reference to view instruction file content

**Writing Effective Instructions**
- Use natural language in Markdown format
- Whitespace between instructions is ignored
- Be specific about commands, versions, and sequences
- Document errors and workarounds
- Include architectural context and file structure
- Specify validation steps and testing procedures

## HOOKS

Hooks in the context of GitHub Copilot refer to Git hooks and workflow customizations.

**Git Hooks with Copilot**
- Copilot can help create and modify Git hooks
- Standard Git hook locations apply: `.git/hooks/`
- Common hooks: pre-commit, pre-push, post-checkout, etc.

**Copilot Setup Steps Workflow**
- File: `.github/workflows/copilot-setup-steps.yml`
- Runs automatically when Copilot coding agent starts work
- Used for environment setup before agent execution
- Supports authentication (Azure, cloud providers, etc.)
- Required for MCP servers needing special dependencies
- Format: Standard GitHub Actions workflow

**Example Setup Steps Workflow**
```yaml
on:
  workflow_dispatch:
permissions:
  id-token: write
  contents: read
jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    environment: copilot
    steps:
      - name: Setup step
        run: echo "Setup commands here"
```

**Use Cases**
- Install runtime dependencies not in default runner
- Authenticate with cloud services (Azure, AWS, etc.)
- Configure MCP server prerequisites (uv, pipx, etc.)
- Set up database connections or test environments

## MCP SERVERS

Model Context Protocol (MCP) extends Copilot capabilities by integrating external systems.

**What is MCP?**
- Protocol for connecting Copilot to external tools and services
- Provides additional capabilities beyond built-in tools
- Supports local (stdio) and remote (HTTP/SSE) servers
- Tools are used autonomously by Copilot without approval prompts

**Built-in MCP Server**
- GitHub MCP server pre-configured in CLI
- Enables interaction with GitHub.com resources
- Supports PR merging, issue management, etc.

**Configuration Locations**
- **Repository**: Settings > Copilot > Coding agent > MCP configuration (JSON)
- **CLI**: `~/.copilot/mcp-config.json` or `$XDG_CONFIG_HOME/copilot/mcp-config.json`
- **Custom agents**: YAML frontmatter `mcp-servers` property (org/enterprise)

**MCP Server Configuration Format**
```json
{
  "mcpServers": {
    "server-name": {
      "type": "local|stdio|http|sse",
      "command": "command-to-run",
      "args": ["arg1", "arg2"],
      "tools": ["tool1", "tool2", "*"],
      "env": {
        "ENV_VAR": "COPILOT_MCP_SECRET_NAME"
      }
    }
  }
}
```

**Local MCP Configuration**
- `type`: `"local"` or `"stdio"`
- `command`: Command to start server (required)
- `args`: Arguments array (required)
- `env`: Environment variables mapping to Copilot secrets
- `tools`: Allowlist of tools to enable

**Remote MCP Configuration**
- `type`: `"http"` or `"sse"`
- `url`: Server endpoint URL (required)
- `headers`: HTTP headers (optional)
- `tools`: Allowlist of tools to enable

**Managing MCP Servers (CLI)**
- `/mcp show`: List configured servers
- `/mcp add`: Add new MCP server interactively
- `/mcp edit`: Modify server configuration
- `/mcp delete`: Remove server
- `/mcp disable/enable`: Toggle server availability

**Secrets for MCP**
- Must be prefixed with `COPILOT_MCP_`
- Configured in repository's "copilot" environment
- Settings > Environments > copilot > Add environment secret
- Referenced in configuration without prefix

**Popular MCP Servers**
- **Sentry**: Access error tracking and exception details
- **Notion**: Interact with notes and documentation
- **Azure**: Understand Azure resources and subscriptions
- **Cloudflare**: Process documentation and data analysis
- **Azure DevOps**: Work items, pipelines, documentation

**Validating MCP Setup**
1. Assign coding agent to an issue
2. Open agent session logs when work starts
3. Click ellipsis > Copilot in sidebar
4. Expand "Start MCP Servers" step
5. Verify tools are listed at bottom of logs

## TOOL SETS

GitHub Copilot has access to various tools depending on the environment.

**Core Tools (All Environments)**
- `view`: Read files and directories
- `edit`: Modify existing files
- `create`: Create new files
- `bash`: Execute shell commands
- `grep`: Search file contents (ripgrep-based)
- `glob`: Find files by pattern matching

**CLI-Specific Tools**
- `task`: Launch specialized sub-agents
- `fetch_copilot_cli_documentation`: Access CLI help
- `report_intent`: Update session intent display
- `update_todo`: Manage task checklists
- `read_bash`: Read output from async bash sessions
- `write_bash`: Send input to async bash sessions
- `stop_bash`: Terminate bash sessions
- `list_bash`: List active bash sessions
- `web_fetch`: Fetch web content (with permission)

**Bash Tool Modes**
- **Sync mode**: Waits for command completion, use for long-running tasks
- **Async mode**: Background execution, use for interactive tools
- **Detached mode**: Persistent processes (servers, daemons) with `detach: true`

**MCP Tools**
- Dynamically added via MCP server configuration
- Tool naming: `server-name/tool-name`
- Specified in agent profiles or global configuration
- Can be allowlisted per agent for security

**Tool Permissions (CLI)**
- Path permissions: Control directory/file access
- URL permissions: Control external web access
- Tool permissions: Require approval for potentially dangerous tools
- Use `--allow-all` or `/yolo` to bypass all permission checks
- Use `/reset-allowed-tools` to clear tool approvals

**Tool Approval Options (CLI)**
1. **Yes**: Allow this one time
2. **Yes, for session**: Auto-approve tool for current session
3. **No (Esc)**: Reject and provide alternative instructions

**Custom Agent Tool Configuration**
- Specify in agent profile frontmatter: `tools: ["read", "edit", "bash"]`
- Use `*` to enable all available tools
- Omitting `tools` property enables all tools by default
- Include MCP tools: `["read", "server-name/tool"]`

## DIAGNOSTICS

Tools and commands for monitoring and troubleshooting Copilot.

**CLI Diagnostics Commands**
- `/usage`: Session statistics (requests, duration, lines edited, tokens)
- `/context`: Visual token usage overview
- `/compact`: Manually compress conversation history
- `/tasks`: View background tasks and shell sessions
- `/help`: Display all available commands and shortcuts
- `copilot help`: Command-line help documentation

**Context Management**
- Automatic compression at 95% token limit
- Manual compression via `/compact` command
- Token visualization via `/context` command
- View per-model token breakdowns

**Session Information**
- `/session`: Show session info and workspace summary
- `/session checkpoints [n]`: View session checkpoints
- `/session files`: List session files
- `/session plan`: View current plan
- `/session rename <name>`: Rename current session

**Logging (CLI)**
- `copilot help logging`: Available logging levels
- Logs help debug tool usage and permissions
- Session logs stored in `~/.copilot/` directory

**Environment Information (CLI)**
- `copilot help environment`: Environment variables affecting CLI
- `copilot help config`: Configuration settings documentation
- `copilot help permissions`: Permission system details

**Code Review Diagnostics**
- Review agent focuses only on genuine issues
- Ignores style and formatting (unless configured otherwise)
- Use `/review` command for quick feedback before committing

**Validating Changes**
- Use `/diff` command to review changes in current directory
- Check git status before agent commits
- Review draft PRs before merging

**MCP Diagnostics**
- Check MCP server startup in coding agent session logs
- Verify tools are listed after server initialization
- Ensure secrets are properly configured with `COPILOT_MCP_` prefix
- Test MCP configuration by assigning agent to test issue

## CHAT SETTINGS

Configuration options for GitHub Copilot Chat across environments.

**IDE Chat Settings (VS Code, JetBrains, etc.)**
- Agent selection dropdown in Chat window
- Model selection (when supported by environment)
- Custom agent configuration via settings
- File attachment using `@` mention syntax

**GitHub.com Chat Settings**
- Access via github.com/copilot
- Repository attachment for context
- View references to see used instructions/files
- Personal vs organization settings inheritance

**CLI Chat Settings**
- Model selection: `/model` or `/models [model]` command
- Theme configuration: `/theme [show|set|list] [theme]`
- Terminal setup: `/terminal-setup` for multiline input
- Current directory: `/cwd` or `/cd` to change workspace

**Available Models** (varies by plan and environment)
- Claude Sonnet, Haiku, Opus variants
- GPT-5, GPT-5 mini, GPT-5 Codex variants
- Gemini Pro variants
- Model availability depends on Copilot plan tier

**Context Window Management**
- Automatic history compression enabled
- Manual compression via `/compact`
- Token usage monitoring via `/context`
- File inclusion via `@` followed by relative path

**Plan Mode (CLI)**
- Toggle with `Shift+Tab`
- Creates implementation plan before coding
- Use `/plan [prompt]` command
- Saves plan to session folder

**Conversation Management**
- `/clear` or `/new`: Clear conversation history
- `/resume [sessionId]`: Switch between sessions
- `/rename <name>`: Rename current session
- `--resume` flag: Resume previous sessions
- `--continue` flag: Resume most recent local session

**Experimental Features (CLI)**
- `/experimental [on|off|show]`: Manage experimental mode
- Access to preview features and capabilities
- Subject to change without notice

## SESSIONS

GitHub Copilot maintains conversation state across different session types.

### CLOUD

Cloud sessions are managed on GitHub.com servers.

**Copilot Chat (github.com/copilot)**
- Persistent across browser sessions
- Accessible from any device after login
- Repository context maintained via attachments
- Conversation history saved automatically
- Can reference previous conversations

**Copilot Coding Agent Sessions**
- Created when agent assigned to issue
- Visible via "View session" link in PR timeline
- Contains full agent activity log
- Shows tool usage and decisions
- Persists after PR completion

**Session Sharing**
- Share via `/share [file|gist] [path]` (CLI)
- Create GitHub gist for collaboration
- Export to markdown file
- Useful for documenting solutions

**Cloud Session Features**
- Automatic context retention
- Cross-device accessibility
- Organization-wide visibility (if configured)
- Integration with GitHub resources

### IDE (LOCAL)

Local IDE sessions run on developer machine.

**Visual Studio Code**
- Separate chat session per workspace
- History maintained in workspace storage
- Custom agents from workspace or user profile
- Model selection via settings

**JetBrains IDEs**
- Chat window with agent dropdown
- Workspace-level custom agents
- Tool configuration dialog
- Session persists per project

**Eclipse**
- Chat window with agent configuration
- Custom agent management
- Tool selection interface
- Project-specific context

**Xcode**
- Chat window with agent creation
- Customize agent dialog
- Model and tool selection
- Project context awareness

**Local Session Features**
- Faster response (no network latency for local processing)
- Workspace-specific context
- Local file system access
- IDE-integrated experience

### IDE (REMOTE)

Remote IDE sessions connect to server-side development environments.

**GitHub Codespaces**
- Copilot integrated automatically
- Cloud compute with local IDE experience
- Repository context pre-loaded
- Environment-specific settings

**Remote Development (VS Code)**
- SSH, WSL, Container connections
- Copilot runs in remote environment
- Access to remote file system
- Shared settings with local

**Remote Session Features**
- Consistent environment across team
- Access from any location
- Shared configuration possible
- Cloud-backed persistence

### CLI

Command-line interface sessions with interactive conversation.

**Starting a Session**
```bash
copilot                    # Start in current directory
copilot --resume           # Resume previous session
copilot --continue         # Resume most recent local session
copilot --agent=name       # Start with specific custom agent
```

**Session Directories**
- Session state: `~/.copilot/session-state/[session-id]/`
- Plan file: `[session-state]/plan.md`
- Artifacts: `[session-state]/files/`
- MCP config: `~/.copilot/mcp-config.json`
- Main config: `~/.copilot/config.json`

**Session Management**
- `/resume [sessionId]`: Switch sessions
- `/rename <name>`: Rename current session
- `/session`: View session information
- Sessions stored locally and remotely (if delegated)

**Trusted Directories**
- Asked to trust folder on first use
- Remember trust for future sessions
- `/add-dir <directory>`: Manually add trusted directory
- `/list-dirs`: Display all allowed directories
- Required for file access permissions

**CLI Session Features**
- Interactive prompt with shortcuts
- Background task management
- Shell command execution
- Plan mode collaboration
- Agent delegation to GitHub
- Local and remote session resumption

**Keyboard Shortcuts**
- `Ctrl+X → /`: Run command
- `Esc`: Cancel operation
- `Ctrl+C`: Cancel/clear/exit
- `Ctrl+D`: Shutdown
- `Ctrl+L`: Clear screen
- `Ctrl+O`: Expand recent timeline
- `Ctrl+E`: Expand all timeline
- `Ctrl+T`: Toggle reasoning display
- `Shift+Tab`: Toggle plan mode

**Delegation to Coding Agent**
```bash
/delegate [task description]
& [task description]
```
- Pushes session to GitHub coding agent
- Creates new branch and draft PR
- Preserves all context
- Agent works in background
- Can resume locally with `/resume`

## CROSS-PLATFORM & MULTI-ENVIRONMENT WORKFLOWS

Working seamlessly across different environments (local Windows, GitHub Codespaces, remote Linux, etc.) with consistent Copilot configuration.

### 📖 SECTION NAVIGATION

**If you are...** → **Go to...**

| Your Situation | Recommended Section |
|----------------|---------------------|
| **Brand new to Codespaces** | [COMPLETE BEGINNER'S QUICK START](#complete-beginners-quick-start-5-minutes) (5 min setup) |
| **Want to understand the concept** | [UNDERSTANDING: WHAT'S ACTUALLY HAPPENING?](#understanding-whats-actually-happening) |
| **Need to install Codespaces extension** | [CONNECTING LOCAL VSCODE TO CODESPACES - Step 1](#step-1-install-required-extension-one-time-setup) |
| **Want detailed step-by-step connection guide** | [CONNECTING LOCAL VSCODE TO CODESPACES](#connecting-local-vscode-to-codespaces) |
| **Need to sync Copilot config across environments** | [SETUP APPROACH 1: REPOSITORY CONFIG ONLY](#setup-approach-1-repository-config-only-easiest) |
| **Want to sync personal preferences too** | [SETUP APPROACH 2: PERSONAL CONFIG SYNC](#setup-approach-2-personal-config-sync-advanced) |
| **Looking for daily workflow examples** | [COMMON WORKFLOWS: LOCAL VSCODE + CODESPACES](#common-workflows-local-vscode--codespaces) |
| **Something not working** | [TROUBLESHOOTING](#troubleshooting-cross-platform-issues) OR [FAQ](#frequently-asked-questions-faq) |
| **Want best practices** | [BEST PRACTICES FOR HYBRID WORKFLOW](#best-practices-for-hybrid-workflow) |
| **Need quick reference** | [TIPS FOR EFFICIENT CODESPACES USE](#tips-for-efficient-codespaces-use) |

### OVERVIEW: YOUR DEVELOPMENT ENVIRONMENT OPTIONS

**You Have THREE Main Ways to Work on Your Code:**

```
┌─────────────────────────────────────────────────────────────────────┐
│                     YOUR DEVELOPMENT OPTIONS                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1️⃣ LOCAL WINDOWS                                                  │
│     VSCode installed on your Windows machine                       │
│     Files stored: C:\Users\username\repos\                         │
│     Copilot config: C:\Users\username\.copilot\                    │
│     ✅ Fast, offline access                                         │
│     ❌ Need to install Node, npm, dependencies                      │
│                                                                     │
│  2️⃣ CODESPACE IN BROWSER                                           │
│     VSCode running in your web browser                             │
│     Files stored: /workspaces/project/ (in cloud)                  │
│     Copilot config: /home/codespace/.copilot/                      │
│     ✅ No installation needed, works anywhere                       │
│     ❌ Requires internet, some browser lag                          │
│                                                                     │
│  3️⃣ LOCAL VSCODE → CODESPACE (BEST OF BOTH! ⭐)                    │
│     VSCode on your Windows machine, connected to cloud Linux       │
│     Files stored: /workspaces/project/ (in cloud)                  │
│     Copilot config: /home/codespace/.copilot/                      │
│     ✅ Local VSCode performance + cloud Linux environment           │
│     ✅ No dependency installation on Windows                        │
│     ✅ Best keyboard shortcuts, clipboard, themes                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Recommended Workflow for Beginners:**
1. Use **Option 3** (Local VSCode → Codespace) as your primary environment
2. Use `.github/copilot-instructions.md` for team configuration (auto-loads everywhere)
3. Optionally set up personal config if you want custom preferences

**This Section Covers:**
- ✅ How to connect local VSCode to Codespaces (detailed walkthrough)
- ✅ How to switch between environments seamlessly
- ✅ How to keep Copilot config synchronized
- ✅ How to troubleshoot common issues
- ✅ Best practices for cross-platform development

### COMPLETE BEGINNER'S QUICK START (5 MINUTES)

**Never Used Codespaces or Copilot Before? Start Here!**

**🎯 Goal**: Get up and running with the best development setup in 5 minutes.

**What You'll Do:**
1. Install GitHub Codespaces extension in your local VSCode
2. Connect to a codespace from your Windows machine
3. Set up Copilot configuration that works everywhere
4. Start coding!

**📋 Prerequisites:**
- [ ] Windows computer with VSCode installed
- [ ] GitHub account (free is fine)
- [ ] This repository: `hannahbellesheart/trac.labs.CascadeTRAC`
- [ ] Internet connection

**🚀 Let's Go!**

**MINUTE 1-2: Install Extension**

1. Open VSCode on your Windows machine
2. Press `Ctrl+Shift+X` (opens Extensions)
3. Search: `GitHub Codespaces`
4. Click "Install" on the official GitHub extension
5. Click the Account icon (bottom-left) → "Sign in with GitHub"
6. Authorize in browser when prompted

✅ **Check**: See your GitHub username in bottom-left corner of VSCode

**MINUTE 3-4: Create & Connect to Codespace**

Method A - From VSCode:
1. Press `Ctrl+Shift+P` (opens command palette)
2. Type: `Codespaces: Create New Codespace`
3. Select repository: `hannahbellesheart/trac.labs.CascadeTRAC`
4. Select branch: `main`
5. Wait 30-60 seconds... VSCode automatically connects!

Method B - From GitHub Website (easier):
1. Go to github.com/hannahbellesheart/trac.labs.CascadeTRAC
2. Click green "Code" button
3. Click "Codespaces" tab
4. Click "Create codespace on main"
5. When it opens in browser, click ☰ menu → "Open in VS Code Desktop"
6. Allow the prompt, and local VSCode launches connected!

✅ **Check**: Bottom-left corner shows "Codespaces: [name]"

**MINUTE 5: Set Up Copilot Config**

In your connected VSCode terminal (press `Ctrl+``):

```bash
# Verify you're in the codespace
pwd
# Should show: /workspaces/trac.labs.CascadeTRAC

# Create Copilot configuration
mkdir -p .github
cat > .github/copilot-instructions.md << 'EOF'
# Copilot Instructions for This Project

## Build & Test
- Build: npm run build
- Test: npm test
- Dev server: npm run dev

## Project Info
This is the trac.labs.CascadeTRAC project.
EOF

# Commit so it's available everywhere
git add .github/
git commit -m "Add Copilot configuration"
git push
```

✅ **Check**: File `.github/copilot-instructions.md` appears in Explorer

**🎉 YOU'RE DONE!** 

Now you can:
- Edit files (they're in the Linux codespace)
- Run commands in terminal (Linux)
- Use Copilot (loads your `.github/` config automatically)
- Close VSCode and reconnect anytime
- Work from any computer by connecting to the same codespace

**Next Steps:**
- Read "CONNECTING LOCAL VSCODE TO CODESPACES" below for detailed info
- Explore "COMMON WORKFLOWS" for day-to-day usage patterns
- Check "TROUBLESHOOTING" if anything doesn't work

### UNDERSTANDING: WHAT'S ACTUALLY HAPPENING?

**The Concept (Simple Explanation)**

Think of a codespace like this:

```
Your Windows Machine                    GitHub's Cloud
┌──────────────────┐                   ┌──────────────────┐
│                  │                   │                  │
│  VSCode Window   │ ←── Internet ───→ │  Linux Container │
│  (Just the UI)   │                   │  (Your code runs │
│                  │                   │   here!)         │
│  - Keyboard      │                   │                  │
│  - Mouse         │                   │  - Files         │
│  - Display       │                   │  - Terminal      │
│  - Clipboard     │                   │  - Processes     │
│                  │                   │  - Git           │
└──────────────────┘                   └──────────────────┘
```

**What This Means:**

1. **Your VSCode is just a "window"** - it displays the UI but doesn't run your code
2. **Linux container in the cloud** - your files and code actually live here
3. **Connection is "transparent"** - feels like working locally, but isn't
4. **Everything Linux** - when you type commands, they run in Linux, not Windows

**Why This Is Powerful:**

✅ **No Windows/Linux conflicts** - Everything runs in Linux (same as production)
✅ **Same environment as team** - Everyone gets identical setup
✅ **No "works on my machine"** - If it works in codespace, it works everywhere
✅ **Pre-configured environment** - Node, npm, tools already installed
✅ **Accessible anywhere** - Sign in from any computer, your environment is there

**The Connection:**

```
What's Local (Windows)          What's Remote (Linux Codespace)
─────────────────────          ──────────────────────────────
VSCode interface               All your project files
Your keyboard/mouse            Terminal commands
Your screen                    npm run dev
Your clipboard                 Git operations
VSCode themes                  Running processes
                              File saves
                              Copilot reads files from here
```

### WINDOWS LOCAL ↔️ LINUX CODESPACES

**The Challenge**
- **Local Windows machine**: Uses VSCode on Windows with paths like `C:\Users\username\.copilot\`
- **GitHub Codespaces**: Linux container in browser with paths like `/home/codespace/.copilot/`
- **Goal**: Same Copilot configuration and experience in both environments

**Understanding Path Differences**

| Component | Windows Local | Linux Codespace | Notes |
|-----------|---------------|-----------------|-------|
| Home directory | `C:\Users\username\` | `/home/codespace/` | `~` expands differently |
| Personal config | `C:\Users\username\.copilot\` | `/home/codespace/.copilot/` | Separate locations |
| Repository | `C:\Users\username\repos\project\` | `/workspaces/project\` | Different mount points |
| Separators | `\` (backslash) | `/` (forward slash) | Git handles automatically |
| Line endings | CRLF (`\r\n`) | LF (`\n`) | Git handles automatically |
| Env vars | `%USERPROFILE%` | `$HOME` | Shell differences |

**Strategy: Repository Config + Personal Sync**

The best approach uses **repository-level config** (committed to git) for team settings and optional **personal config sync** for your preferences.

### CONNECTING LOCAL VSCODE TO CODESPACES

**What You Need to Know**

GitHub Codespaces can be accessed in THREE ways:
1. **Browser-based VSCode** - Opens in your web browser (simplest, no installation)
2. **Local VSCode Desktop** - Connect from VSCode on your Windows machine (best performance)
3. **VSCode Insiders** - Same as desktop but with preview features

**Why Use Local VSCode?**
✅ Better performance (faster typing, no browser lag)
✅ Local keyboard shortcuts work perfectly
✅ Can use any VSCode theme/settings
✅ Access local clipboard seamlessly
✅ Works offline after initial connection
✅ Same familiar VSCode experience

**Step-by-Step: Connect Local VSCode to Codespaces (Beginner Guide)**

#### STEP 1: Install Required Extension (One-Time Setup)

1. **Open VSCode on your Windows machine**
   - Launch Visual Studio Code

2. **Open Extensions Panel**
   - Click the Extensions icon in the left sidebar (looks like 4 squares)
   - OR press `Ctrl+Shift+X`

3. **Search for Extension**
   - Type: `GitHub Codespaces`
   - Look for the official extension by GitHub (verified checkmark ✓)

4. **Install the Extension**
   - Click the blue "Install" button
   - Wait for installation to complete (usually 10-30 seconds)
   - You'll see "Reload Required" or it auto-activates

5. **Sign in to GitHub**
   - Click the Account icon (person silhouette) in the bottom-left corner
   - Choose "Sign in with GitHub"
   - Your browser will open asking you to authorize VSCode
   - Click "Authorize GitHub" or "Continue"
   - Return to VSCode - you should see your GitHub username in the bottom-left

**Verification**: You should now see a new "Remote Explorer" icon (computer with arrows) in the left sidebar.

#### STEP 2: Create or Connect to a Codespace

**Option A: Create New Codespace from VSCode**

1. **Open Command Palette**
   - Press `Ctrl+Shift+P` (or `F1`)
   - This opens a search box at the top

2. **Create Codespace**
   - Type: `Codespaces: Create New Codespace`
   - Press Enter
   
3. **Select Repository**
   - Choose your repository: `hannahbellesheart/trac.labs.CascadeTRAC`
   - If you don't see it, click "Select a repository from GitHub" and search

4. **Select Branch**
   - Choose: `main` (or your working branch)

5. **Select Machine Type** (if asked)
   - For most work: "2-core" is fine
   - For heavy builds: "4-core" or "8-core"
   - Click to select

6. **Wait for Creation**
   - VSCode will show "Creating codespace..."
   - This takes 30 seconds to 2 minutes
   - When ready, VSCode will automatically connect

**You're now connected!** Your local VSCode is controlling a Linux container in the cloud.

**Option B: Connect to Existing Codespace**

1. **Open Remote Explorer**
   - Click the "Remote Explorer" icon in left sidebar (computer with arrows)
   - OR press `Ctrl+Shift+P` and type "Remote Explorer: Focus on GitHub Codespaces View"

2. **View Your Codespaces**
   - You'll see a list under "GitHub Codespaces"
   - Shows all your active codespaces

3. **Connect to Codespace**
   - Find your codespace (e.g., "trac-labs-CascadeTRAC-main-abc123")
   - Right-click on it
   - Choose "Connect to Codespace"
   - OR hover and click the "→" (Connect) icon

4. **Wait for Connection**
   - VSCode connects to the codespace (5-15 seconds)
   - When connected, you'll see "Codespaces: [name]" in the bottom-left corner

**You're now connected!** Any files you open/edit are in the codespace (Linux), not your Windows machine.

**Option C: Quick Connect from Browser Codespace**

1. **Open Codespace in Browser First**
   - Go to github.com/hannahbellesheart/trac.labs.CascadeTRAC
   - Click "Code" → "Codespaces" → Open existing or create new

2. **Switch to Local VSCode**
   - In the browser VSCode, click the ☰ menu (top-left)
   - Choose "Open in VS Code Desktop"
   - Browser will ask "Open Visual Studio Code?"
   - Click "Open" or "Allow"

3. **Automatic Connection**
   - Your local VSCode launches and connects automatically
   - Takes 5-10 seconds

**You're now connected!**

#### STEP 3: Verify Connection

**Check Your Connection Status**

Look at the **bottom-left corner** of VSCode:
- ✅ **"Codespaces: [name]"** = Connected to codespace (you're in Linux!)
- ❌ **Just "Windows"** or nothing = Local Windows machine

**Quick Verification Commands**

Open a terminal in VSCode (`Ctrl+`` or Terminal → New Terminal):

```bash
# Check what system you're on
uname -a
# Should show: "Linux ... codespaces ..."

# Check your location
pwd
# Should show: /workspaces/trac.labs.CascadeTRAC

# Check environment
echo $CODESPACES
# Should show: true

# Check home directory
echo $HOME
# Should show: /home/codespace
```

If these commands show Linux paths → ✅ You're connected to codespace!

#### STEP 4: Working in Connected Codespace

**Everything Works Like Normal VSCode, BUT...**

✅ **What's Running in the Codespace (Linux)**:
- All files in the File Explorer
- Any terminal commands you run
- Any processes you start (npm, python, etc.)
- Git operations
- File saves
- Extensions installed in the codespace

✅ **What's Running Locally (Windows)**:
- VSCode interface itself
- Your keyboard/mouse
- VSCode settings sync (if enabled)
- Local clipboard

**Opening and Editing Files**

```bash
# Everything works exactly like local VSCode!

# Open file
# File → Open File... or Ctrl+O
# Browse to any file in `/workspaces/trac.labs.CascadeTRAC/`

# Create new file
# Right-click in Explorer → New File
# Type in terminal: touch newfile.ts

# Edit files
# Just click and type - saves automatically to codespace

# Search files
# Ctrl+P to quick open
# Ctrl+Shift+F to search across all files
```

**Running Commands**

```bash
# Open terminal: Ctrl+` or Terminal → New Terminal

# Build project
npm run build

# Run development server
npm run dev

# Run tests
npm test

# Install packages
npm install packagename

# Git operations
git status
git add .
git commit -m "Your message"
git push
```

**Using Copilot in the Codespace**

```bash
# Copilot automatically loads repository config!
# Open Copilot Chat: Ctrl+Shift+I or click chat icon

# Verify config is loaded
# Ask Copilot: "What are the build commands for this project?"
# It should reference .github/copilot-instructions.md

# Check active instructions
# In Copilot response, look for "References" section
# Should show which instruction files were used
```

#### STEP 5: Disconnecting from Codespace

**When You're Done Working**

**Option 1: Keep Codespace Running (Recommended)**
- Just close VSCode window (X button)
- Codespace stays active for 30 minutes of inactivity
- Can reconnect anytime and continue where you left off
- Files and processes remain intact

**Option 2: Stop Codespace (Save Resources)**
1. Press `Ctrl+Shift+P`
2. Type: `Codespaces: Stop Current Codespace`
3. Press Enter
4. Codespace stops (but doesn't delete)
5. Can restart later - all files preserved

**Option 3: Delete Codespace (Clean Up)**
1. Open Remote Explorer (left sidebar)
2. Find your codespace in the list
3. Right-click → "Delete Codespace"
4. Confirm deletion
5. **Warning**: All uncommitted changes are lost!

**Important**: Always commit and push your changes before deleting a codespace!

```bash
# Before stopping/deleting, save your work:
git add .
git commit -m "Work in progress"
git push
```

#### STEP 6: Reconnecting Later

**Next Time You Want to Work**

1. **Open VSCode on Windows**
2. **Open Remote Explorer** (Ctrl+Shift+P → "Remote Explorer")
3. **Find Your Codespace** in the list
4. **Connect**: Right-click → "Connect to Codespace"
5. **Continue Working** - everything is exactly as you left it!

If you deleted the codespace, create a new one (Step 2 above).

### COMMON WORKFLOWS: LOCAL VSCODE + CODESPACES

**Workflow 1: Daily Development**

```bash
# Morning: Start work
1. Open VSCode on Windows
2. Ctrl+Shift+P → "Codespaces: Connect to Codespace"
3. Select your codespace from list
4. Start coding!

# During day: Edit, test, commit
5. Edit files in VSCode
6. Run commands in terminal
7. Test your changes
8. Commit: git add . && git commit -m "Feature X"
9. Push: git push

# Evening: Finish work
10. Make sure everything is committed and pushed
11. Close VSCode (codespace auto-stops after 30 min)
12. OR manually stop: Ctrl+Shift+P → "Stop Current Codespace"
```

**Workflow 2: Switch Between Local and Codespace**

```bash
# Working locally on Windows
1. Edit files in local VSCode
2. Commit changes: git commit -am "Local changes"
3. Push: git push

# Switch to codespace
4. Ctrl+Shift+P → "Codespaces: Connect to Codespace"
5. In terminal: git pull
6. Continue working with your changes!

# Switch back to local
7. Commit in codespace: git commit -am "Codespace changes"
8. Push: git push
9. Disconnect from codespace (close or stop)
10. In local VSCode: git pull
11. Continue working locally!
```

**Workflow 3: Multiple Codespaces for Different Features**

```bash
# Create codespace for feature A
1. Ctrl+Shift+P → "Create New Codespace"
2. Select branch: feature-a
3. Work on feature A
4. Commit, but don't delete codespace yet

# Create another codespace for feature B
5. Ctrl+Shift+P → "Create New Codespace"
6. Select branch: feature-b
7. Work on feature B

# Switch between them
8. Open Remote Explorer
9. See both codespaces listed
10. Click to switch between them instantly!
11. Each has its own files, processes, terminal history
```

### TROUBLESHOOTING VSCODE + CODESPACES CONNECTION

| Problem | Solution |
|---------|----------|
| **"Can't find GitHub Codespaces extension"** | Search for "GitHub Codespaces" (not "Codespaces"). Make sure you're signed into VSCode with GitHub account. |
| **"No codespaces appear in list"** | Click refresh icon in Remote Explorer. Make sure you're signed in (check bottom-left corner). |
| **"Failed to connect to codespace"** | Codespace might be stopped. Right-click → "Start Codespace" first. Check internet connection. |
| **"Extension not working"** | Reload VSCode (`Ctrl+Shift+P` → "Reload Window"). Check extension is enabled. Update extension to latest version. |
| **"Shows local files instead of codespace files"** | Check bottom-left corner - should say "Codespaces: [name]". If not, not connected. Try connecting again. |
| **"Terminal commands don't work"** | Make sure you're in a terminal (Ctrl+`). Verify you're connected (bottom-left shows "Codespaces"). |
| **"Changes not saving"** | File might be open in browser codespace too. Close browser tab. Check file permissions in terminal: `ls -la filename`. |
| **"Can't commit/push"** | Configure git if first time: `git config --global user.email "you@example.com"` and `git config --global user.name "Your Name"` |
| **"Copilot not working in codespace"** | Copilot extension must be installed in codespace (not just locally). Open Extensions, search "GitHub Copilot", click "Install in Codespace". |
| **"Slow performance/lag"** | Check internet connection. Browser codespace might be better on slow connections. Try stopping/restarting codespace. |

### UNDERSTANDING: WHERE AM I WORKING?

**Visual Indicators**

```
Bottom-Left Corner Shows:
┌────────────────────────────────┐
│ "Codespaces: trac-...abc123"   │  ← You're in a codespace (Linux)
└────────────────────────────────┘

OR

┌────────────────────────────────┐
│ Nothing or "Windows"            │  ← You're on local Windows machine
└────────────────────────────────┘
```

**Quick Check Command**

```bash
# In any terminal, run:
pwd && echo "Home: $HOME" && uname -a

# Local Windows (Git Bash):
# /c/Users/username/repos/project
# Home: /c/Users/username
# MINGW64 or MSYS

# Codespace (Linux):
# /workspaces/trac.labs.CascadeTRAC
# Home: /home/codespace
# Linux ... codespaces ...
```

**File Path Tells You Too**

When you open a file, look at the tab:
- `/workspaces/trac.labs.CascadeTRAC/file.ts` → Codespace
- `C:\Users\username\repos\project\file.ts` → Local Windows

### TIPS FOR EFFICIENT CODESPACES USE

**💡 Pro Tips**

1. **Name Your Codespaces**
   - When creating, add a display name
   - Makes it easy to identify multiple codespaces

2. **Use Branch-Specific Codespaces**
   - Create one codespace per feature branch
   - Switch between them without switching branches

3. **Keep Repository Config**
   - Always use `.github/copilot-instructions.md`
   - Auto-loads in any codespace, any time

4. **Commit Often**
   - Codespaces can be deleted accidentally
   - Committed work is safe on GitHub

5. **Stop When Not Using**
   - Codespaces count against your free hours
   - Stop them when taking breaks

6. **Install Extensions in Codespace**
   - Some extensions need to be "installed in codespace"
   - Look for "Install in Codespace" button in Extensions panel

7. **Use Port Forwarding**
   - When you run a dev server (localhost:3000)
   - VSCode automatically forwards ports
   - Click the notification to open in browser

8. **Check Your Quota**
   - GitHub.com → Settings → Billing → Codespaces
   - See how many hours you've used
   - Free tier: 120 core-hours/month (60 hours on 2-core)

### SETUP APPROACH 1: REPOSITORY CONFIG ONLY (EASIEST)

Use `.github/` or `.trac/` repository configuration that automatically works in any environment.

**Step 1: Setup Repository Config (One Time)**

```bash
# On either Windows OR Codespaces (doesn't matter which)
# Navigate to your repository
cd /workspaces/trac.labs.CascadeTRAC  # Codespaces
# OR
cd C:\Users\username\repos\trac.labs.CascadeTRAC  # Windows

# Create repository config (standard)
mkdir -p .github/agents .github/skills .github/instructions

cat > .github/copilot-instructions.md << 'EOF'
# Project Copilot Instructions

## Build & Test
- Build: npm run build
- Test: npm test
- Dev: npm run dev

## Project Structure
- `.trac/` - TRAC configuration
- `app/` - Application code
- `components/` - React components

## Standards
- TypeScript strict mode
- Follow ESLint rules
- Write tests for new features
EOF

# Commit so it's available everywhere
git add .github/
git commit -m "Add Copilot repository configuration"
git push
```

**Step 2: Use in Both Environments**

✅ **Windows Local**: Open VSCode → Pull latest → Copilot loads `.github/` automatically
✅ **Codespaces**: Create/open codespace → Copilot loads `.github/` automatically

**Result**: Same configuration everywhere, zero additional setup needed!

### SETUP APPROACH 2: PERSONAL CONFIG SYNC (ADVANCED)

Sync your personal `.copilot/` preferences across environments using dotfiles or manual sync.

**Option A: GitHub Dotfiles Repository (Automatic)**

GitHub Codespaces can automatically clone a dotfiles repository and run a setup script.

**Step 1: Create Dotfiles Repository (One Time)**

```bash
# On your local machine OR in a codespace
# Create a new repository called 'dotfiles'
mkdir ~/dotfiles
cd ~/dotfiles

# Create directory structure
mkdir -p copilot/agents copilot/skills

# Create your personal instructions
cat > copilot/copilot-instructions.md << 'EOF'
# My Personal Copilot Preferences

## Code Style
- 2-space indentation
- Single quotes for strings
- Comprehensive comments

## Preferences
- Always show type annotations
- Prefer functional patterns
- Write detailed commit messages
EOF

# Create installation script
cat > install.sh << 'EOF'
#!/bin/bash
# Copilot dotfiles installation script

echo "Installing Copilot personal configuration..."

# Create .copilot directory
mkdir -p ~/.copilot/{agents,skills}

# Copy configuration files
if [ -d "$HOME/dotfiles/copilot" ]; then
  cp -r $HOME/dotfiles/copilot/* ~/.copilot/
  echo "✓ Copilot configuration installed"
else
  echo "⚠ Copilot directory not found in dotfiles"
fi

# Set up environment variables
if ! grep -q "COPILOT_CUSTOM_INSTRUCTIONS_DIRS" ~/.bashrc; then
  echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$PWD/.trac"' >> ~/.bashrc
  echo "✓ Environment variables added to .bashrc"
fi

echo "✓ Copilot setup complete!"
EOF

chmod +x install.sh

# Create README
cat > README.md << 'EOF'
# My Dotfiles

Personal configuration including Copilot settings.

## What's Included
- Copilot personal instructions
- Copilot personal agents
- Copilot personal skills
EOF

# Initialize git and push
git init
git add .
git commit -m "Initial dotfiles with Copilot config"
gh repo create dotfiles --public --source=. --push
# OR manually create repo on GitHub and push
```

**Step 2: Configure GitHub to Use Dotfiles**

1. Go to https://github.com/settings/codespaces
2. Under "Dotfiles", check "Automatically install dotfiles"
3. Select your `dotfiles` repository
4. Set install command to: `./install.sh`

**Step 3: Use in Codespaces**

✅ Every new codespace automatically runs your install script
✅ Your personal config is available immediately

**Option B: Manual Sync with Git**

Keep personal config in a private repository and clone manually.

```bash
# Setup (one time per environment)

# === In Windows Local ===
# Create personal config
$env:USERPROFILE\.copilot\copilot-instructions.md
# Edit your personal preferences

# Create private repo for personal config
cd $env:USERPROFILE
git init copilot-config
cd copilot-config
cp -r $env:USERPROFILE\.copilot\* .
git add .
git commit -m "Personal Copilot config"
gh repo create copilot-config --private --source=. --push

# === In Codespaces ===
# Clone your personal config
cd ~
gh repo clone username/copilot-config
cp -r ~/copilot-config/* ~/.copilot/

# Done! Personal config now active in codespace
```

**Option C: Simple File Copy (Quick & Manual)**

For quick one-time setup or testing.

```powershell
# === On Windows Local ===
# Export your config to repository
cd C:\Users\username\repos\trac.labs.CascadeTRAC
mkdir -p .copilot-sync
cp -r $env:USERPROFILE\.copilot\copilot-instructions.md .copilot-sync\
# DON'T commit - add to .gitignore
echo ".copilot-sync/" >> .gitignore

# Upload to temporary location
# Option 1: gh gist
gh gist create .copilot-sync/copilot-instructions.md --public

# Option 2: Copy-paste to GitHub Gist manually
# Visit https://gist.github.com/ and paste content
```

```bash
# === In Codespaces ===
# Download your config
mkdir -p ~/.copilot
cd ~/.copilot

# Option 1: Download from gist
gh gist view GIST_ID --raw > copilot-instructions.md

# Option 2: If you have SSH access to your Windows machine
scp username@your-ip:C:/Users/username/.copilot/copilot-instructions.md ~/.copilot/

# Option 3: Copy-paste content manually via GitHub Gist
curl https://gist.githubusercontent.com/username/GIST_ID/raw/... > copilot-instructions.md
```

### SWITCHING BETWEEN ENVIRONMENTS

**From Windows Local → Codespaces**

```bash
# On Windows Local - Make sure everything is committed
git add .
git commit -m "WIP: Current work"
git push

# Open GitHub repository in browser
# Click "Code" → "Codespaces" → "Create codespace" or open existing

# In Codespaces (opens automatically in browser)
# Repository is automatically cloned
# .github/ config is automatically available
# Personal config (if using dotfiles) is automatically installed
# Continue working!
```

**From Codespaces → Windows Local**

```bash
# In Codespaces - Commit your work
git add .
git commit -m "WIP: Current work"
git push

# On Windows Local - Open VSCode
# Pull latest changes
git pull

# Continue working!
```

**Quick Switch Commands**

```bash
# === Check what environment you're in ===
uname -a  # Linux/Codespaces: 'Linux'  Windows: command not found (use PowerShell)
echo $HOME  # Linux: /home/codespace  Windows: C:\Users\username
echo $CODESPACES  # Codespaces: 'true'  Local: (empty)

# === Verify Copilot config location ===
# Works in both environments
ls -la ~/.copilot/copilot-instructions.md  # Personal config
ls -la .github/copilot-instructions.md      # Repo config
echo $COPILOT_CUSTOM_INSTRUCTIONS_DIRS      # Custom dirs

# === Quick sync check (in any environment) ===
git status  # Check for uncommitted changes
git log --oneline -1  # Check latest commit
```

### ENVIRONMENT-SPECIFIC CONFIGURATIONS

Sometimes you need different settings per environment.

**Using Environment Detection**

```markdown
<!-- In .github/copilot-instructions.md -->
# Copilot Instructions

## Build Commands

**Windows Local**: Use `npm.cmd` or ensure Node is in PATH
- Build: `npm run build`
- Test: `npm test`

**Linux/Codespaces**: Standard npm commands
- Build: `npm run build`
- Test: `npm test`

## Path Conventions
- Use forward slashes `/` in code (works everywhere)
- Git handles line ending conversions automatically
- Use relative paths: `./src/file.ts` not `C:\...\file.ts`
```

**Conditional Setup in Dotfiles**

```bash
# In ~/dotfiles/install.sh
#!/bin/bash

# Detect environment
if [ "$CODESPACES" = "true" ]; then
  echo "Setting up for GitHub Codespaces..."
  export COPILOT_THEME="dark"
  export COPILOT_MODEL="claude-sonnet-4"
elif [[ "$OSTYPE" == "msys" ]] || [[ "$OSTYPE" == "win32" ]]; then
  echo "Setting up for Windows..."
  export COPILOT_THEME="light"
else
  echo "Setting up for Linux..."
  export COPILOT_THEME="dark"
fi

# Common setup
mkdir -p ~/.copilot
cp -r ~/dotfiles/copilot/* ~/.copilot/
```

### TROUBLESHOOTING CROSS-PLATFORM ISSUES

| Problem | Solution |
|---------|----------|
| "Config works locally but not in Codespaces" | Check `.github/` is committed and pushed. Pull in codespace. |
| "Personal config not syncing" | Verify dotfiles repo is set in GitHub settings. Check install script. |
| "Path not found errors" | Use relative paths. Avoid `C:\` hardcoded paths. |
| "Line ending issues" | Git should auto-convert. Check `.gitattributes` has `* text=auto` |
| "Env var not set in Codespaces" | Add to dotfiles `install.sh` or `.bashrc`. Check with `echo $VAR` |
| "Instructions not loading" | Verify file exists: `ls -la .github/copilot-instructions.md` |
| "Different tools installed" | Document requirements in `.github/copilot-instructions.md` |
| "Can't find agent/skill" | Check files committed to repo. Personal agents only work with dotfiles sync |

### BEST PRACTICES FOR HYBRID WORKFLOW

**✅ DO: Put in Repository (`.github/` or `.trac/`)**
- Project build/test commands
- Project structure documentation
- Team coding standards
- Project-specific agents
- Project-specific skills
- MCP setup workflows

**❌ DON'T: Put in Repository**
- Personal preferences (indentation, quotes, etc.)
- Personal API keys or tokens
- Personal agent definitions
- Machine-specific paths (`C:\Users\...`)

**✅ DO: Use Dotfiles for Personal Config**
- Personal coding style preferences
- Personal keyboard shortcuts
- Personal Copilot agents (for all your projects)
- Personal skills
- Personal .bashrc, .zshrc, etc.

**✅ DO: Use Relative Paths**
```typescript
// ✅ Good - works everywhere
import { helper } from './utils/helper';
const configPath = path.join(__dirname, 'config.json');

// ❌ Bad - breaks in different environments
import { helper } from 'C:\Users\me\project\utils\helper';
const configPath = 'C:\Users\me\project\config.json';
```

**✅ DO: Document Platform Differences**
```markdown
<!-- In copilot-instructions.md -->
## Platform-Specific Notes

### Windows Local Development
- Install Node.js from nodejs.org
- Use Git Bash or PowerShell
- VS Code: Install "Remote Development" extension

### GitHub Codespaces
- Node.js pre-installed
- Uses bash by default
- VS Code in browser or desktop with Remote-Codespaces
```

### QUICK START: WINDOWS + CODESPACES WORKFLOW

**Complete Setup (5 minutes)**

```bash
# === 1. Setup Repository Config (Do Once) ===
# Can do in Windows OR Codespaces, doesn't matter

# Create repository config
mkdir -p .github/agents .github/skills
echo "# Project Copilot Instructions" > .github/copilot-instructions.md
# Edit .github/copilot-instructions.md with project info

git add .github/
git commit -m "Add Copilot config"
git push

# === 2. (Optional) Setup Personal Config Sync ===
# If you want your personal preferences in both places

# Quick method: Create gist with personal config
gh gist create ~/.copilot/copilot-instructions.md --public
# Save the gist URL

# In other environment:
mkdir -p ~/.copilot
gh gist view GIST_ID --raw > ~/.copilot/copilot-instructions.md

# === 3. Daily Workflow ===
# Work on Windows: edit, commit, push
# Switch to Codespaces: your commits are there
# Work in Codespaces: edit, commit, push
# Switch to Windows: pull changes, continue
```

**Verification Checklist**

✅ Repository config committed and pushed
```bash
git log --oneline | grep -i copilot
ls -la .github/copilot-instructions.md
```

✅ Config loads in Windows
```powershell
# Open VSCode
# Open Copilot Chat
# Send: "What are the build commands for this project?"
# Should reference .github/copilot-instructions.md
```

✅ Config loads in Codespaces
```bash
# Open or create codespace
# Open Copilot Chat
# Send: "What are the build commands for this project?"
# Should reference .github/copilot-instructions.md
```

✅ Personal config synced (if using)
```bash
# In both environments
cat ~/.copilot/copilot-instructions.md
# Should show same content
```

### RECOMMENDED APPROACH

For most users working between Windows and Codespaces:

1. **Use `.github/` for everything team-wide** ✅
   - Zero setup required
   - Works automatically everywhere
   - Team gets same experience

2. **Use dotfiles IF you want personal sync** (optional)
   - Setup once: create dotfiles repo with install script
   - Configure in GitHub settings
   - Automatically works in new codespaces

3. **Skip personal config sync IF you don't need it**
   - Repository config is usually enough
   - Less complexity
   - Fewer things to maintain

**Simple Rule**: If everyone on the team should have it → `.github/`. If only you need it → personal config.

### FREQUENTLY ASKED QUESTIONS (FAQ)

**Q1: Do I need to install Node.js/npm on my Windows machine to use codespaces?**
**A**: NO! That's the beauty of codespaces. Everything is pre-installed in the Linux container. Your Windows machine only needs VSCode and the GitHub Codespaces extension. Just connect and start coding.

**Q2: If I edit files in local VSCode connected to a codespace, where are the files actually stored?**
**A**: In the Linux codespace (cloud), NOT on your Windows machine. When you connect, VSCode shows you the remote files. All saves go to the codespace. Your Windows machine is just displaying them.

**Q3: What happens to my work if I close VSCode while connected to a codespace?**
**A**: Your work is safe! The codespace stays running for 30 minutes after you disconnect. Your files, terminal sessions, running processes - everything stays intact. Just reconnect and continue.

**Q4: Can I work on the same repository both locally (Windows) AND in a codespace?**
**A**: YES! This is common. Strategy:
1. Work locally: edit, commit, push
2. Work in codespace: pull, edit, commit, push
3. Keep git commits synchronized
4. `.github/copilot-instructions.md` works in both places automatically

**Q5: Do I need to configure Copilot twice (once locally, once in codespace)?**
**A**: NO! Use `.github/copilot-instructions.md` - it auto-loads in BOTH local VSCode AND codespaces. One config, works everywhere.

**Q6: Can multiple people work in the same codespace?**
**A**: NO. Each codespace is personal to you. But everyone can have the same configuration by using the same repository `.github/` files.

**Q7: What happens if I delete a codespace? Do I lose my work?**
**A**: IF you committed and pushed, NO - your work is safe on GitHub. IF you didn't commit, YES - uncommitted changes are lost. **Always commit before deleting!**

**Q8: How much does using codespaces cost?**
**A**: GitHub Free tier gives you 120 core-hours/month free. With a 2-core machine, that's 60 hours of use. For most developers, this is enough. Check usage at: github.com → Settings → Billing → Codespaces

**Q9: Can I use my local VSCode extensions in a codespace?**
**A**: SOME extensions work automatically, SOME need to be "installed in codespace". When you first connect, VSCode may prompt to install extensions in the codespace. Click "Install". OR manually: Extensions panel → find extension → "Install in Codespace" button.

**Q10: Why does Copilot show different suggestions in codespace vs local Windows?**
**A**: If you're using different instruction files:
- Local Windows might read: `C:\Users\...\.copilot\copilot-instructions.md`
- Codespace reads: `/home/codespace/.copilot/copilot-instructions.md` + `.github/copilot-instructions.md`
Solution: Use `.github/copilot-instructions.md` (works in both)

**Q11: Can I run a development server (like `npm run dev`) in codespace and view it in my Windows browser?**
**A**: YES! VSCode automatically forwards ports. When you run a server, you'll see a notification: "Your application running on port 3000 is available." Click it to open in your browser. Works seamlessly.

**Q12: What if I'm on slow internet? Will codespaces be laggy?**
**A**: Local VSCode → Codespace works well even on slower connections because only small amounts of data are sent (your keystrokes, file changes). Browser-based codespace might be laggier. For very slow connections, working fully local might be better.

**Q13: Can I copy-paste between my Windows machine and the codespace?**
**A**: YES! Clipboard works transparently. Copy on Windows, paste in codespace terminal - it just works. Same in reverse.

**Q14: What's the difference between "Codespaces" in browser and "Local VSCode connected to Codespaces"?**
**A**: 
- **Browser**: VSCode runs IN the browser. Slightly laggy, no local themes/settings.
- **Local VSCode**: VSCode runs on your Windows machine, connected remotely. Faster, better shortcuts, local settings.
Both are using the SAME Linux container for your code.

**Q15: If I commit files in the codespace, do they appear in my local Windows git?**
**A**: YES, after you `git push` from codespace and `git pull` on local Windows. Git synchronizes everything via GitHub.

**Q16: Can I switch branches in a codespace?**
**A**: YES, just like normal git: `git checkout branch-name`. OR create a separate codespace for each branch (recommended for parallel work).

**Q17: How do I know if I'm connected to a codespace or working locally?**
**A**: Look at **bottom-left corner** of VSCode:
- Shows "Codespaces: [name]" → Connected to codespace
- Shows nothing or "Windows" → Working locally on Windows

**Q18: Do files in `.github/` get committed to git?**
**A**: YES! That's the point. `.github/copilot-instructions.md` should be committed so it works for everyone. BUT `~/.copilot/` files should NEVER be committed (they're in your home directory, not the repo).

**Q19: Can I use the codespace CLI directly from Windows terminal?**
**A**: NO, not directly. The Linux terminal is in the cloud. You access it through:
1. VSCode terminal (Ctrl+` when connected)
2. GitHub CLI: `gh codespace ssh` (connects you to the codespace)

**Q20: What if I want to work on a different repository?**
**A**: Create a new codespace for that repository! You can have multiple codespaces from different repos. Switch between them in the Remote Explorer panel.

**Q21: Is my `.github/copilot-instructions.md` private or public?**
**A**: Follows your repository visibility. Private repo = private config. Public repo = public config. Don't put secrets in instruction files!

**Q22: Can I use `.trac/` instead of `.github/` in codespaces?**
**A**: YES, but you need to set `COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac"` in the codespace. Easier to just use `.github/` which works automatically everywhere.

**Q23: What happens if I accidentally edit files in both places without pushing?**
**A**: You'll have a merge conflict. When you push from one place and pull from another, git will ask you to resolve the conflict. Best practice: Always push after committing, pull before starting work.

**Q24: Can I debug Node.js/Python/etc. applications in a codespace?**
**A**: YES! Full debugging works. Press F5 or use Run & Debug panel. Breakpoints, variable inspection, everything works just like local debugging.

**Q25: How do I delete all my codespaces at once?**
**A**: GitHub.com → Settings → Codespaces → Click "..." on any codespace → "Delete" (repeat for each). OR use GitHub CLI: `gh codespace delete --all`

## COMPLETE QUICK REFERENCE

### ALL FILE LOCATIONS AT A GLANCE

**Legend**: 🏠 = Personal (home dir, never commit) | 📦 = Repository (commit to git) | 🏢 = Organization | ⚙️ = Custom (configurable)

| File/Directory | Location | Purpose | Scope | Path Type | Commit? |
|----------------|----------|---------|-------|-----------|----------|
| `copilot-instructions.md` | `.github/` | Repository instructions | Repository | 📦 Repo | ✅ Yes |
| `copilot-instructions.md` | `~/.copilot/` | Personal instructions (highest priority) | User | 🏠 Personal | ❌ Never |
| `copilot-instructions.md` | `~/.trac/` | Personal TRAC instructions | User | 🏠 Personal + ⚙️ Custom | ❌ Never |
| `copilot-instructions.md` | `.trac/` | Repository TRAC instructions | Repository | 📦 Repo + ⚙️ Custom | ✅ Yes |
| `*.instructions.md` | `.github/instructions/` | Path-specific instructions | Repository paths | 📦 Repo | ✅ Yes |
| `*.agent.md` | `.github/agents/` | Custom agent definitions | Repository | 📦 Repo | ✅ Yes |
| `*.agent.md` | `~/.copilot/agents/` | Personal agent definitions | User (CLI only) | 🏠 Personal | ❌ Never |
| `*.agent.md` | `~/.trac/agents/` | Personal TRAC agents | User | 🏠 Personal + ⚙️ Custom | ❌ Never |
| `*.agent.md` | `.trac/agents/` | Repository TRAC agents | Repository | 📦 Repo + ⚙️ Custom | ✅ Yes |
| `*.agent.md` | `.github-private/agents/` | Org/enterprise agents | Organization | 🏢 Org | ✅ Yes (org repo) |
| `SKILL.md` | `.github/skills/[name]/` | Repository skill | Repository | 📦 Repo | ✅ Yes |
| `SKILL.md` | `~/.copilot/skills/[name]/` | Personal skill | User | 🏠 Personal | ❌ Never |
| `SKILL.md` | `~/.trac/skills/[name]/` | Personal TRAC skill | User | 🏠 Personal + ⚙️ Custom | ❌ Never |
| `SKILL.md` | `.trac/skills/[name]/` | Repository TRAC skill | Repository | 📦 Repo + ⚙️ Custom | ✅ Yes |
| `SKILL.md` | `.claude/skills/[name]/` | Claude-specific skill | Repository/User | 📦 or 🏠 | Depends |
| `AGENTS.md` | Any directory | General agent instructions | Directory tree | 📦 Repo | Usually Yes |
| `CLAUDE.md` | Project root | Claude-specific instructions | Project root only | 📦 Repo | ✅ Yes |
| `GEMINI.md` | Project root | Gemini-specific instructions | Project root only | 📦 Repo | ✅ Yes |
| `.agent.md` | Any directory | Hidden agent instructions | Directory tree | 📦 Repo | Usually Yes |
| `config.json` | `~/.copilot/` | CLI configuration | User | 🏠 Personal | ❌ Never |
| `mcp-config.json` | `~/.copilot/` | MCP server configuration | User | 🏠 Personal | ❌ Never |
| `copilot-setup-steps.yml` | `.github/workflows/` | Agent setup workflow | Repository | 📦 Repo | ✅ Yes |
| Session state | `~/.copilot/session-state/` | CLI session persistence | User | 🏠 Personal | ❌ Never |
| Logs | `~/.copilot/logs/` | CLI operation logs | User | 🏠 Personal | ❌ Never |
| Cache | `~/.copilot/cache/` | Cached responses | User | 🏠 Personal | ❌ Never |

**Important Notes**:
- **`~/.copilot/`**: Standard personal config location, works without any setup
- **`~/.trac/`**: Custom personal config, requires `COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac"`
- **`.github/`**: Standard repo location, always works, recommended for teams
- **`.trac/`**: Custom repo location, requires env var, useful for TRAC-specific structure
- **Colon `:` separates multiple directories** in `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`
- **Left to right priority**: Leftmost directory has highest priority

### ALL CLI COMMANDS AT A GLANCE

| Command | Purpose | Example |
|---------|---------|---------|
| `copilot` | Start CLI | `copilot` |
| `copilot --resume` | Resume previous session | `copilot --resume` |
| `copilot --continue` | Resume most recent session | `copilot --continue` |
| `copilot --agent=name` | Start with specific agent | `copilot --agent=reviewer` |
| `copilot --allow-all` | Bypass all permissions | `copilot --allow-all` |
| `/help` | Show all commands | `/help` |
| `/models` | Show available models | `/models` |
| `/model [name]` | Switch model | `/model claude-sonnet-4` |
| `/agent [name]` | Switch agent | `/agent code-review` |
| `/new` or `/clear` | New conversation | `/new` |
| `/resume [id]` | Switch session | `/resume abc123` |
| `/session` | Show session info | `/session` |
| `/session rename` | Rename session | `/session rename my-session` |
| `/tasks` | View background tasks | `/tasks` |
| `/compact` | Compress history | `/compact` |
| `/context` | View token usage | `/context` |
| `/usage` | Show session statistics | `/usage` |
| `/plan [prompt]` | Create implementation plan | `/plan add auth` |
| `/diff` | Show current changes | `/diff` |
| `/review` | Quick code review | `/review` |
| `/delegate [task]` | Delegate to coding agent | `/delegate fix bug` |
| `& [task]` | Delegate shortcut | `& implement feature` |
| `/mcp show` | List MCP servers | `/mcp show` |
| `/mcp add` | Add MCP server | `/mcp add` |
| `/mcp edit` | Edit MCP server | `/mcp edit server-name` |
| `/mcp delete` | Remove MCP server | `/mcp delete server-name` |
| `/add-dir <dir>` | Add trusted directory | `/add-dir /home/user/project` |
| `/list-dirs` | Show trusted directories | `/list-dirs` |
| `/cwd` | Show current directory | `/cwd` |
| `/cd <dir>` | Change directory | `/cd src/` |
| `/theme show` | Show current theme | `/theme show` |
| `/theme set` | Set theme | `/theme set dark` |
| `/experimental on` | Enable experimental features | `/experimental on` |
| `/reset-allowed-tools` | Clear tool approvals | `/reset-allowed-tools` |
| `/yolo` | Bypass permission prompts | `/yolo` |
| `/share file` | Export to markdown | `/share file report.md` |
| `/share gist` | Share as GitHub gist | `/share gist` |
| `/terminal-setup` | Configure terminal | `/terminal-setup` |

### ALL KEYBOARD SHORTCUTS AT A GLANCE

| Shortcut | Action |
|----------|--------|
| `Ctrl+X` → `/` | Run slash command |
| `Shift+Tab` | Toggle plan mode |
| `Esc` | Cancel operation |
| `Ctrl+C` | Cancel/clear/exit |
| `Ctrl+D` | Shutdown |
| `Ctrl+L` | Clear screen |
| `Ctrl+O` | Expand recent timeline |
| `Ctrl+E` | Expand all timeline |
| `Ctrl+T` | Toggle reasoning display |
| `Up/Down` | Navigate command history |
| `Tab` | Auto-complete |

### ALL AGENT FRONTMATTER PROPERTIES AT A GLANCE

| Property | Type | Required | Description | Example |
|----------|------|----------|-------------|---------|
| `name` | string | Yes | Unique agent identifier | `code-reviewer` |
| `description` | string | Yes | What the agent does | `Reviews code for bugs` |
| `tools` | array | No | Available tools (`*` for all) | `["view", "edit", "bash"]` |
| `model` | string | No | AI model to use (IDE only) | `claude-sonnet-4` |
| `target` | string | No | Environment restriction | `vscode` or `github-copilot` |
| `mcp-servers` | object | No | MCP server config (org/ent only) | See MCP section |

### ALL INSTRUCTION FRONTMATTER PROPERTIES AT A GLANCE

| Property | Type | Required | Description | Example |
|----------|------|----------|-------------|---------|
| `applyTo` | string | Yes | Glob pattern for files | `src/api/**/*.ts` |
| `excludeAgent` | array | No | Agents to exclude | `["code-review"]` |

### ALL SKILL FRONTMATTER PROPERTIES AT A GLANCE

| Property | Type | Required | Description | Example |
|----------|------|----------|-------------|---------|
| `name` | string | Yes | Skill identifier | `debugging` |
| `description` | string | Yes | When to use this skill | `Advanced debugging techniques` |
| `license` | string | No | License information | `MIT` |

### ALL MCP CONFIGURATION PROPERTIES AT A GLANCE

**Local/Stdio MCP Server**
| Property | Type | Required | Description | Example |
|----------|------|----------|-------------|---------|
| `type` | string | Yes | Server type | `"stdio"` or `"local"` |
| `command` | string | Yes | Command to start server | `"node"` |
| `args` | array | Yes | Command arguments | `["server.js"]` |
| `env` | object | No | Environment variables | `{"TOKEN": "COPILOT_MCP_TOKEN"}` |
| `tools` | array | No | Tool allowlist | `["*"]` or `["tool1", "tool2"]` |

**Remote MCP Server**
| Property | Type | Required | Description | Example |
|----------|------|----------|-------------|---------|
| `type` | string | Yes | Server type | `"http"` or `"sse"` |
| `url` | string | Yes | Server endpoint | `"https://api.example.com"` |
| `headers` | object | No | HTTP headers | `{"Authorization": "Bearer token"}` |
| `tools` | array | No | Tool allowlist | `["*"]` or `["tool1"]` |

### ALL BUILT-IN TOOLS AT A GLANCE

| Tool | Environment | Description |
|------|-------------|-------------|
| `view` | All | Read files and directories |
| `edit` | All | Modify existing files |
| `create` | All | Create new files |
| `bash` | All | Execute shell commands |
| `grep` | All | Search file contents (ripgrep) |
| `glob` | All | Find files by pattern |
| `task` | CLI | Launch specialized sub-agents |
| `fetch_copilot_cli_documentation` | CLI | Access CLI help |
| `report_intent` | CLI | Update session intent display |
| `update_todo` | CLI | Manage task checklists |
| `read_bash` | CLI | Read async bash output |
| `write_bash` | CLI | Send input to bash sessions |
| `stop_bash` | CLI | Terminate bash sessions |
| `list_bash` | CLI | List active bash sessions |
| `web_fetch` | CLI | Fetch web content (with permission) |

### ALL ENVIRONMENT VARIABLES AT A GLANCE

| Variable | Purpose | Example | Scope |
|----------|---------|---------|-------|
| `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` | Enable custom directories like `.trac/` | `$HOME/.trac:$PWD/.trac` | User/Project |
| `XDG_CONFIG_HOME` | XDG config directory (Linux standard) | `$HOME/.config` | User |
| `XDG_DATA_HOME` | XDG data directory | `$HOME/.local/share` | User |
| `XDG_CACHE_HOME` | XDG cache directory | `$HOME/.cache` | User |
| `COPILOT_MODEL` | Default model | `claude-sonnet-4` | User |
| `COPILOT_THEME` | CLI theme | `dark` | User |
| `COPILOT_MCP_*` | MCP secrets (GitHub environment) | `COPILOT_MCP_GITHUB_TOKEN` | Repository |

**Critical**: `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` is **required** to use `.trac/` or any non-standard directory name

### ALL AVAILABLE MODELS AT A GLANCE

*Note: Availability depends on Copilot plan tier*

| Model | Best For | Context Window |
|-------|----------|----------------|
| `claude-sonnet-4` | Balanced performance and quality | Large |
| `claude-opus-4` | Complex reasoning tasks | Largest |
| `claude-haiku` | Fast responses | Medium |
| `gpt-5` | General purpose | Large |
| `gpt-5-mini` | Fast and efficient | Medium |
| `gpt-5-turbo` | Balanced | Large |
| `gemini-pro` | Google ecosystem integration | Large |
| `gemini-ultra` | Advanced tasks | Largest |

### SETUP CHECKLIST FOR NEW PROJECTS

**Minimal Setup** (Recommended for all projects)
- [ ] Create `.github/copilot-instructions.md`
- [ ] Add build/test commands to instructions
- [ ] Describe project structure in instructions
- [ ] Commit and push to default branch

**Full Setup** (For teams using custom agents)
- [ ] Create `.github/copilot-instructions.md`
- [ ] Create `.github/agents/` directory
- [ ] Create custom agent(s) in `.github/agents/`
- [ ] Create `.github/skills/` directory (if using skills)
- [ ] Create `.github/instructions/` for path-specific rules
- [ ] Create `.github/workflows/copilot-setup-steps.yml` (if using MCP)
- [ ] Configure `copilot` environment in repository settings
- [ ] Add MCP secrets with `COPILOT_MCP_` prefix
- [ ] Test by assigning coding agent to an issue

**Personal Setup** (One-time per developer)
- [ ] Create `~/.copilot/copilot-instructions.md`
- [ ] Create `~/.copilot/mcp-config.json` (if using MCP locally)
- [ ] Create personal agents in `~/.copilot/agents/` (optional)
- [ ] Create personal skills in `~/.copilot/skills/` (optional)
- [ ] Run `copilot` to initialize CLI

### TROUBLESHOOTING QUICK REFERENCE

| Problem | Solution |
|---------|----------|
| "Instructions not loading" | Check file location, name, and `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` env var |
| "Custom .trac/ directory not working" | Verify `export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac"` is set |
| "Agent not found" | Check `.agent.md` extension, file location, and directory in search path |
| "MCP server not starting" | Check logs in coding agent session, verify secrets with `COPILOT_MCP_` prefix |
| "Permission denied" | Use `/add-dir` to trust directory |
| "Tool not available" | Check agent `tools:` property or use `--allow-all` flag |
| "Context too large" | Use `/compact` to compress history |
| "Session not resuming" | Check `~/.copilot/session-state/` directory exists |
| "Model not available" | Check plan tier, use `/models` to see available models |
| "Instructions not applying" | Check References in response, verify glob pattern and priority order |
| "Skill not loading" | Check `SKILL.md` filename (must be uppercase) and directory structure |
| "Custom directory not recognized" | Echo `$COPILOT_CUSTOM_INSTRUCTIONS_DIRS`, verify colon-separated paths |
| "~/.trac/ ignored" | Must set env var, not auto-discovered like `~/.copilot/` |
| ".github/ works but .trac/ doesn't" | `.github/` is standard (always works), `.trac/` needs env var configuration |

### REGULAR MAINTENANCE TASKS

**Weekly**
- Review and clean old session states: `ls ~/.copilot/session-state/`
- Check log file sizes: `du -sh ~/.copilot/logs`

**Monthly**
- Review and update `.github/copilot-instructions.md`
- Archive old logs: `find ~/.copilot/logs -mtime +30 -delete`
- Clean cache: `rm -rf ~/.copilot/cache/*`

**Per Project**
- Update instructions when project structure changes
- Update custom agents when workflows change
- Review and update path-specific instructions
- Verify MCP secrets are still valid

**Per Release**
- Document known issues in copilot-instructions.md
- Update build/test commands if changed
- Update deployment procedures
- Add migration notes for breaking changes