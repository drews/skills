---
name: context-assistant
description: Filter and suggest tasks based on current context (location, time available, energy level, tools available). Use when user asks "what can I work on now?" or specifies constraints like "I'm at coffee shop" or "I have 30 minutes".
---

# Context Assistant

## Purpose
Help users find the right tasks for their current situation by filtering based on context.

## Prerequisites
- Tasks MCP server (to access task list with context tags)

## When to Use
- User asks "what can I work on now?"
- User specifies location: "I'm at {place}"
- User specifies time: "I have {duration} available"
- User specifies energy: "I'm tired" or "I'm feeling focused"
- User specifies tools: "I only have my phone"
- User wants context-appropriate suggestions

## Instructions

### Step 1: Identify Current Context

**Ask user (or infer) these context dimensions:**

**Location:**
- `home` - Working from home
- `office` - At the office
- `remote` - Coffee shop, coworking, etc.
- `commute` - On train/bus
- `anywhere` - Location-independent

**Time Available:**
- `<15min` - Quick task only
- `15-30min` - Short task
- `30-60min` - Medium task
- `1-2hr` - Focus block
- `2hr+` - Deep work session

**Energy Level:**
- `high` - Focused, creative, high mental capacity
- `medium` - Normal working energy
- `low` - Tired, can handle routine work only

**Tools Available:**
- `laptop` - Full development environment
- `phone-only` - Mobile device only
- `offline` - No internet connection
- `limited` - Basic tools only

### Step 2: Fetch All Available Tasks

Use Tasks MCP:
- Status: incomplete
- Include: title, tags, estimated_duration, priority, deadline
- Include: context tags (if tasks are tagged with requirements)

### Step 3: Filter Tasks by Context

Apply filters based on context:

**Location Filter:**
```
If context = "remote" (coffee shop):
  Exclude tasks tagged: requires-office, sensitive-data

If context = "office":
  Include all tasks, prioritize collaborative work

If context = "commute":
  Only show tasks tagged: reading, mobile-friendly, offline-ok
```

**Time Filter:**
```
If time_available = "30min":
  Only show tasks where estimated_duration ≤ 30min

Fuzzy matching OK:
  If user has 30min, include 25min tasks (not 45min)
  Add 10% buffer: 30min available → show up to 27min tasks
```

**Energy Filter:**
```
If energy = "low":
  Exclude tasks tagged: requires-focus, creative, complex
  Include tasks tagged: administrative, routine, review

If energy = "high":
  Prioritize tasks tagged: deep-work, creative, strategic, complex
```

**Tools Filter:**
```
If tools = "phone-only":
  Exclude tasks tagged: requires-laptop, requires-IDE
  Include tasks tagged: reading, email, calls, review

If tools = "offline":
  Exclude tasks requiring internet
```

### Step 4: Rank Filtered Tasks

After filtering, apply priority scoring (use priority-assistant algorithm):
- Deadline urgency
- User priority
- Dependencies
- Quick wins

Show top 5 context-appropriate tasks.

### Step 5: Present Suggestions

Format output to emphasize context match:

```
Based on your context ({context_summary}), here's what you can work on:

✅ Perfect fits (match all your constraints):

1. [P2] Review design mockups (20min)
   Why: Short task, no tools needed, good for {context}

2. [P1] Respond to client emails (15min)
   Why: Can do on phone, high priority

⚠️ Possible (with caveats):

3. [P1] Write API documentation (45min)
   Note: Will need more time than you have (45min vs 30min available)
   Could you do partial work now?

---

Recommendation: Start with "Review design mockups" - perfect match for your situation.
```

## Context Patterns

### Common Contexts

**"I'm at a coffee shop":**
```
Filter:
  ✓ Location: remote, anywhere
  ✗ Exclude: requires-office, sensitive-data, internal-systems
  ✓ Tools: laptop (usually), internet (public wifi)
  ✓ Time: usually 1-2 hours

Good tasks:
  - Writing (blog posts, docs)
  - Learning (courses, reading)
  - Design work (mockups, wireframes)
  - Planning (project planning, brainstorming)

Bad tasks:
  - Accessing internal systems (security)
  - Customer data work (privacy)
  - Meetings requiring quiet (cafe noise)
```

**"I have 15 minutes before my meeting":**
```
Filter:
  ✓ Time: <15min
  ✓ Quick wins highly valued
  ✓ Can be interrupted (meeting is hard stop)

Good tasks:
  - Respond to an email
  - Review a short PR
  - Update a Jira ticket
  - Read a short article
  - Quick admin tasks

Bad tasks:
  - Anything requiring deep focus
  - Tasks you can't pause midway
```

