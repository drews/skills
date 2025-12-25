---
name: operating-systems
description: Platform-aware command and path handling for Windows development environments. Use when working on Windows, encountering path separator issues, or needing command equivalents across shells.
version: 1.0.0
---

# Operating Systems

Platform-specific guidance for operating on Windows machines with Claude Code. Prevents common issues with commands, paths, encodings, and shell differences.

## When to Use

Use this skill when:
- Working on a Windows machine (platform: win32)
- Commands fail due to platform differences
- Path separators cause issues (backslash vs forward slash)
- Encoding problems occur (UTF-8, CRLF vs LF)
- Shell differences cause confusion (PowerShell, CMD, Git Bash)
- User explicitly requests Windows-specific guidance

## Platform Detection

Claude Code provides platform information in the environment:

```
Platform: win32     # Windows
Platform: darwin    # macOS
Platform: linux     # Linux
```

**Always check the platform before making assumptions about:**
- Path separators
- Available commands
- Shell syntax
- Line endings
- File system case sensitivity

## Windows Development Environment

### Shell Options on Windows

Windows developers typically use one of three shells:

| Shell      | Path Style     | Use Case                           | Commands           |
|------------|----------------|------------------------------------|-------------------|
| Git Bash   | Unix-style     | Git operations, Unix-like workflow | ls, cat, grep     |
| PowerShell | Both           | Modern Windows scripting           | Get-Item, pwsh    |
| CMD        | Windows-style  | Legacy Windows scripts             | dir, type, findstr|

**Claude Code Bash tool uses Git Bash** on Windows by default.

### Path Separators

**Windows supports both separators in most contexts:**

```bash
# Git Bash (Unix-style) - PREFERRED in Claude Code
cd C:/Users/Drew/Projects/drews-skills
ls .claude-plugin/

# Windows-style (works but avoid in Git Bash)
cd C:\Users\Drew\Projects\drews-skills  # Backslash needs escaping
dir .claude-plugin\
```

**Best practice for Claude Code:**
- Use forward slashes `/` in Git Bash commands
- Use backslashes `\` only in PowerShell or when required by Windows-specific tools
- Quote paths with spaces: `cd "C:/Program Files/Git"`

### Common Command Equivalents

| Task                  | Git Bash (Claude Code) | PowerShell           | CMD              |
|-----------------------|------------------------|----------------------|------------------|
| List files            | `ls -la`               | `Get-ChildItem`      | `dir`            |
| Read file             | `cat file.txt`         | `Get-Content`        | `type file.txt`  |
| Find text             | `grep pattern`         | `Select-String`      | `findstr`        |
| Environment variable  | `echo $HOME`           | `echo $env:HOME`     | `echo %HOME%`    |
| Current directory     | `pwd`                  | `Get-Location`       | `cd`             |
| Create directory      | `mkdir -p path`        | `New-Item -Type Dir` | `mkdir path`     |
| Remove file           | `rm file.txt`          | `Remove-Item`        | `del file.txt`   |
| Copy file             | `cp src dst`           | `Copy-Item`          | `copy src dst`   |
| Move file             | `mv src dst`           | `Move-Item`          | `move src dst`   |

**For Claude Code, always use Git Bash syntax** unless explicitly working with PowerShell scripts.

### Windows-Specific Pitfalls

#### 1. Path Separator in Commands

**WRONG:**
```bash
ls C:\Users\Drew  # Backslash not escaped
```

**RIGHT:**
```bash
ls C:/Users/Drew  # Forward slash works in Git Bash
ls "C:\\Users\\Drew"  # Or escape backslashes
```

#### 2. Conditional Syntax

**WRONG (CMD syntax in Git Bash):**
```bash
if exist .claude-plugin\hooks (echo exists) else (echo missing)
```

**RIGHT (Bash syntax):**
```bash
test -d .claude-plugin/hooks && echo "exists" || echo "missing"
# or
if [ -d .claude-plugin/hooks ]; then
    echo "exists"
else
    echo "missing"
fi
```

#### 3. Environment Variables

**WRONG:**
```bash
echo %USERPROFILE%  # CMD syntax
echo $env:USERPROFILE  # PowerShell syntax
```

**RIGHT (Git Bash):**
```bash
echo $USERPROFILE
echo $HOME  # Unix equivalent
```

#### 4. Line Endings

Windows uses CRLF (`\r\n`), Unix uses LF (`\n`).

**Git configuration (recommended):**
```bash
# Auto-convert CRLF to LF on commit
git config --global core.autocrlf true

