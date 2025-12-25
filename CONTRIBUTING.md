# Contributing to Drew's Skills

This guide covers the development workflow, testing, and distribution process for this Claude Code plugin marketplace.

## Table of Contents

- [Development Setup](#development-setup)
- [Local Development Workflow](#local-development-workflow)
- [Testing Changes](#testing-changes)
- [Distribution & Deployment](#distribution--deployment)
- [Version Management](#version-management)
- [Plugin Loading Behavior](#plugin-loading-behavior)
- [Creating New Skills](#creating-new-skills)
- [Marketplace Updates](#marketplace-updates)

## Development Setup

### Prerequisites

- Claude Code CLI installed
- Git configured
- Text editor of choice

### Clone and Setup

```bash
# Clone repository
git clone https://github.com/drews/skills.git ~/projects/drews-skills
cd ~/projects/drews-skills

# Verify structure
ls -la .claude-plugin/
```

## Local Development Workflow

### Understanding Plugin Loading

Claude Code uses a **cache-based plugin system** with important implications:

1. **Installed plugins** are copied to a cache directory, not used in-place
2. **Local development** requires the `--plugin-dir` flag to bypass caching
3. **Changes require restart** - Claude Code doesn't hot-reload plugins

### Fast Iteration During Development

**Use `--plugin-dir` for local testing:**

```bash
# Test a single skill during development
claude --plugin-dir ~/projects/drews-skills

# Test multiple plugins simultaneously
claude --plugin-dir ~/projects/drews-skills --plugin-dir ~/other-plugin
```

**Important:** This bypasses the cache and loads directly from your working directory. Any changes require restarting Claude Code.

### Development Cycle

```bash
# 1. Make changes to skill files
vim my-skill/SKILL.md

# 2. Test with --plugin-dir
claude --plugin-dir ~/projects/drews-skills

# 3. Verify the skill appears
/help

# 4. Restart and test again after changes
# Exit Claude Code (Ctrl+D or /exit)
# Start again with --plugin-dir
```

## Testing Changes

### Local Testing Before Distribution

Before pushing to GitHub, test the marketplace installation flow:

```bash
# 1. Add your local marketplace
/plugin marketplace add ~/projects/drews-skills

# 2. List available plugins
/plugin marketplace

# 3. Install to test the installation flow
/plugin install exec-func-skills@drews-skills

# 4. Verify it works
/help
# Look for your skills in the list

# 5. Clean up test installation
/plugin uninstall exec-func-skills@drews-skills
/plugin marketplace remove drews-skills
```

### Verify Marketplace Structure

```bash
# Check marketplace.json is valid
cat .claude-plugin/marketplace.json | jq .

# Verify all listed skills exist
ls -la getting-started/ saving-progress/ sensing-limits/
```

## Distribution & Deployment

### Publishing to GitHub

```bash
# 1. Update version numbers in affected skills
vim my-skill/SKILL.md
# Update the version in frontmatter

# 2. Update marketplace.json
vim .claude-plugin/marketplace.json
# Update version numbers for changed skills

# 3. Commit and push
git add .
git commit -m "Update my-skill to v1.2.0"
git push origin main
```

### User Installation (from GitHub)

Users install from GitHub using:

```bash
# Add marketplace
/plugin marketplace add drews/skills

# Install specific plugin
/plugin install exec-func-skills@drews-skills

# Or browse and install interactively
/plugin marketplace
# Select "Browse and install plugins"
# Select "drews-skills"
# Select the desired skill
# Select "Install now"
```

## Version Management

### Semantic Versioning

Use semantic versioning (MAJOR.MINOR.PATCH) in skill frontmatter:

```yaml
---
name: my-skill
version: 1.2.0
description: My custom skill
---
```

**Version bumps:**
- **MAJOR** (1.0.0 → 2.0.0): Breaking changes to skill interface or behavior
- **MINOR** (1.1.0 → 1.2.0): New features, backward-compatible
- **PATCH** (1.1.1 → 1.1.2): Bug fixes, documentation updates

### Updating Marketplace Versions

Both locations must be updated for version changes:

1. **Skill frontmatter** (`my-skill/SKILL.md`):
   ```yaml
   version: 1.2.0
   ```

2. **Marketplace manifest** (`.claude-plugin/marketplace.json`):
   ```json
   {
     "plugins": [
       {
         "name": "exec-func-skills",
         "version": "1.2.0",
         ...
       }
     ]
   }
   ```

### User Updates

Updates are **manual**, not automatic:

```bash
# Users update marketplace index
/plugin marketplace update

# Users update specific plugins
/plugin update exec-func-skills

# Or update all plugins
/plugin update --all
```

## Plugin Loading Behavior

### Installation Scopes

Claude Code supports multiple installation scopes with precedence:

| Scope     | Settings File                     | Use Case                          | Precedence |
|-----------|-----------------------------------|-----------------------------------|------------|
| `local`   | `.claude/settings.local.json`     | Project-specific (gitignored)     | 1 (highest) |
| `project` | `.claude/settings.json`           | Team-shared (version-controlled)  | 2          |
| `user`    | `~/.claude/settings.json`         | Personal, all projects            | 3          |
| `managed` | `managed-settings.json`           | Enterprise (read-only)            | 4 (lowest) |

### What Happens During Installation

1. Plugin files are **copied** to Claude Code's cache directory
2. Cache location depends on installation scope
3. The cached version is what Claude Code uses
4. Your source directory is **not** referenced after installation

### Scope Precedence Example

```bash
# Scenario: Same plugin installed at different scopes
/plugin install my-skill@drews-skills --scope user     # v1.0.0
/plugin install my-skill@drews-skills --scope project  # v1.1.0
/plugin install my-skill@drews-skills --scope local    # v1.2.0

# Result: Claude Code uses v1.2.0 (local scope has highest precedence)
```

### Development vs. Production

**During Development:**
```bash
# Uses source directory directly (not cached)
claude --plugin-dir ~/projects/drews-skills
```

**In Production:**
```bash
# Uses cached copy from user scope
/plugin install exec-func-skills@drews-skills --scope user
```

**Tip:** Keep development and production separate by using `--plugin-dir` for dev work and installing to `user` scope for daily use.

## Creating New Skills

### Skill Directory Structure

```
my-new-skill/
├── SKILL.md              # Required: Instructions and frontmatter
├── scripts/              # Optional: Executable code
├── references/           # Optional: Load-on-demand docs
└── assets/               # Optional: Output files
```

### Minimal SKILL.md Template

```yaml
---
name: my-new-skill
description: Clear description of what this does and when to use it
version: 1.0.0
---

# My New Skill

Instructions for Claude Code go here.

## When to Use

Describe trigger conditions...

## How It Works

Explain the approach...
```

### Adding to Marketplace

1. **Create skill directory and SKILL.md**

2. **Update `.claude-plugin/marketplace.json`:**
   ```json
   {
     "name": "drews-skills",
     "plugins": [
       {
         "name": "my-new-skill",
         "source": "./my-new-skill",
         "description": "Brief description",
         "version": "1.0.0"
       }
     ]
   }
   ```

3. **Test locally:**
   ```bash
   claude --plugin-dir ~/projects/drews-skills
   /help  # Verify skill appears
   ```

4. **Commit and push:**
   ```bash
   git add my-new-skill/ .claude-plugin/marketplace.json
   git commit -m "Add my-new-skill v1.0.0"
   git push origin main
   ```

## Marketplace Updates

### Updating Existing Skills

1. **Modify skill files**
2. **Bump version in SKILL.md frontmatter**
3. **Update version in marketplace.json**
4. **Commit with clear message:**
   ```bash
   git commit -m "Update getting-started to v1.3.0 - Add new examples"
   ```
5. **Push to GitHub**

### User Update Flow

After you push updates:

```bash
# Users refresh marketplace index
/plugin marketplace update

# Check available updates
/plugin list

# Update specific plugin
/plugin update getting-started

# Or update all
/plugin update --all
```

### Breaking Changes

For breaking changes that might affect users:

1. **Bump MAJOR version** (e.g., 1.5.0 → 2.0.0)
2. **Document in commit message:**
   ```bash
   git commit -m "BREAKING: Update getting-started to v2.0.0 - Remove deprecated examples"
   ```
3. **Consider adding migration notes** in SKILL.md

## Best Practices

### Version Control
- Commit skill changes separately from marketplace manifest updates
- Use clear commit messages with version numbers
- Tag releases: `git tag v1.2.0 && git push --tags`

### Testing
- Always test with `--plugin-dir` before pushing
- Test the installation flow from local marketplace
- Verify skills work after cache-based installation

### Documentation
- Keep skill descriptions concise in marketplace.json
- Provide detailed usage in SKILL.md
- Include examples and trigger conditions

### Performance
- Use progressive disclosure in skills (load references on-demand)
- Keep SKILL.md focused on essentials
- Move large docs to `references/` subdirectory

### Shared Code
- Don't use relative paths like `../shared-utils` (breaks after caching)
- Include shared files within skill directory
- Or copy shared code into each skill that needs it

## Troubleshooting

### Skill Not Appearing After Install

```bash
# Check plugin is enabled
/plugin list

# Verify installation scope
cat ~/.claude/settings.json | jq '.enabledPlugins'

# Reinstall to user scope explicitly
/plugin uninstall my-skill@drews-skills
/plugin install my-skill@drews-skills --scope user
```

### Changes Not Reflected

```bash
# If using --plugin-dir: restart Claude Code
# If installed: you need to update

/plugin marketplace update     # Refresh marketplace index
/plugin update my-skill        # Pull latest version
```

### Marketplace Not Found

```bash
# Check marketplace is added
/plugin marketplace list

# Re-add if missing
/plugin marketplace add drews/skills

# Verify GitHub URL is correct
# Should be: github.com/drews/skills
```

## Resources

- [Agent Skills Spec](./agent_skills_spec.md) - Complete specification
- [Claude Code Plugins Documentation](https://code.claude.com/docs/en/plugins)
- [Plugin Marketplaces Guide](https://code.claude.com/docs/en/plugin-marketplaces.md)
- [OpenSpec Workflow](./openspec/AGENTS.md) - Spec-driven development

## License

Individual skills may have their own licenses. Check each skill directory.