**"I'm tired, what can I do?":**
```
Filter:
  ✓ Energy: low
  ✗ Exclude: creative, complex, focus-required
  ✓ Include: routine, administrative, review

Good tasks:
  - Organize files
  - Clean up email inbox
  - Review PRs (low-stakes)
  - Update documentation
  - Administrative tasks (expense reports, etc.)

Bad tasks:
  - Writing code
  - Strategic planning
  - Complex problem-solving
```

**"I'm on the train" (commute):**
```
Filter:
  ✓ Tools: phone-only
  ✓ Offline-friendly (unreliable connection)
  ✓ Noise/interruptions expected
  ✓ Time: 20-40 min typically

Good tasks:
  - Reading (articles, docs, books)
  - Listening (podcasts, audiobooks)
  - Planning (using note-taking app)
  - Email triage (read/archive/flag)
  - Review notes from meetings

Bad tasks:
  - Video calls
  - Tasks requiring concentration
  - Coding (small screen, shaky)
```

## Advanced: Context Learning

Over time, learn user's context patterns:

**Pattern Recognition:**
```
If user frequently works from coffee shop on Wednesday mornings:
  - Pre-filter tasks tagged "good-for-remote" on Wed AM
  - Suggest batching coffee shop work

If user has low energy after 3pm consistently:
  - Automatically suggest low-energy tasks after 3pm
  - Save high-focus work for mornings
```

**Smart Suggestions:**
```
"I notice you're at the coffee shop (based on your calendar location).
Based on past patterns, you usually work on writing tasks here.

Current writing tasks:
1. Blog post draft (2hr)
2. Documentation update (1hr)
3. Project proposal (1.5hr)

Would you like to work on any of these?"
```

## Integration with Other Skills

**Combine with priority-assistant:**
```
User: "I have 30 minutes, what's most important?"

Steps:
1. context-assistant: Filter to tasks ≤30min
2. priority-assistant: Rank filtered tasks by priority
3. Result: Top-priority task that fits current context
```

**Combine with task-scheduler:**
```
User: "Schedule my deep work for this week"

Steps:
1. context-assistant: Identify tasks needing high energy/focus
2. task-scheduler: Find morning time blocks (best for focus)
3. Result: Deep work scheduled in optimal time slots
```

## Examples

### Example 1: Quick Win Hunting
```
User: "I have 20 minutes before lunch, what should I do?"

Context:
- Time: 20min
- Location: office (inferred)
- Energy: medium (mid-morning)

Output:
"Quick tasks you can finish before lunch:

1. [P1] Review PR #234 (15min) - Blocks deployment
2. [P2] Respond to client email (10min) - Due today
3. [P3] Update Jira tickets (15min) - Clean up backlog

I recommend PR #234 - high priority and you'll finish with time to spare."
```

### Example 2: Mobile-Only Work
```
User: "I only have my phone right now, what can I work on?"

Context:
- Tools: phone-only
- Location: anywhere
- Time: not specified

Output:
"Tasks you can do on your phone:

1. [P1] Approve expense reports (5min each, 3 pending)
2. [P2] Review and comment on design mockups (20min)
3. [P2] Read competitor analysis doc (15min)
4. [P3] Brainstorm ideas for next sprint (use Notes app)

Suggestion: Knock out those expense approvals first (quick admin)."
```

### Example 3: Low Energy Mode
```
User: "I'm exhausted, but still have an hour to work. What can I handle?"

Context:
- Energy: low
- Time: 1hr
- Location: home (inferred)

Output:
"Low-energy tasks for the next hour:

1. [P2] Organize project files (30min) - Mindless cleanup
2. [P3] Watch team training video (45min) - Passive learning
3. [P3] Update documentation links (20min) - Routine admin
4. [P2] Respond to non-urgent emails (30min) - Light communication

Suggestion: File organization - you can listen to music and zone out a bit while tidying up."
```

## Error Handling

**No tasks match context:**
```
"No tasks match your current context ({context}).

Options:
1. Relax constraints - show tasks slightly longer than {time_available}?
2. Use this time for learning/reading (always productive)?
3. Take a break - sometimes rest is the best use of time!"
```

**User context unclear:**
```
Ask clarifying questions:
"To suggest the right tasks, help me understand your situation:
- Where are you working? (home/office/coffee shop)
- How much time do you have?
- How's your energy level? (focused/normal/tired)"
```

## Notes

- Context tags must be added to tasks (educate user on tagging)
- Learn user's patterns over time (coffee shop Wednesdays, tired afternoons)
- Don't be rigid - if nothing perfectly matches, suggest close matches
- Context changes throughout the day - re-run when context shifts
- Encourage batching similar-context work (all phone tasks together)
