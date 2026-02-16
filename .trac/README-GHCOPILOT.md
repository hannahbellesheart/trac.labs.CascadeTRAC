# GITHUB COPILOT CONFIGURATION

Complete guide to configuring GitHub Copilot including agents, skills, instructions, MCP servers, tools, prompt files, sessions, and cross-platform setup.

> **💻 Looking for VSCode and Codespaces setup?** See [README-VSCODE.md](./README-VSCODE.md) for connecting local VSCode to codespaces and cross-platform development workflows.

## TABLE OF CONTENTS

- [File Locations & Default Configurations](#file-locations--default-configurations)
- [Agents](#agents)
- [Prompt Files](#prompt-files)
- [Skills](#skills)
- [Instructions and Rules](#instructions-and-rules)
- [Hooks](#hooks)
- [MCP Servers](#mcp-servers)
- [Tool Sets](#tool-sets)
- [Diagnostics](#diagnostics)
- [Chat Settings](#chat-settings)
- [Sessions](#sessions)
- [Cross-Platform & Multi-Environment Workflows](#cross-platform--multi-environment-workflows)
- [Complete Quick Reference](#complete-quick-reference)

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
```

**Repository-Level Configuration** (Custom - alternative to `.github/`)
```
.trac/                             # Custom configuration directory
├── copilot-instructions.md        # Alternative location (requires env var)
├── agents/                        # Custom agents location
│   └── custom-agent.agent.md
└── skills/                        # Custom skills location
    └── skill-name/

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

# Repository-level custom directory
/path/to/project/.trac/            # Project TRAC-specific config
├── copilot-instructions.md        # Project TRAC instructions
├── agents/
│   └── project-agent.agent.md
└── skills/
    └── project-skill/

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
  - bash
  - grep
model: claude-sonnet-4
target: vscode
mcp-servers:
  server-name:
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
    message: "Human-readable message"
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
- Event listener leaks
- Closure memory retention
- Large object caching
```

### ENVIRONMENT VARIABLES

**COPILOT_CUSTOM_INSTRUCTIONS_DIRS**

Critical environment variable for enabling custom configuration directories like `.trac/`.

**Purpose**: Tell Copilot to look for configuration files in non-standard directories

**Format**: Colon-separated list of absolute paths
```bash
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="/path1:/path2:/path3"
```

**Priority**: Left to right (leftmost = highest priority)

**Common Patterns**:
```bash
# Personal TRAC config only
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac"

# Repository TRAC config only
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac"

# Both personal and repository (recommended)
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$PWD/.trac"

# Multiple custom locations
export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$HOME/.myconfig:$PWD/.trac"
```

**Setup Instructions**:

```bash
# === Bash (Linux, macOS, Git Bash on Windows) ===
echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$PWD/.trac"' >> ~/.bashrc
source ~/.bashrc

# === Zsh (macOS default, some Linux) ===
echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.trac:$PWD/.trac"' >> ~/.zshrc
source ~/.zshrc

# === Fish ===
set -Ux COPILOT_CUSTOM_INSTRUCTIONS_DIRS "$HOME/.trac:$PWD/.trac"

# === Windows PowerShell ===
[System.Environment]::SetEnvironmentVariable('COPILOT_CUSTOM_INSTRUCTIONS_DIRS', "$env:USERPROFILE\.trac;$PWD\.trac", 'User')

# === Verify ===
echo $COPILOT_CUSTOM_INSTRUCTIONS_DIRS
```

**Important Notes**:
- **Required** for `.trac/` or any non-`.github/` directory
- `.github/` always works without env var (standard location)
- Must be set in every shell/environment where you use Copilot
- Use absolute paths or shell variables (`$HOME`, `$PWD`)
- Windows: Use semicolon `;` separator instead of colon `:`

**Other Environment Variables**:
```bash
# XDG directories (Linux standard)
export XDG_CONFIG_HOME="$HOME/.config"     # Config files
export XDG_DATA_HOME="$HOME/.local/share"  # Data files
export XDG_CACHE_HOME="$HOME/.cache"       # Cache files

# Copilot CLI preferences
export COPILOT_MODEL="claude-sonnet-4"     # Default model
export COPILOT_THEME="dark"                # CLI theme

# MCP Secrets (set in GitHub repository settings, not shell)
# COPILOT_MCP_GITHUB_TOKEN
# COPILOT_MCP_AZURE_TOKEN
# etc.
```

### FILE NAME CONVENTIONS

**Instruction Files**
- Repository-wide: `copilot-instructions.md`
- Path-specific: `*.instructions.md` (in `.github/instructions/`)
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

# OR add to your shell rc file
echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac:$HOME/.trac"' >> ~/.bashrc

git add .trac .envrc
git commit -m "Add TRAC Copilot configuration"
```

**Initialize with Both Standard and Custom Directories**
```bash
# Use .github/ for team config AND .trac/ for TRAC-specific
mkdir -p .github/{agents,skills,instructions}
mkdir -p .trac/{agents,skills}

# Team configuration in .github/
cat > .github/copilot-instructions.md << 'EOF'
# Team Copilot Instructions
[Team-wide configuration here]
EOF

# TRAC-specific in .trac/
cat > .trac/copilot-instructions.md << 'EOF'
# TRAC-Specific Instructions
[TRAC project configuration here]
EOF

# Set environment variable
echo 'export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$PWD/.trac"' >> ~/.bashrc

git add .github .trac
git commit -m "Add Copilot configuration (standard + TRAC)"
```

### HIDDEN FILES & SYSTEM FILES

Files created and managed automatically by Copilot that you typically shouldn't modify directly.

**Session State Files** (`~/.copilot/session-state/[session-id]/`)
```
session-state/
├── [session-id]/
│   ├── state.json             # Session metadata
│   ├── plan.md               # Current implementation plan
│   ├── files/                # Session artifacts
│   │   ├── output.txt
│   │   └── data.json
│   └── bash/                 # Bash session state
│       └── [session-id].json
```

**Log Files** (`~/.copilot/logs/`)
```
logs/
├── copilot.log               # Main CLI logs
├── mcp-[server-name].log     # MCP server logs
└── [date]/                   # Daily log archives
```

**Cache Files** (`~/.copilot/cache/`)
```
cache/
├── model-responses/          # Cached LLM responses
├── file-analysis/            # Analyzed file metadata
└── .cache.db                 # Cache database
```

**Working Files** (Temporary, auto-deleted)
```
# Backup files (some IDEs)
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
- `/delegate [task]` or `& [task]`: Send task to coding agent
- Creates GitHub issue and assigns coding agent
- Agent works in cloud with full repo access
- Returns results as PR

## CROSS-PLATFORM & MULTI-ENVIRONMENT WORKFLOWS

Working seamlessly with GitHub Copilot across different environments (local Windows, GitHub Codespaces, remote Linux, etc.) with consistent configuration.

> **💻 For detailed VSCode and Codespaces setup**, see [README-VSCODE.md](./README-VSCODE.md)

### OVERVIEW: COPILOT CONFIGURATION ACROSS ENVIRONMENTS

**The Challenge**
- Different environments have different file system paths
- Personal config lives in different locations (Windows vs Linux)
- Need consistent Copilot behavior everywhere

**The Solution**
- Use **repository config** (`.github/` or `.trac/`) for team settings (auto-syncs via git)
- Optionally use **personal config sync** for your preferences (dotfiles or manual)

### PATH DIFFERENCES FOR COPILOT CONFIG

| Component | Windows Local | Linux Codespace | Notes |
|-----------|---------------|-----------------|-------|
| Personal config | `C:\Users\username\.copilot\` | `/home/codespace/.copilot/` | Separate locations |
| Repository config | `C:\Users\username\repos\project\.github\` | `/workspaces/project/.github/` | Same files (via git) |
| Home directory | `C:\Users\username\` | `/home/codespace/` | `~` expands differently |

**Key Insight**: Repository config (`.github/` or `.trac/`) automatically works in BOTH environments because it's committed to git. Personal config requires sync.

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

**Result**: Same Copilot configuration everywhere, zero additional setup needed!

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

# Initialize git and push
git init
git add .
git commit -m "Initial dotfiles with Copilot config"
gh repo create dotfiles --public --source=. --push
```

**Step 2: Configure GitHub to Use Dotfiles**

1. Go to https://github.com/settings/codespaces
2. Under "Dotfiles", check "Automatically install dotfiles"
3. Select your `dotfiles` repository
4. Set install command to: `./install.sh`

**Step 3: Use in Codespaces**

✅ Every new codespace automatically runs your install script
✅ Your personal Copilot config is available immediately

**Option B: Manual Sync**

For quick one-time setup or testing.

```powershell
# === On Windows Local ===
# Export your config
gh gist create $env:USERPROFILE\.copilot\copilot-instructions.md --public
# Note the gist ID
```

```bash
# === In Codespaces ===
# Download your config
mkdir -p ~/.copilot
gh gist view GIST_ID --raw > ~/.copilot/copilot-instructions.md
```

### VERIFICATION: CHECK COPILOT CONFIG LOADING

**In Any Environment (Windows, Codespaces, Linux)**

```bash
# Check repository config exists
ls -la .github/copilot-instructions.md

# Check personal config exists (if using)
ls -la ~/.copilot/copilot-instructions.md

# Check custom directory config (if using)
echo $COPILOT_CUSTOM_INSTRUCTIONS_DIRS
ls -la ~/.trac/copilot-instructions.md  # If using personal TRAC
ls -la .trac/copilot-instructions.md    # If using repository TRAC

# Test in Copilot Chat
# Open Copilot Chat and ask:
# "What are the build commands for this project?"
# Check "References" in response - should show which instruction files were used
```

### SWITCHING COPILOT CONFIG BETWEEN ENVIRONMENTS

**From Windows Local → Codespaces**

```bash
# Copilot repository config automatically available (via git)
# Personal config requires dotfiles or manual sync (see above)

# Verify in codespace:
ls -la .github/copilot-instructions.md  # Should exist
ls -la ~/.copilot/copilot-instructions.md  # May or may not exist
```

**From Codespaces → Windows Local**

```bash
# Copilot repository config automatically available (via git pull)
# Personal config is separate on each machine

# Verify on Windows:
# Repository config: automatically synced via git
# Personal config: stays on Windows machine
```

### RECOMMENDED APPROACH FOR COPILOT

**For Most Users:**

1. **Use `.github/` for everything team-wide** ✅
   - Zero setup required
   - Works automatically everywhere
   - Team gets same Copilot experience

2. **Use dotfiles IF you want personal Copilot preferences synced** (optional)
   - Setup once: create dotfiles repo with install script
   - Configure in GitHub settings
   - Automatically works in new codespaces

3. **Skip personal config sync IF you don't need it**
   - Repository Copilot config is usually enough
   - Less complexity

**Simple Rule**: If everyone should have it → `.github/`. If only you need it → personal config.

### TROUBLESHOOTING COPILOT ACROSS PLATFORMS

| Problem | Solution |
|---------|----------|
| "Copilot not working in codespace" | Copilot extension must be installed in codespace. Extensions → "GitHub Copilot" → "Install in Codespace" |
| "Repository instructions work locally but not in Codespaces" | Check `.github/` is committed and pushed. Pull in codespace: `git pull` |
| "Personal config not syncing" | Verify dotfiles repo is set in GitHub codespace settings. Check install script ran. |
| "Config works in Codespaces but not locally" | Verify repository config is pulled locally: `git pull`. Check personal config exists if needed. |
| "Different suggestions in different environments" | Different instruction files being loaded. Check "References" in Copilot response. |
| "Custom .trac/ directory not working" | Verify `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` environment variable is set correctly |

### COPILOT-SPECIFIC BEST PRACTICES

**✅ DO: Put in Repository (`.github/` or `.trac/`)**
- Project build/test commands for Copilot
- Project structure documentation
- Team coding standards
- Project-specific agents
- Project-specific skills
- MCP setup workflows

**❌ DON'T: Put in Repository**
- Personal Copilot preferences
- Personal API keys for MCP servers
- Personal agent definitions
- Personal ~/.copilot/ files

**✅ DO: Document Platform Differences in Instructions**

```markdown
<!-- In .github/copilot-instructions.md -->
# Copilot Instructions

## Build Commands

**Windows Local**: Use `npm.cmd` or ensure Node is in PATH
- Build: `npm run build`

**Linux/Codespaces**: Standard npm commands
- Build: `npm run build`
```

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