# Ensure consistent line endings in repository
echo "* text=auto" > .gitattributes
```

**Convert line endings manually:**
```bash
# CRLF to LF (Unix-style)
dos2unix file.txt

# LF to CRLF (Windows-style)
unix2dos file.txt
```

#### 5. File Permissions

Windows doesn't use Unix-style permissions (`chmod`).

**WRONG:**
```bash
chmod +x script.sh  # Usually has no effect on Windows
```

**RIGHT:**
```bash
# Git Bash: Files are executable if they have execute bit in Git
git update-index --chmod=+x script.sh

# PowerShell: Use execution policy
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

#### 6. Symbolic Links

Windows requires administrator privileges for symlinks by default.

**Enable developer mode** (Windows 10/11):
- Settings → Update & Security → For developers → Developer Mode

**Then create symlinks:**
```bash
ln -s target link_name
```

**Without developer mode, use hardlinks or junctions:**
```powershell
# PowerShell junction (directory only)
New-Item -ItemType Junction -Path link_name -Target target_dir
```

### Encoding Issues

#### Default Encodings

- **Git Bash**: UTF-8
- **PowerShell**: UTF-8 (PowerShell 7+), UTF-16LE (legacy)
- **CMD**: System code page (often Windows-1252)

#### Fix Encoding Issues

**PowerShell UTF-8 output:**
```powershell
$OutputEncoding = [System.Text.Encoding]::UTF8
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
```

**Read file with specific encoding:**
```bash
# Git Bash
iconv -f UTF-16LE -t UTF-8 input.txt > output.txt

# PowerShell
Get-Content file.txt -Encoding UTF8
```

#### File Encoding in VSCode

Set in `.vscode/settings.json`:
```json
{
  "files.encoding": "utf8",
  "files.eol": "\n"
}
```

## Platform-Specific Guidance for Claude

### Command Selection Algorithm

When executing bash commands in Claude Code on Windows:

1. **Check Platform**: Confirm `Platform: win32` in environment
2. **Use Git Bash syntax**: ls, cat, grep, test, etc.
3. **Use forward slashes**: `C:/Users/Drew/path`
4. **Quote paths with spaces**: `"C:/Program Files/Git/bin"`
5. **Avoid CMD/PowerShell syntax**: unless explicitly scripting .ps1 or .bat files

### Error Recovery

If a command fails on Windows:

**Check for:**
1. Path separator issues (backslash instead of forward slash)
2. CMD syntax in Git Bash (e.g., `if exist`, `%VAR%`)
3. Missing quotes around paths with spaces
4. Assuming Unix commands exist (e.g., `apt-get` on Windows)
5. Case sensitivity assumptions (Windows is case-insensitive)

**Common fixes:**
```bash
# Failed command
ls C:\Users\Drew

# Fixed version
ls C:/Users/Drew

# Failed command with spaces
cd C:/Program Files/Git

# Fixed version
cd "C:/Program Files/Git"
```

### PowerShell Interop

When you need PowerShell-specific features:

**Call PowerShell from Git Bash:**
```bash
pwsh -NoProfile -Command "Get-Process | Where-Object CPU -gt 100"
```

**Recommended for:**
- Windows-specific APIs (.NET access)
- Registry operations
- WMI/CIM queries
- Modern Windows administration

## Quick Reference

### Path Conversion

| Context           | Format                           | Example                     |
|-------------------|----------------------------------|-----------------------------|
| Git Bash commands | Forward slash `/`                | `C:/Users/Drew/file.txt`    |
| PowerShell        | Backslash `\` or forward slash   | `C:\Users\Drew\file.txt`    |
| Windows APIs      | Backslash `\`                    | `C:\\Users\\Drew\\file.txt` |
| Git config        | Forward slash `/`                | `C:/Program Files/Git`      |

### Shell Detection

```bash
# Detect shell environment
if [ -n "$BASH_VERSION" ]; then
    echo "Running in Bash (Git Bash on Windows)"
elif [ -n "$ZSH_VERSION" ]; then
    echo "Running in Zsh"
fi

# Detect OS
if [[ "$OSTYPE" == "msys" ]] || [[ "$OSTYPE" == "cygwin" ]]; then
    echo "Windows (Git Bash/Cygwin)"
