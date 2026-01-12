---
name: tracking-issues
description: Issue tracking workflows using beads (bd) for persistent, dependency-aware task management across agent sessions. Use when starting work sessions, planning tasks, managing dependencies, or ending sessions to ensure work persists.
---

# Tracking Issues with Beads

Provides structured workflows for the `bd` CLI tool—a git-backed issue tracker designed for AI coding agents.

## When to Use This Skill

- **Starting a session**: Find ready work and claim it
- **During work**: Create discovered issues, manage dependencies
- **Planning**: Decompose features into trackable tasks
- **Ending a session**: The critical "landing the plane" protocol

## Why This Matters

AI agents have no persistent memory between sessions. Each session lasts ~10-20 minutes before context degrades. Beads solves this by providing:
- Dependency-aware task graphs (not flat TODO lists)
- Git-native storage that travels with the repo
- Hash-based IDs that prevent merge conflicts
- Automatic sync through git push/pull

---

## Core Workflows

### 1. Finding Ready Work

Start each session by finding unblocked tasks:

```bash
bd ready
```

This shows tasks with no open blockers, sorted by priority (P0 → P4).

**Before starting work, claim it:**
```bash
bd update <id> --status in_progress
```

This prevents multi-agent conflicts when multiple Claude instances work on a project.

### 2. Issue Lifecycle

**Create issues with meaningful context:**
```bash
bd create "Implement OAuth flow for Google provider" -p 1 --type feature
```

**Track state transitions:**
- `open` → `in_progress` → `closed`

**Always close with a reason** (future agents need this context):
```bash
bd close <id> --reason "OAuth implemented with Google, GitHub providers. Tests passing."
```

**Discover work during implementation:**
When you find additional tasks while working, create them immediately:
```bash
bd create "Add rate limiting to OAuth endpoints" -p 2 --type task
bd dep add <new-id> <current-id>  # If it's a follow-up
```

### 3. Dependency Management

Build proper task graphs, not flat lists:

```bash
# Hard dependency (child blocked until parent closes)
bd dep add <child> <parent>

# View what's blocking a task
bd show <id>
```

**Hierarchical IDs for epics** (prefix varies by project, e.g., `myproject-a3f8`):
- `<prefix>-a3f8` — Epic level
- `<prefix>-a3f8.1` — Task level
- `<prefix>-a3f8.1.1` — Sub-task level

**Types of relationships:**
- `blocks` — Hard dependency, prevents progress
- `related` — Soft reference, informational
- `parent-child` — Hierarchical grouping

### 4. Landing the Plane (Session End)

This protocol is **non-negotiable**. The session isn't complete until `git push` succeeds.

```bash
# 1. File any remaining work discovered during session
bd create "Remaining task description" -p 2

# 2. Close completed issues
bd close <id> --reason "Description of what was accomplished"

# 3. Force sync (bypasses 30-second debounce)
bd sync

# 4. Push to remote - THIS IS MANDATORY
git pull --rebase
git push

# 5. Verify clean state
git status  # Should show "up to date with origin"
```

**Critical rules:**
- Never say "ready to push when you are!"—YOU must push
- Never stop before `git push` completes
- Unpushed work breaks multi-agent coordination

### 5. Planning & Decomposition

**For features spanning multiple sessions:**

1. Create an epic:
   ```bash
   bd create "User authentication system" -p 1 --type epic
   ```

2. Decompose into session-sized tasks (~10-20 min each):
   ```bash
   bd create "Set up auth database schema" -p 1 --type task
   bd create "Implement login endpoint" -p 1 --type task
   bd create "Add session management" -p 1 --type task
   ```

3. Add dependencies:
   ```bash
   bd dep add <login-id> <schema-id>
   bd dep add <session-id> <login-id>
   ```

**Priority levels:**
- P0: Critical, blocking other work
- P1: High priority, do soon
- P2: Medium priority, normal work
- P3: Low priority, when time permits
- P4: Backlog, someday/maybe

**Issue types:**
- `epic` — Large feature, spans sessions
- `feature` — User-facing functionality
- `task` — Implementation work
- `bug` — Defect to fix
- `chore` — Maintenance, refactoring

---

## Daily Maintenance

```bash
# Run daily on active repos
bd doctor

# Run every few days
bd cleanup
```

**Install git hooks for automatic consistency:**
```bash
bd hooks install
```

This adds pre-commit, post-merge, pre-push, and post-checkout hooks.

---

## Multi-Agent Coordination

When multiple agents work on a project:

1. **Claim before working**: `bd update <id> --status in_progress`
2. **Check assignments**: `bd list --assignee <name>`
3. **Always push**: Git-based sync means changes are visible after push
4. **Hash IDs prevent conflicts**: Even concurrent creates on different branches merge cleanly

---

## Protected Branch Workflow

For repos where you can't commit directly to main:

```bash
bd init --branch beads-sync   # Use separate sync branch
bd daemon --start --auto-commit
```

---

## Anti-Patterns to Avoid

- Starting work without claiming (causes conflicts)
- Closing issues without a reason (loses context)
- Ignoring priority order (P0 before P4)
- Working on blocked tasks
- Stopping before `git push`
- Creating flat lists instead of dependency graphs
- Forgetting to create discovered issues

---

## Integration with Other Skills

- **getting-started**: Use after `bd ready` identifies tasks but you feel stuck
- **saving-progress**: The beads issue becomes your checkpoint; close with detailed reason
- **sensing-limits**: Match task priority to current energy level

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `bd ready` | Show unblocked tasks by priority |
| `bd create "Title" -p N` | Create priority-N issue |
| `bd update <id> --status in_progress` | Claim work |
| `bd close <id> --reason "..."` | Complete with context |
| `bd dep add <child> <parent>` | Add dependency |
| `bd show <id>` | View issue details |
| `bd sync` | Force immediate sync |
| `bd doctor` | Health check |

---

## Resources

- [Beads GitHub](https://github.com/steveyegge/beads)
- [Agent Instructions](https://github.com/steveyegge/beads/blob/main/AGENT_INSTRUCTIONS.md)
- [Best Practices](https://steve-yegge.medium.com/beads-best-practices-2db636b9760c)
- [FAQ](https://github.com/steveyegge/beads/blob/main/docs/FAQ.md)
