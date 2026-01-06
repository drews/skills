# Tools as Skills

This document outlines how external CLI tools can be encapsulated as Claude Code skills, providing structured knowledge for effective usage patterns rather than just command references.

## Philosophy

CLI tools often have steep learning curves and context-dependent best practices. Skills bridge this gap by:
- Teaching *when* to use commands, not just *how*
- Encoding workflow patterns that take time to discover
- Preventing common anti-patterns and mistakes
- Integrating with related skills for cohesive workflows

---

## Beads (bd)

**Source**: [steveyegge/beads](https://github.com/steveyegge/beads)
**Purpose**: Git-backed issue tracking designed for AI coding agents

### Why Beads Matters

AI agents have no persistent memory between sessions—each session lasts ~10-20 minutes before context degrades. Beads solves this by providing:
- Dependency-aware task graphs (not flat TODO lists)
- Git-native storage (`.beads/issues.jsonl`) that travels with the repo
- Hash-based IDs that prevent merge conflicts in multi-agent workflows
- Automatic sync through git push/pull

### Planned Skill Coverage

#### 1. Ready-Work Discovery (`beads-ready`)
**Intent**: Help agents find the right work to start.

Key patterns:
- `bd ready` shows tasks with no open blockers, sorted by priority
- Claim work with `bd update <id> --status in_progress` before starting
- Avoid picking blocked tasks—let the dependency graph guide selection

Anti-patterns to prevent:
- Starting work without claiming (causes multi-agent conflicts)
- Ignoring priority ordering (P0 before P4)
- Working on blocked tasks that can't complete

#### 2. Issue Lifecycle (`beads-lifecycle`)
**Intent**: Proper issue state management through the full workflow.

Key patterns:
- Create with meaningful titles: `bd create "Implement OAuth flow" -p 1`
- Track state: pending → in_progress → closed
- Always close with a reason: `bd close <id> --reason "OAuth implemented with Google, GitHub providers"`
- Discover work during implementation: create new issues for found tasks

Anti-patterns to prevent:
- Closing without a reason (loses context for future agents)
- Forgetting to create discovered issues (work falls through cracks)
- Leaving stale "in_progress" issues

#### 3. Dependency Management (`beads-deps`)
**Intent**: Build proper task graphs, not flat lists.

Key patterns:
- `bd dep add <child> <parent>` for blocking relationships
- Hierarchical IDs for epics: `bd-a3f8` → `bd-a3f8.1` → `bd-a3f8.1.1`
- Use `related` for soft dependencies, `blocks` for hard dependencies

Anti-patterns to prevent:
- Creating isolated issues without dependencies
- Circular dependencies
- Overly deep hierarchies (3 levels usually sufficient)

#### 4. Session Completion (`beads-landing`)
**Intent**: The critical "landing the plane" protocol—ensuring work persists.

Key patterns:
```bash
# Non-negotiable sequence at session end:
bd sync                 # Force immediate export/import/commit
git pull --rebase       # Get latest
git push               # THE PLANE ISN'T LANDED UNTIL THIS SUCCEEDS
git status             # Verify "up to date with origin"
```

Anti-patterns to prevent:
- Saying "ready to push when you are!" (agent must push)
- Stopping before `git push` (strands work locally)
- Skipping `bd sync` (changes may be in debounce window)

#### 5. Planning & Decomposition (`beads-planning`)
**Intent**: Break down work into trackable issues with proper structure.

Key patterns:
- Start with an epic for features spanning multiple sessions
- Decompose into tasks that fit a single agent session (~10-20 min)
- Use priorities: P0 (critical), P1 (high), P2 (medium), P3 (low), P4 (backlog)
- Use types: bug, feature, task, epic, chore

Integration with exec-func-skills:
- Pairs with `getting-started` for task initiation
- Pairs with `saving-progress` for context handoff between sessions

### Daily Maintenance Habits

From beads best practices:
- Run `bd doctor` daily on active repos
- Run `bd cleanup` every few days
- Install git hooks: `bd hooks install`

### Multi-Agent Coordination

When multiple agents work on a project:
- Each agent claims work before starting
- Use `bd list --assignee <name>` to see who's doing what
- Git-based sync means independent operation with merge semantics
- Hash-based IDs prevent collision even on different branches

### Protected Branch Workflow

For repos with protected main branches:
```bash
bd init --branch beads-sync   # Use separate sync branch
bd daemon --start --auto-commit
```

### Resources

- [Beads README](https://github.com/steveyegge/beads)
- [Agent Instructions](https://github.com/steveyegge/beads/blob/main/AGENT_INSTRUCTIONS.md)
- [Best Practices](https://steve-yegge.medium.com/beads-best-practices-2db636b9760c)
- [Plugin Documentation](https://github.com/steveyegge/beads/blob/main/docs/PLUGIN.md)
- [FAQ](https://github.com/steveyegge/beads/blob/main/docs/FAQ.md)

---

## Future Tools to Consider

Placeholder for additional tool-based skills:
- **gh** (GitHub CLI) - PR workflows, issue management
- **git worktrees** - Parallel branch development
- **tmux** - Session management for multi-agent orchestration
