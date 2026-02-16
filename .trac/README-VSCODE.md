# VISUAL STUDIO CODE & GITHUB CODESPACES

Complete guide to working with VSCode across local and cloud environments, including GitHub Codespaces setup and cross-platform development workflows.

> **📘 Looking for GitHub Copilot configuration?** See [README-GHCOPILOT.md](./README-GHCOPILOT.md) for agents, skills, instructions, MCP servers, and tool configuration.

## TABLE OF CONTENTS

- [Section Navigation](#-section-navigation)
- [Overview: Your Development Environment Options](#overview-your-development-environment-options)
- [Complete Beginner's Quick Start (5 Minutes)](#complete-beginners-quick-start-5-minutes)
- [Understanding: What's Actually Happening?](#understanding-whats-actually-happening)
- [Connecting Local VSCode to Codespaces](#connecting-local-vscode-to-codespaces)
- [Common Workflows: Local VSCode + Codespaces](#common-workflows-local-vscode--codespaces)
- [Windows Local ↔️ Linux Codespaces](#windows-local--linux-codespaces)
- [Environment-Specific Configurations](#environment-specific-configurations)
- [Understanding: Where Am I Working?](#understanding-where-am-i-working)
- [Tips for Efficient Codespaces Use](#tips-for-efficient-codespaces-use)
- [Cross-Platform Best Practices](#cross-platform-best-practices)
- [Troubleshooting](#troubleshooting-vscode--codespaces-connection)
- [Frequently Asked Questions (FAQ)](#frequently-asked-questions-faq)

## 📖 SECTION NAVIGATION

**If you are...** → **Go to...**

| Your Situation | Recommended Section |
|----------------|---------------------|
| **Brand new to Codespaces** | [COMPLETE BEGINNER'S QUICK START](#complete-beginners-quick-start-5-minutes) (5 min setup) |
| **Want to understand the concept** | [UNDERSTANDING: WHAT'S ACTUALLY HAPPENING?](#understanding-whats-actually-happening) |
| **Need to install Codespaces extension** | [CONNECTING LOCAL VSCODE TO CODESPACES - Step 1](#step-1-install-required-extension-one-time-setup) |
| **Want detailed step-by-step connection guide** | [CONNECTING LOCAL VSCODE TO CODESPACES](#connecting-local-vscode-to-codespaces) |
| **Looking for daily workflow examples** | [COMMON WORKFLOWS: LOCAL VSCODE + CODESPACES](#common-workflows-local-vscode--codespaces) |
| **Working between Windows and Linux** | [WINDOWS LOCAL ↔️ LINUX CODESPACES](#windows-local--linux-codespaces) |
| **Something not working** | [TROUBLESHOOTING](#troubleshooting-vscode--codespaces-connection) OR [FAQ](#frequently-asked-questions-faq) |
| **Want best practices** | [TIPS FOR EFFICIENT CODESPACES USE](#tips-for-efficient-codespaces-use) |
| **Need to configure Copilot in both environments** | [README-GHCOPILOT.md - Cross-Platform Setup](./README-GHCOPILOT.md#cross-platform--multi-environment-workflows) |

## OVERVIEW: YOUR DEVELOPMENT ENVIRONMENT OPTIONS

**You Have THREE Main Ways to Work on Your Code:**

```
┌─────────────────────────────────────────────────────────────────────┐
│                     YOUR DEVELOPMENT OPTIONS                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1️⃣ LOCAL WINDOWS                                                  │
│     VSCode installed on your Windows machine                       │
│     Files stored: C:\Users\username\repos\                         │
│     ✅ Fast, offline access                                         │
│     ❌ Need to install Node, npm, dependencies                      │
│                                                                     │
│  2️⃣ CODESPACE IN BROWSER                                           │
│     VSCode running in your web browser                             │
│     Files stored: /workspaces/project/ (in cloud)                  │
│     ✅ No installation needed, works anywhere                       │
│     ❌ Requires internet, some browser lag                          │
│                                                                     │
│  3️⃣ LOCAL VSCODE → CODESPACE (BEST OF BOTH! ⭐)                    │
│     VSCode on your Windows machine, connected to cloud Linux       │
│     Files stored: /workspaces/project/ (in cloud)                  │
│     ✅ Local VSCode performance + cloud Linux environment           │
│     ✅ No dependency installation on Windows                        │
│     ✅ Best keyboard shortcuts, clipboard, themes                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Recommended Workflow for Beginners:**
1. Use **Option 3** (Local VSCode → Codespace) as your primary environment
2. Keep git commits synchronized
3. Access from any computer by connecting to the same codespace

**This Document Covers:**
- ✅ How to connect local VSCode to Codespaces (detailed walkthrough)
- ✅ How to switch between environments seamlessly
- ✅ How to troubleshoot common issues
- ✅ Best practices for cross-platform development

**For Copilot Configuration Across Environments:**
See [README-GHCOPILOT.md - Cross-Platform Setup](./README-GHCOPILOT.md#cross-platform--multi-environment-workflows)

## COMPLETE BEGINNER'S QUICK START (5 MINUTES)

**Never Used Codespaces Before? Start Here!**

**🎯 Goal**: Get up and running with VSCode connected to a Codespace in 5 minutes.

**What You'll Do:**
1. Install GitHub Codespaces extension in your local VSCode
2. Connect to a codespace from your Windows machine
3. Start coding!

**📋 Prerequisites:**
- [ ] Windows computer with VSCode installed
- [ ] GitHub account (free is fine)
- [ ] This repository: `hannahbellesheart/trac.labs.CascadeTRAC`
- [ ] Internet connection

**🚀 Let's Go!**

### MINUTE 1-2: Install Extension

1. Open VSCode on your Windows machine
2. Press `Ctrl+Shift+X` (opens Extensions)
3. Search: `GitHub Codespaces`
4. Click "Install" on the official GitHub extension
5. Click the Account icon (bottom-left) → "Sign in with GitHub"
6. Authorize in browser when prompted

✅ **Check**: See your GitHub username in bottom-left corner of VSCode

### MINUTE 3-4: Create & Connect to Codespace

**Method A - From VSCode:**
1. Press `Ctrl+Shift+P` (opens command palette)
2. Type: `Codespaces: Create New Codespace`
3. Select repository: `hannahbellesheart/trac.labs.CascadeTRAC`
4. Select branch: `main`
5. Wait 30-60 seconds... VSCode automatically connects!

**Method B - From GitHub Website (easier):**
1. Go to github.com/hannahbellesheart/trac.labs.CascadeTRAC
2. Click green "Code" button
3. Click "Codespaces" tab
4. Click "Create codespace on main"
5. When it opens in browser, click ☰ menu → "Open in VS Code Desktop"
6. Allow the prompt, and local VSCode launches connected!

✅ **Check**: Bottom-left corner shows "Codespaces: [name]"

### MINUTE 5: Verify Everything Works

In your connected VSCode terminal (press `Ctrl+``):

```bash
# Verify you're in the codespace
pwd
# Should show: /workspaces/trac.labs.CascadeTRAC

# Check environment
echo $CODESPACES
# Should show: true

# Test a command
ls -la
# Should show your repository files
```

✅ **Check**: File `.github/` and other repo files appear in Explorer

**🎉 YOU'RE DONE!** 

Now you can:
- Edit files (they're in the Linux codespace)
- Run commands in terminal (Linux)
- Close VSCode and reconnect anytime
- Work from any computer by connecting to the same codespace

**Next Steps:**
- Read "CONNECTING LOCAL VSCODE TO CODESPACES" below for detailed info
- Explore "COMMON WORKFLOWS" for day-to-day usage patterns
- Check "TROUBLESHOOTING" if anything doesn't work
- **For Copilot setup**: See [README-GHCOPILOT.md](./README-GHCOPILOT.md)

## UNDERSTANDING: WHAT'S ACTUALLY HAPPENING?

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
```

## CONNECTING LOCAL VSCODE TO CODESPACES

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

### STEP 1: Install Required Extension (One-Time Setup)

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

### STEP 2: Create or Connect to a Codespace

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

### STEP 3: Verify Connection

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

### STEP 4: Working in Connected Codespace

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

### STEP 5: Disconnecting from Codespace

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

### STEP 6: Reconnecting Later

**Next Time You Want to Work**

1. **Open VSCode on Windows**
2. **Open Remote Explorer** (Ctrl+Shift+P → "Remote Explorer")
3. **Find Your Codespace** in the list
4. **Connect**: Right-click → "Connect to Codespace"
5. **Continue Working** - everything is exactly as you left it!

If you deleted the codespace, create a new one (Step 2 above).

## COMMON WORKFLOWS: LOCAL VSCODE + CODESPACES

### Workflow 1: Daily Development

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

### Workflow 2: Switch Between Local and Codespace

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

### Workflow 3: Multiple Codespaces for Different Features

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

## WINDOWS LOCAL ↔️ LINUX CODESPACES

**The Challenge**
- **Local Windows machine**: Uses VSCode on Windows with paths like `C:\Users\username\`
- **GitHub Codespaces**: Linux container in browser with paths like `/home/codespace/`
- **Goal**: Same development experience in both environments

### Understanding Path Differences

| Component | Windows Local | Linux Codespace | Notes |
|-----------|---------------|-----------------|-------|
| Home directory | `C:\Users\username\` | `/home/codespace/` | `~` expands differently |
| Repository | `C:\Users\username\repos\project\` | `/workspaces/project\` | Different mount points |
| Separators | `\` (backslash) | `/` (forward slash) | Git handles automatically |
| Line endings | CRLF (`\r\n`) | LF (`\n`) | Git handles automatically |
| Env vars | `%USERPROFILE%` | `$HOME` | Shell differences |

### Switching Between Environments

**From Windows Local → Codespaces**

```bash
# On Windows Local - Make sure everything is committed
git add .
git commit -m "WIP: Current work"
git push

# Open GitHub repository in browser
# Click "Code" → "Codespaces" → "Create codespace" or open existing

# In Codespaces (opens automatically in browser OR connect from local VSCode)
# Repository is automatically cloned
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
uname -a  # Linux/Codespaces: 'Linux'  Windows: command not found
echo $HOME  # Linux: /home/codespace  Windows: C:\Users\username
echo $CODESPACES  # Codespaces: 'true'  Local: (empty)

# === Quick sync check (in any environment) ===
git status  # Check for uncommitted changes
git log --oneline -1  # Check latest commit
```

## ENVIRONMENT-SPECIFIC CONFIGURATIONS

Sometimes you need different settings per environment.

### Using Environment Detection

```bash
# In any script or configuration
if [ "$CODESPACES" = "true" ]; then
  echo "Running in GitHub Codespaces"
  # Codespace-specific setup
elif [[ "$OSTYPE" == "msys" ]] || [[ "$OSTYPE" == "win32" ]]; then
  echo "Running on Windows"
  # Windows-specific setup
else
  echo "Running on Linux"
  # Linux-specific setup
fi
```

### Conditional Setup Scripts

```bash
# In setup.sh or similar
#!/bin/bash

# Detect environment
if [ "$CODESPACES" = "true" ]; then
  echo "Setting up for GitHub Codespaces..."
  export DEV_ENV="codespaces"
elif [[ "$OSTYPE" == "msys" ]] || [[ "$OSTYPE" == "win32" ]]; then
  echo "Setting up for Windows..."
  export DEV_ENV="windows"
else
  echo "Setting up for Linux..."
  export DEV_ENV="linux"
fi

# Common setup
npm install
```

## UNDERSTANDING: WHERE AM I WORKING?

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

## TIPS FOR EFFICIENT CODESPACES USE

### 💡 Pro Tips

1. **Name Your Codespaces**
   - When creating, add a display name
   - Makes it easy to identify multiple codespaces

2. **Use Branch-Specific Codespaces**
   - Create one codespace per feature branch
   - Switch between them without switching branches

3. **Commit Often**
   - Codespaces can be deleted accidentally
   - Committed work is safe on GitHub

4. **Stop When Not Using**
   - Codespaces count against your free hours
   - Stop them when taking breaks

5. **Install Extensions in Codespace**
   - Some extensions need to be "installed in codespace"
   - Look for "Install in Codespace" button in Extensions panel

6. **Use Port Forwarding**
   - When you run a dev server (localhost:3000)
   - VSCode automatically forwards ports
   - Click the notification to open in browser

7. **Check Your Quota**
   - GitHub.com → Settings → Billing → Codespaces
   - See how many hours you've used
   - Free tier: 120 core-hours/month (60 hours on 2-core)

## CROSS-PLATFORM BEST PRACTICES

### ✅ DO: Use Relative Paths

```typescript
// ✅ Good - works everywhere
import { helper } from './utils/helper';
const configPath = path.join(__dirname, 'config.json');

// ❌ Bad - breaks in different environments
import { helper } from 'C:\Users\me\project\utils\helper';
const configPath = 'C:\Users\me\project\config.json';
```

### ✅ DO: Use Git for Synchronization

- Work on Windows: edit, commit, push
- Switch to Codespaces: your commits are there
- Work in Codespaces: edit, commit, push
- Switch to Windows: pull changes, continue

### ❌ DON'T: Keep Multiple Copies

- Don't manually copy files between environments
- Use git as single source of truth
- Let git handle line endings and path conversions

### ✅ DO: Document Platform Differences

```markdown
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

## TROUBLESHOOTING VSCODE + CODESPACES CONNECTION

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
| **"Slow performance/lag"** | Check internet connection. Browser codespace might be better on slow connections. Try stopping/restarting codespace. |

## FREQUENTLY ASKED QUESTIONS (FAQ)

### General Questions

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

**Q5: What happens if I delete a codespace? Do I lose my work?**

**A**: IF you committed and pushed, NO - your work is safe on GitHub. IF you didn't commit, YES - uncommitted changes are lost. **Always commit before deleting!**

### Cost & Performance

**Q6: How much does using codespaces cost?**

**A**: GitHub Free tier gives you 120 core-hours/month free. With a 2-core machine, that's 60 hours of use. For most developers, this is enough. Check usage at: github.com → Settings → Billing → Codespaces

**Q7: What if I'm on slow internet? Will codespaces be laggy?**

**A**: Local VSCode → Codespace works well even on slower connections because only small amounts of data are sent (your keystrokes, file changes). Browser-based codespace might be laggier. For very slow connections, working fully local might be better.

**Q8: Can multiple people work in the same codespace?**

**A**: NO. Each codespace is personal to you. But everyone can have the same environment by using the same repository configuration.

### Extensions & Features

**Q9: Can I use my local VSCode extensions in a codespace?**

**A**: SOME extensions work automatically, SOME need to be "installed in codespace". When you first connect, VSCode may prompt to install extensions in the codespace. Click "Install". OR manually: Extensions panel → find extension → "Install in Codespace" button.

**Q10: Can I run a development server (like `npm run dev`) in codespace and view it in my Windows browser?**

**A**: YES! VSCode automatically forwards ports. When you run a server, you'll see a notification: "Your application running on port 3000 is available." Click it to open in your browser. Works seamlessly.

**Q11: Can I copy-paste between my Windows machine and the codespace?**

**A**: YES! Clipboard works transparently. Copy on Windows, paste in codespace terminal - it just works. Same in reverse.

**Q12: Can I debug Node.js/Python/etc. applications in a codespace?**

**A**: YES! Full debugging works. Press F5 or use Run & Debug panel. Breakpoints, variable inspection, everything works just like local debugging.

### Environment & Configuration

**Q13: What's the difference between "Codespaces" in browser and "Local VSCode connected to Codespaces"?**

**A**: 
- **Browser**: VSCode runs IN the browser. Slightly laggy, no local themes/settings.
- **Local VSCode**: VSCode runs on your Windows machine, connected remotely. Faster, better shortcuts, local settings.
Both are using the SAME Linux container for your code.

**Q14: How do I know if I'm connected to a codespace or working locally?**

**A**: Look at **bottom-left corner** of VSCode:
- Shows "Codespaces: [name]" → Connected to codespace
- Shows nothing or "Windows" → Working locally on Windows

**Q15: Can I use the codespace CLI directly from Windows terminal?**

**A**: NO, not directly. The Linux terminal is in the cloud. You access it through:
1. VSCode terminal (Ctrl+` when connected)
2. GitHub CLI: `gh codespace ssh` (connects you to the codespace)

### Git & Synchronization

**Q16: If I commit files in the codespace, do they appear in my local Windows git?**

**A**: YES, after you `git push` from codespace and `git pull` on local Windows. Git synchronizes everything via GitHub.

**Q17: Can I switch branches in a codespace?**

**A**: YES, just like normal git: `git checkout branch-name`. OR create a separate codespace for each branch (recommended for parallel work).

**Q18: What happens if I accidentally edit files in both places without pushing?**

**A**: You'll have a merge conflict. When you push from one place and pull from another, git will ask you to resolve the conflict. Best practice: Always push after committing, pull before starting work.

### Multiple Codespaces & Management

**Q19: What if I want to work on a different repository?**

**A**: Create a new codespace for that repository! You can have multiple codespaces from different repos. Switch between them in the Remote Explorer panel.

**Q20: How do I delete all my codespaces at once?**

**A**: GitHub.com → Settings → Codespaces → Click "..." on any codespace → "Delete" (repeat for each). OR use GitHub CLI: `gh codespace delete --all`

**Q21: What if my codespace runs out of disk space?**

**A**: Codespaces have limited storage (usually 32GB). Clean up node_modules, build artifacts, docker images. OR upgrade to a larger machine type.

### Advanced Features

**Q22: Can I share my terminal output/screen from codespace with team?**

**A**: YES! Use VS Code Live Share extension. Share your entire codespace session including terminal, editing, and debugging.

**Q23: Do environment variables persist in codespaces?**

**A**: YES, if added to `.bashrc` or `.zshrc` in the codespace. They'll be there when you reconnect. But they're lost if you delete the codespace.

**Q24: Can I use Docker inside a codespace?**

**A**: YES! Docker is pre-installed in codespaces. You can run containers, docker-compose, everything.

**Q25: How do I transfer files between my Windows machine and codespace?**

**A**: 
- Option 1: Use git (commit, push, pull)
- Option 2: Drag and drop in VSCode Explorer
- Option 3: Use `gh codespace cp` command
- Option 4: Open codespace in browser, use Upload Files button

---

> **📘 For GitHub Copilot Configuration**: See [README-GHCOPILOT.md](./README-GHCOPILOT.md) for agents, skills, instructions, MCP servers, and tool configuration across environments.
