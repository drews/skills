---
name: canvas-cli
description: Safe, correct use of the `canvas` CLI (canvas-cli) for Canvas LMS from an agent session. Use when reading courses, assignments, modules, enrollments, outcomes, or grades via `canvas`, when setting up agent guardrails, or when handling student data from Canvas.
---

# Using the Canvas CLI

`canvas` wraps the Canvas LMS API. Discover commands with `canvas --help` and `canvas <cmd> --help`; this skill covers the safety rules and the quirks `--help` won't tell you.

## Safety first

- **Read-only by default.** Never create, update, grade, publish, or delete without the teacher's explicit, per-action approval. Approval for one change doesn't cover the next.
- **Install the guard.** `canvas agent guard --host claude-code` prints permission rules and a hook that hard-block irreversible verbs (delete, remove, conclude, reset, unpublish, ...) and gate unknown mutating verbs. Review the output, then, only with the teacher's approval, add `--write` to install it (it edits settings and hooks); add `--all-writes` to block every write. Regenerate after upgrading `canvas`. See "Permission deny patterns" below for a minimal fallback.
- **`canvas api` is the escape hatch.** It can send any HTTP verb. The guard blocks `canvas api DELETE|PUT|POST|PATCH` patterns but can't enumerate every path. Use `canvas api get` freely; for mutations, print the command for the teacher to run themselves.
- **When blocked, stop.** Don't route around the guard (wrapper scripts, heredocs containing blocked commands). Show the command and let the human run it.
- Use `--dry-run` to print the curl instead of executing it.

## Student data

Canvas responses (enrollments, submissions, grades) are education records (FERPA).

- Don't print student names or ids into logs, commits, or chat unless needed; prefer counts and aggregates.
- Responses are cached in `~/.canvas-cli/cache/`. After reading student records, ask the teacher to run `canvas cache clear --all` (it deletes the shared cache, and the guard may gate it).
- The token lives under `~/.canvas-cli/`. Never print or commit it; leave `--show-token` off.

## Everyday patterns

- `-o json` for anything you'll parse; `--quiet` to drop informational noise.
- `--limit N`, `--filter TEXT`, `--columns a,b`, `--sort -field` to trim output.
- `--no-cache` when you need a live read; cached answers can be stale.
- `canvas context` stores a working course/assignment/user id so you can omit repeated flags.

## Gotchas

- **PATH.** `canvas` often installs to `~/go/bin`, which cron/systemd units don't have. A scheduled job fails with `canvas: command not found`; set PATH explicitly.
- **`canvas api get` wraps results in `.body`;** dedicated commands return a bare array. Unwrap accordingly.
- **`assignments list --bucket overdue|ungraded|unsubmitted` is scoped to the calling user's submissions.** As a teacher it returns nothing useful. Compute past-due from due dates instead.
- **`analytics students` is a lagging batch rollup** (participations can read 0 days after work was due). For live activity read `/api/v1/courses/:id/enrollments` (`last_activity_at`, `total_activity_time`).
- **The `missing` flag is only set when the course has a late policy.** Without one it's always false; derive it from the due date and submission state.
- **Module item titles are not assignment names,** and items get renamed and reordered. Join on the content id, not title or position.
- **Outcomes:**
  - `outcomes list --group-id` returns only id, title, url. Fetch details with `outcomes get ID` (it rejects `--course-id`).
  - A GET may omit `mastery_points` / `calculation_*`; mastery scales are often managed at the account level and `--mastery-points` on create can be silently ignored. Verify in the Canvas UI.
  - `outcomes alignments --course-id` can return 400 (the endpoint wants a student id). Use `outcomes results --include alignments` once something is graded.

## Permission deny patterns

A plugin can't ship `permissions.deny` rules (plugins carry skills, hooks, agents and MCP config, not permission settings), so these go in the user's or project's `.claude/settings.json`. The generated guard is the complete version (a per-command list tied to your `canvas` build); this is a minimal, version-independent floor:

```json
{
  "permissions": {
    "deny": [
      "Bash(canvas api DELETE:*)",
      "Bash(canvas api PUT:*)",
      "Bash(canvas api POST:*)",
      "Bash(canvas api PATCH:*)",
      "Bash(canvas * delete:*)",
      "Bash(canvas * remove:*)",
      "Bash(canvas * conclude:*)",
      "Bash(canvas * reset:*)",
      "Bash(canvas * unpublish:*)"
    ]
  }
}
```

Deny rules are prefix/glob matches on the command text and don't see through variable indirection or aliases; the hook from `canvas agent guard` is the stronger layer.
