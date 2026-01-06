# Tools as Skills

This document outlines how external CLI tools can be encapsulated as Claude Code skills, providing structured knowledge for effective usage patterns rather than just command references.

## Philosophy

CLI tools often have steep learning curves and context-dependent best practices. Skills bridge this gap by:
- Teaching *when* to use commands, not just *how*
- Encoding workflow patterns that take time to discover
- Preventing common anti-patterns and mistakes
- Integrating with related skills for cohesive workflows

---

## Implemented Tool Skills

### Beads (bd) → `tracking-issues`

**Tool**: [steveyegge/beads](https://github.com/steveyegge/beads)
**Skill**: [`tracking-issues`](../tracking-issues/SKILL.md)

Git-backed issue tracking designed for AI coding agents. Provides persistent, dependency-aware task management across sessions.

**Key workflows covered:**
- Ready-work discovery and claiming
- Issue lifecycle (create → claim → close with reason)
- Dependency graph management
- Session completion ("landing the plane" protocol)
- Planning and decomposition

**Why it matters:** AI agents have no memory between sessions. Beads provides the persistent task graph that lets work continue across session boundaries.

---

## Future Tools to Consider

Placeholder for additional tool-based skills:
- **gh** (GitHub CLI) - PR workflows, issue management
- **git worktrees** - Parallel branch development
- **tmux** - Session management for multi-agent orchestration
