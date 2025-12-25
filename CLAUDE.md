<!-- OPENSPEC:START -->
# OpenSpec Instructions

These instructions are for AI assistants working in this project.

Always open `@/openspec/AGENTS.md` when the request:
- Mentions planning or proposals (words like proposal, spec, change, plan)
- Introduces new capabilities, breaking changes, architecture shifts, or big performance/security work
- Sounds ambiguous and you need the authoritative spec before coding

Use `@/openspec/AGENTS.md` to learn:
- How to create and apply change proposals
- Spec format and conventions
- Project structure and guidelines

Keep this managed block so 'openspec update' can refresh the instructions.

<!-- OPENSPEC:END -->

## Platform Awareness

This project is developed across Windows, macOS, and Linux. **Always check the platform indicator** in your session environment before making assumptions.

**Platform-specific considerations:**
- **Commands**: Use Git Bash syntax on Windows (`ls`, `cat`, `test`), avoid CMD/PowerShell syntax
- **Paths**: Prefer forward slashes (works everywhere), quote paths with spaces
- **Shell**: Git Bash on Windows, Bash/Zsh on Unix-like systems

**When encountering platform-related issues**, use the `operating-systems` skill for detailed cross-platform guidance and command equivalents.

See `operating-systems/SKILL.md` for comprehensive platform-aware development patterns.