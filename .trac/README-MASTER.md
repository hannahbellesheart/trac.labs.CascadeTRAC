# TRAC SOFTWARE'S DEVELOPMENT ENVIRONMENT

Complete documentation for working with VSCode, GitHub Codespaces, and GitHub Copilot in the TRAC software ecosystem.

## 📚 DOCUMENTATION INDEX

This documentation is split into two comprehensive guides:

### 1. [README-VSCODE.md](./README-VSCODE.md) - VSCode & GitHub Codespaces

**Complete guide to VSCode and remote development environments**

**What's Covered:**
- ✅ Connecting local VSCode to GitHub Codespaces (step-by-step)
- ✅ Working across Windows and Linux environments
- ✅ Complete beginner's quick start (5 minutes)
- ✅ Daily development workflows
- ✅ Cross-platform best practices
- ✅ Troubleshooting & FAQ (25 questions)

**Read this if you want to:**
- Set up VSCode to connect to codespaces
- Understand how remote development works
- Switch seamlessly between Windows and Linux
- Learn efficient codespace workflows
- Resolve VSCode/Codespaces connection issues

**Quick Start:** [Complete Beginner's Quick Start (5 Minutes)](./README-VSCODE.md#complete-beginners-quick-start-5-minutes)

---

### 2. [README-GHCOPILOT.md](./README-GHCOPILOT.md) - GitHub Copilot Configuration

**Complete guide to GitHub Copilot configuration and features**

**What's Covered:**
- ✅ File locations & configuration hierarchy
- ✅ Agents (built-in, custom, background)
- ✅ Skills (creating and using)
- ✅ Instructions and rules (repository & personal)
- ✅ MCP servers (Model Context Protocol)
- ✅ Tool sets and permissions
- ✅ Sessions (cloud, IDE, CLI)
- ✅ Cross-platform Copilot setup
- ✅ Complete quick reference

**Read this if you want to:**
- Configure GitHub Copilot for your project
- Create custom agents or skills
- Set up MCP servers
- Understand instruction file hierarchy
- Sync Copilot config across environments
- Use `.trac/` custom directories
- Master CLI commands and shortcuts

**Quick Start:** [Setup Checklist for New Projects](./README-GHCOPILOT.md#setup-checklist-for-new-projects)

---

## 🎯 WHICH DOCUMENT DO I NEED?

| Your Goal | Document |
|-----------|----------|
| **Set up my development environment** | [README-VSCODE.md](./README-VSCODE.md) |
| **Connect VSCode to codespaces** | [README-VSCODE.md](./README-VSCODE.md) |
| **Work between Windows and Linux** | [README-VSCODE.md](./README-VSCODE.md) |
| **Configure GitHub Copilot** | [README-GHCOPILOT.md](./README-GHCOPILOT.md) |
| **Create custom agents or skills** | [README-GHCOPILOT.md](./README-GHCOPILOT.md) |
| **Set up MCP servers** | [README-GHCOPILOT.md](./README-GHCOPILOT.md) |
| **Use `.trac/` directories** | [README-GHCOPILOT.md](./README-GHCOPILOT.md) |
| **Troubleshoot VSCode issues** | [README-VSCODE.md § Troubleshooting](./README-VSCODE.md#troubleshooting-vscode--codespaces-connection) |
| **Troubleshoot Copilot issues** | [README-GHCOPILOT.md § Troubleshooting](./README-GHCOPILOT.md#troubleshooting-quick-reference) |
| **Complete beginner** | Start with [README-VSCODE.md](./README-VSCODE.md), then [README-GHCOPILOT.md](./README-GHCOPILOT.md) |

---

## 🚀 QUICK START PATHS

### Path 1: New to Codespaces (5 minutes)
1. Read [README-VSCODE.md § Complete Beginner's Quick Start](./README-VSCODE.md#complete-beginners-quick-start-5-minutes)
2. Connect VSCode to a codespace
3. Start coding!

### Path 2: New Project Setup (10 minutes)
1. Create `.github/copilot-instructions.md` ([Template](./README-GHCOPILOT.md#githubcopilot-instructionsmd-repository-instructions))
2. Add build/test commands
3. Commit and push
4. Copilot auto-loads config everywhere!

### Path 3: Advanced Setup (30 minutes)
1. Set up VSCode + Codespaces ([README-VSCODE.md](./README-VSCODE.md))
2. Configure Copilot repository config ([README-GHCOPILOT.md § Setup](./README-GHCOPILOT.md#setup-approach-1-repository-config-only-easiest))
3. Create custom agents/skills ([README-GHCOPILOT.md § Agents](./README-GHCOPILOT.md#agents))
4. Set up MCP servers ([README-GHCOPILOT.md § MCP](./README-GHCOPILOT.md#mcp-servers))

---

## 📖 DOCUMENT STRUCTURE

### README-VSCODE.md Contents
- Overview of development environment options
- Complete beginner's quick start (5 minutes)
- Understanding: What's actually happening?
- Connecting local VSCode to codespaces (6 steps)
- Common workflows
- Windows ↔️ Linux codespaces
- Environment-specific configurations
- Tips for efficient use
- Cross-platform best practices
- Troubleshooting
- Frequently asked questions (25 Q&As)

### README-GHCOPILOT.md Contents
- File locations & default configurations
- Understanding path types (personal vs repository)
- Configuration file hierarchy
- Agents (built-in, custom, background)
- Prompt files
- Skills
- Instructions and rules
- Hooks
- MCP servers
- Tool sets
- Diagnostics
- Chat settings
- Sessions (cloud, IDE, CLI)
- Cross-platform & multi-environment workflows
- Complete quick reference

---

## 🔗 CROSS-REFERENCES

Both documents are designed to work together and reference each other where appropriate:

- **VSCode document** references Copilot document for configuration across environments
- **Copilot document** references VSCode document for environment setup details
- Each document is **standalone** and can be read independently
- Cross-references use direct links for easy navigation

---

## 💡 TIPS FOR USING THIS DOCUMENTATION

1. **Bookmark both documents** - They're comprehensive references you'll return to
2. **Use the navigation tables** - Each document has a navigation guide at the top
3. **Search within files** - Press `Ctrl+F` to find specific topics
4. **Follow the quick start guides** - Get up and running fast, learn details later
5. **Check the FAQ sections** - Common questions are already answered

---

## 🆘 GETTING HELP

### If something isn't working:

1. **VSCode/Codespaces issues**: See [README-VSCODE.md § Troubleshooting](./README-VSCODE.md#troubleshooting-vscode--codespaces-connection)
2. **Copilot configuration issues**: See [README-GHCOPILOT.md § Troubleshooting](./README-GHCOPILOT.md#troubleshooting-quick-reference)
3. **General questions**: Check the FAQ sections in both documents
4. **Still stuck**: Check the specific section related to your problem

### Common Issues Quick Links:

- [Can't connect to codespace](./README-VSCODE.md#troubleshooting-vscode--codespaces-connection)
- [Copilot instructions not loading](./README-GHCOPILOT.md#troubleshooting-quick-reference)
- [.trac/ directory not working](./README-GHCOPILOT.md#environment-variables)
- [Different behavior in Windows vs Linux](./README-VSCODE.md#windows-local--linux-codespaces)

---

## 📝 DOCUMENTATION MAINTENANCE

**Last Updated**: February 16, 2026

**Version**: 2.0 (Split Architecture)

**Changelog**:
- Split single README into two focused documents
- Added comprehensive navigation and cross-references
- Expanded troubleshooting and FAQ sections
- Added 5-minute quick start guides
- Improved beginner-friendly explanations

---

**Ready to get started?**
- VSCode/Codespaces setup → [README-VSCODE.md](./README-VSCODE.md)
- GitHub Copilot configuration → [README-GHCOPILOT.md](./README-GHCOPILOT.md)