elif [[ "$OSTYPE" == "darwin"* ]]; then
    echo "macOS"
elif [[ "$OSTYPE" == "linux-gnu"* ]]; then
    echo "Linux"
fi
```

### Common Windows Directories

| Purpose              | Environment Variable | Git Bash Path                     |
|----------------------|----------------------|-----------------------------------|
| User home            | `$HOME`              | `/c/Users/Drew`                   |
| Desktop              | -                    | `$HOME/Desktop`                   |
| Documents            | -                    | `$HOME/Documents`                 |
| AppData Roaming      | `$APPDATA`           | `/c/Users/Drew/AppData/Roaming`   |
| AppData Local        | `$LOCALAPPDATA`      | `/c/Users/Drew/AppData/Local`     |
| Program Files        | `$PROGRAMFILES`      | `/c/Program Files`                |
| Temp                 | `$TEMP`              | `/c/Users/Drew/AppData/Local/Temp`|

## Best Practices

### 1. Platform-Agnostic Code

Write scripts that work across platforms when possible:

```bash
# Good: Works on Windows (Git Bash), macOS, Linux
if [ -f package.json ]; then
    npm install
fi

# Bad: Windows CMD-specific
if exist package.json npm install
```

### 2. Use Git Bash as Default

Claude Code's Bash tool uses Git Bash on Windows, providing Unix-like environment:

```bash
# These all work in Git Bash on Windows
grep -r "pattern" .
find . -name "*.js"
cat file.txt | wc -l
```

### 3. Explicit PowerShell When Needed

For Windows-specific tasks, explicitly call PowerShell:

```bash
# Get Windows services
pwsh -Command "Get-Service | Where-Object Status -eq 'Running'"

# Check .NET version
pwsh -Command "[System.Environment]::Version"
```

### 4. Handle Paths Defensively

```bash
# Always quote paths that might contain spaces
cd "$HOME/My Documents"

# Normalize path separators (Unix-style in Git Bash)
PROJECT_DIR="C:/Users/Drew/Projects/my-project"
cd "$PROJECT_DIR"
```

### 5. Test Cross-Platform

If writing scripts for distribution:

```bash
#!/bin/bash

# Detect OS and adjust behavior
if [[ "$OSTYPE" == "msys" ]] || [[ "$OSTYPE" == "cygwin" ]]; then
    # Windows-specific behavior
    PYTHON_CMD="python"
else
    # Unix-specific behavior
    PYTHON_CMD="python3"
fi

$PYTHON_CMD script.py
```

## Troubleshooting

### Command Not Found

**Issue:** `command not found: some-tool`

**Windows considerations:**
1. Check if tool is in PATH
2. Git Bash has limited default PATH; may need to add Windows paths
3. Some Windows tools may be .exe files (`tool.exe` vs `tool`)

**Solutions:**
```bash
# Add Windows tool directory to PATH
export PATH="$PATH:/c/Program Files/MyTool/bin"

# Or call .exe explicitly
node.exe --version
```

### Permission Denied

**Issue:** `Permission denied` when running scripts

**Windows solutions:**
```bash
# Git Bash: Mark as executable in Git
git update-index --chmod=+x script.sh

# Or run with bash explicitly
bash script.sh

# PowerShell: Adjust execution policy
pwsh -Command "Set-ExecutionPolicy RemoteSigned -Scope CurrentUser"
```

### Encoding Problems

**Issue:** Garbled text, mojibake characters

**Solutions:**
```bash
# Force UTF-8 in Git Bash
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8

# Convert file encoding
iconv -f WINDOWS-1252 -t UTF-8 input.txt > output.txt
```

## Summary

**Golden rules for Claude Code on Windows:**

1. ✅ Use Git Bash syntax (Unix-style) for Bash tool commands
2. ✅ Use forward slashes `/` in paths
3. ✅ Quote paths with spaces
4. ✅ Check `Platform: win32` before making assumptions
5. ✅ Call PowerShell explicitly when needed: `pwsh -Command "..."`
6. ❌ Avoid backslashes in Git Bash unless escaped
7. ❌ Don't use CMD syntax (`if exist`, `%VAR%`)
8. ❌ Don't assume Unix commands exist (`apt`, `brew`, etc.)

**When in doubt:**
- Default to Git Bash / Unix-style syntax
- Use forward slashes for paths
- Quote everything with spaces
- Test on the actual platform before assuming behavior
