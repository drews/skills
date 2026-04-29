---
name: tuning-affordances
description: Safely adjust personal computing affordances as self-accommodations that reduce executive function load. Use when user wants to tweak configs, rebind keys, add aliases, reduce friction in their setup, or says things like "too many steps", "I keep forgetting where X is", "make this easier to do". Also covers iterating on previous changes and physical workspace adjustments.
---

# Tuning Affordances

Environment friction compounds executive dysfunction. Every extra step, hidden shortcut, or misplaced tool is a tax on initiation, working memory, and sustained attention. This skill treats the computing environment as a living set of affordances — the actions the system makes easy, hard, or invisible — and helps adjust them safely and iteratively as self-accommodations. The computer is not a fixed tool but a surface that shapes, and is shaped by, the user's attention and habits.

## When to Use This Skill

### Trigger Categories
- **Config edits**: dotfiles, `.zshrc`, `.config`, `settings.json`, editor/shell/WM config
- **Personalization intent**: "rebind", "alias for", "shortcut for", "make it easier to", "I always have to"
- **Environment framing**: "my setup", "my workspace", "my environment", "my rig"
- **Iteration language**: "tweak", "tune", "adjust", "refine", "try", "experiment with"
- **Friction reports**: "this keeps annoying me", "I do X too often", "I wish my computer would", "too many steps"
- **Accommodation needs**: "I keep forgetting where X is", "I need this to be more obvious", "reduce the friction", "make the right thing the easy thing"
- **Physical workspace**: desk layout, peripherals, lighting, posture affordances that interact with digital ones

### Disambiguating Triggers
Some triggers overlap with other tasks. Use this skill when the intent is *reshaping what the environment affords*, not fixing a one-time problem:
- "My shell is slow" → if about startup time/config bloat, use this skill. If about a hung process, that's debugging.
- "Change this keybinding" → if in the user's personal editor/WM config, use this skill. If in a project's source code, that's a code change.
- "I need to set up X" → if adding a permanent personal affordance, use this skill. If bootstrapping a fresh machine from zero, that's provisioning.
- "This is annoying" → if about recurring friction in the user's environment, use this skill. If about a bug in project code, that's a fix.

## Core Principles

### 1. Affordances, Not Configs
The unit of change is not "a line in a file" but "an action the environment makes easier or harder." Always name the affordance before touching the config. Example: not "add alias gco" but "make switching git branches a two-keystroke move."

### 2. Reversibility is a Feature
Every change should be easy to undo. Prefer: version-controlled configs, additive edits over rewrites, feature flags / conditional blocks, and staged rollout (try in a scratch shell before sourcing globally). If a change can't be rolled back in under a minute, flag that cost explicitly.

### 3. Iterate in the Small
Tuning is a loop, not a project. Favor many small reversible experiments over one big refactor. The goal is to learn what the user actually wants from their environment, which is often only visible after living with a change for a few days.

### 4. Two-Way Loop
The environment shapes the user and the user shapes the environment. A new keybinding changes what becomes habitual; a new habit reveals what the next keybinding should be. Treat friction reports as signal about the user's evolving workflow, not just bugs to patch.

### 5. Environment as External Scaffolding
A well-tuned environment is prosthetic executive function. Visible cues replace recall. Short paths replace initiation cost. Consistent layouts replace spatial working memory. Every affordance that removes a decision or a step is one fewer thing the executive system has to manage.

### 6. Physical and Digital are the Same Surface
A desk layout change and a window manager rebind are the same kind of move: both adjust what's in arm's reach. Don't artificially scope to just software.

## Workflow

### Step 1: Name the Affordance
Before touching anything, get explicit about what action should become easier, harder, or differently-shaped. Write it as a sentence: "I want [action] to take [effort/steps], instead of [current effort/steps]."

### Step 2: Locate the Surface
Identify where the change lives. Common surfaces:
- **Shell**: aliases, functions, prompt, completion, history
- **Editor**: keybindings, snippets, LSP, formatters, plugins
- **Window/session**: WM shortcuts, tmux, workspace layouts, launcher
- **OS**: system keybindings, accessibility settings, input method
- **Tooling**: git config, CLI tool configs, scripts on PATH
- **AI assistants**: CLAUDE.md, skills, hooks, slash commands
- **Physical**: desk, peripherals, lighting, what's visible at rest

### Step 3: Stage the Change
Prefer staged application over global commit:
- New shell function → define in current session first
- New keybinding → try in a scratch config or conditional block
- New script → put in `~/bin/experimental/` before promoting
- Config file edit → keep the old value in a comment for one iteration

### Step 4: Live With It
The honest evaluation of an affordance change takes hours to days, not seconds. When possible, explicitly mark the change as "trial" and check back before committing.

### Step 5: Promote or Revert
After the trial, either promote (move to canonical config, remove the comment, document if non-obvious) or revert cleanly. Avoid the middle state where half-finished experiments accumulate.

## Safety Checks

Before any change that touches shared or fragile surfaces, confirm:
- **Version control**: Is the config file tracked? If not, snapshot before editing.
- **Blast radius**: Does this affect only the current user / shell / editor, or system-wide?
- **Secrets**: Does the file hold anything that shouldn't be committed?
- **Idempotency**: If the config is sourced twice, does the change double-apply?
- **Recovery path**: If this breaks login / editor startup / WM, how does the user get back to a working state?

For changes to login shells, display managers, or anything that runs at boot: always keep a known-good fallback (backup file, alternative shell, recovery shortcut) before applying.

## Anti-Patterns

❌ Do NOT rewrite a whole config file when a three-line edit would do
❌ Do NOT add layers of abstraction ("a framework for managing my dotfiles") before the underlying edits are stable
❌ Do NOT treat every friction as needing a config fix — sometimes the answer is a habit change, not a knob
❌ Do NOT commit experiments to canonical config before living with them
❌ Do NOT build cross-machine portability before knowing what the local affordance should even be
❌ Do NOT confuse "more configured" with "better tuned" — subtractive changes count

## Integration With Other Skills

**With sensing-limits**: Low-capacity states are a bad time for irreversible config surgery. If the user is depleted, prefer the smallest possible tweak or defer the change entirely.

**With getting-started**: When stuck on a task because the environment is in the way, a small affordance tweak can be the "2-minute starter" that unblocks everything else.

**With saving-progress**: Experimental config changes are in-progress state — capture what was tried, what's on trial, and what to revisit.

## Success Indicators

This skill is working when:
- Friction the user mentioned is gone and the fix is traceable.
- The environment evolves without accumulating half-finished experiments.
- Reverting a bad change is cheap and never avoided out of sunk-cost.
- The user can articulate *why* a given affordance is shaped the way it is.
- Changes compound: earlier tuning makes later tuning easier, not harder.
