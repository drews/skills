---
name: keeping-place
description: Saves and restores work context to handle interruptions and task switching. Use when user says "save where I am", "need to context switch", "got interrupted", "what was I doing", "where was I", "lost my place", or needs to capture current work state, resume after interruption, or transition between tasks cleanly.
---

# Keeping Place

Manages work context through saves, restores, and recovery from interruptions. Acts as external working memory for maintaining state across task switches and disruptions.

## When to Use This Skill

Use this skill when detecting:
- **Save requests**: "save where I am", "checkpoint this", "need to switch tasks"
- **Recovery needs**: "what was I doing", "where was I", "got interrupted", "lost my train of thought"
- **Transition language**: "switching to", "coming back to", "need to pause this"
- **Memory externalization**: "help me remember", "keep track of", "don't let me forget"

## Core Approach

### 1. Capture Current State Quickly
The faster and lower-friction the capture, the more likely it gets used. Minimize questions—get essentials now, details later.

### 2. Make Context Self-Explanatory
Future-you (or interrupted-you) shouldn't need to reconstruct meaning. Capture not just what, but enough why and where.

### 3. Track Open Loops
Human working memory fails with unfinished business. Explicitly note pending decisions, unanswered questions, things-to-remember.

### 4. Enable Clean Transitions
Good context handoff means you can fully engage with the new task without background worry about the old one.

## Context Save Templates

### Quick Save (30 seconds)
Minimum viable context capture:
```
## [Task Name] - Checkpoint [timestamp]

Current focus: [one sentence - what you're working on right now]
Next action: [the very next thing to do when resuming]
Open loops: [any pending decisions or questions]
```

### Standard Save (2 minutes)
More comprehensive:
```
## [Task Name] - Context Save [timestamp]

### Current State
- Working on: [specific component/section/problem]
- Progress so far: [what's done]
- Current thought: [where your head was at]

### Next Steps
1. [immediate next action]
2. [what comes after that]

### Open Loops
- Decisions needed: [choices to make]
- Questions: [things you were wondering about]
- To remember: [important context for later]

### Files/Resources
- Currently open: [relevant files]
- References: [docs/links you had up]
```

### Interrupt Recovery Save
When interrupted mid-flow:
```
## [Task] - INTERRUPTED [timestamp]

Before interruption, I was:
- Doing: [exact action]
- About to: [next micro-step]
- Thinking: [current train of thought]

Resume cue: [one sentence to trigger memory]
Open tabs/context: [what was on screen]
```

## Context Restore Protocols

### "Where Was I?" Recovery
When user returns but doesn't remember state:

1. **Find the checkpoint** - Locate most recent save
2. **Read "Resume cue" first** - Often triggers full context
3. **Orient** - "You were [situation], working on [specific thing]"
4. **Immediate action** - "Your next step was: [action]"
5. **Context load** - Share open loops and progress

### Smooth Resumption Pattern
```
Welcome back to [task].

You were: [working on X, had just completed Y]
Your next step was: [specific action]

Still relevant:
- [open loop 1]
- [open loop 2]

Would you like to continue from there, or has the situation changed?
```

## Working Memory Externalization

### Open Loops Tracker
Maintain a running list of:
- **Decisions pending**: "Still need to choose between A and B"
- **Questions unanswered**: "Waiting to hear about X"
- **Things to remember**: "Don't forget constraint Y applies"
- **Half-formed thoughts**: "Was wondering if Z approach might work"

Update this continuously during work, not just at save points.

### Multi-Task Context Map
When juggling multiple things:
```
Active Context Map:

[Task A] - Status: [active/paused/blocked]
- Last action: [what]
- Next action: [what]
- Blocking on: [if applicable]

[Task B] - Status: [active/paused/blocked]
- Last action: [what]
- Next action: [what]
- Dependencies: [if applicable]
```

## Clean Transitions

### Closing the Loop Before Switching
```
Before switching from [Current Task] to [New Task]:

1. Current task state: [complete/paused/blocked]
2. Next time you work on this: [start here]
3. Open questions to capture: [list]
4. Clean break?: [anything unresolved that might nag you?]

Good to switch? [confirm nothing will leak into next task]
```

### Handoff Template
When passing work to future-self or others:
```
## Context Handoff: [Task]

### What Was Accomplished
- [completed item 1]
- [completed item 2]

### Current State
[where things stand now]

### What's Next
[what should happen next and why]

### Important Context
[non-obvious things someone needs to know]

### Gotchas/Warnings
[things that might trip someone up]
```

## Interrupt Recovery Techniques

### The "Before I Was Interrupted" Prompt
Immediately after interruption (even brief):
- "Before I was interrupted, I was [doing X]"
- "I was about to [next micro-action]"
- "I was thinking [thought process]"

Captures volatile working memory before it decays.

### Reconstruct From Artifacts
When no checkpoint exists:
- Last file saved: [what was the last edit about?]
- Open windows/tabs: [what was in context?]
- Recent searches: [what were you looking for?]
- Terminal history: [what commands were running?]

Piece together context from digital breadcrumbs.

### The 60-Second Refresh
Quick context reload protocol:
1. Skim last checkpoint (10s)
2. Check open loops (10s)
3. Identify next action (10s)
4. Load relevant file/resource (15s)
5. Start doing next action (15s)

Gets you back in motion fast.

## File-Based Context Management

### Suggested Storage Pattern
```
.context/
  current.md           # Active work context
  checkpoints/         # Timestamped saves
    2025-11-06-1430.md
    2025-11-06-1520.md
  open-loops.md        # Running list of pending items
  task-map.md          # Multi-task overview
```

Or use whatever system the user prefers—the pattern matters more than the location.

## Anti-Patterns to Avoid

❌ Don't make context saves so detailed they're burdensome (friction kills usage)
❌ Don't skip the "why" (future-you won't remember the reasoning)
❌ Don't leave open loops implicit (they'll nag subconsciously)
❌ Don't assume memory is reliable (it's not, especially after interruptions)

## Success Indicators

This skill is working when:
- User can resume work quickly after interruptions
- User reports less anxiety about task switching
- User actually uses the checkpoints (low friction)
- "What was I doing?" becomes less frequent

## Relationship to Other Skills

- **task-initiator**: Can help resume momentum after context restore
- **energy-aware-planning**: Consider capacity when planning resume timing
- May store context in files that user can reference later
