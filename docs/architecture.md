# Motion Alternative: Skills Stack Architecture
## System Design for FOSS Productivity Suite

> **Purpose:** Define the architecture for building Motion-like AI productivity features using Claude Code Skills and FOSS tools

---

## ARCHITECTURE OVERVIEW

```
┌────────────────────────────────────────────────────────────────┐
│                        USER INTERFACES                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │   CLI    │  │   TUI    │  │   Web    │  │  Mobile  │      │
│  │ (Claude  │  │(terminal)│  │   App    │  │   App    │      │
│  │  Code)   │  │   UI     │  │ (future) │  │ (future) │      │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘      │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                      CLAUDE CODE                                │
│                   (Orchestration Engine)                        │
│                                                                 │
│  Reads and follows instructions from Skills                    │
│  Uses native tools: Bash, Read, Write, MCP tools              │
│  Manages context window and progressive disclosure             │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                  SKILLS LAYER (This Repo)                       │
│              Markdown instruction files in .claude/skills/      │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  task-scheduler/          (Schedule tasks into calendar) │  │
│  │    ├── SKILL.md           (Instructions for Claude)      │  │
│  │    └── helpers/           (Optional Python scripts)      │  │
│  │                                                           │  │
│  │  priority-assistant/      (Calculate task priorities)    │  │
│  │    ├── SKILL.md                                          │  │
│  │    └── score.py           (Priority scoring script)      │  │
│  │                                                           │  │
│  │  deadline-guardian/       (Detect at-risk deadlines)     │  │
│  │    ├── SKILL.md                                          │  │
│  │    └── risk_calc.py                                      │  │
│  │                                                           │  │
│  │  project-planner/         (Break down projects)          │  │
│  │    ├── SKILL.md                                          │  │
│  │    └── templates/         (Project templates)            │  │
│  │                                                           │  │
│  │  productivity-analyzer/   (Learn usage patterns)         │  │
│  │    ├── SKILL.md                                          │  │
│  │    └── analyze.py                                        │  │
│  │                                                           │  │
│  │  meeting-optimizer/       (Find optimal meeting times)   │  │
│  │    └── SKILL.md                                          │  │
│  │                                                           │  │
│  │  context-assistant/       (Filter tasks by context)      │  │
│  │    └── SKILL.md                                          │  │
│  └──────────────────────────────────────────────────────────┘  │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                   MCP INTEGRATION LAYER                         │
│              Extend Claude's tools with new capabilities        │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │  Calendar    │  │    Tasks     │  │   Projects   │        │
│  │ MCP Server   │  │ MCP Server   │  │ MCP Server   │        │
│  │              │  │              │  │              │        │
│  │ - CalDAV     │  │ - Vikunja    │  │ - Plane      │        │
│  │ - Events     │  │ - Taskwarrior│  │ - OpenProject│        │
│  │ - Free/busy  │  │ - Todoist    │  │ - Taiga      │        │
│  └──────────────┘  └──────────────┘  └──────────────┘        │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │   Notes      │  │   Search     │  │  Analytics   │        │
│  │ MCP Server   │  │ MCP Server   │  │ MCP Server   │        │
│  │              │  │              │  │              │        │
│  │ - Nextcloud  │  │ - Meilisearch│  │ - Metrics DB │        │
│  │ - Outline    │  │ - Vector DB  │  │ - Patterns   │        │
│  │ - Obsidian   │  │ - Semantic   │  │ - Reports    │        │
│  └──────────────┘  └──────────────┘  └──────────────┘        │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                  HELPER SERVICES (Optional)                     │
│         Used by Skills when complex computation needed          │
│                                                                 │
│  ┌────────────────────┐  ┌──────────────────────────────────┐ │
│  │ Scheduling Engine  │  │   Pattern Recognition            │ │
│  │ (OR-Tools/Python)  │  │   (ML/Statistics)                │ │
│  │                    │  │                                  │ │
│  │ - Constraint solve │  │  - Duration learning             │ │
│  │ - Optimal placement│  │  - Productivity patterns         │ │
│  │ - Re-optimization  │  │  - User preferences              │ │
│  └────────────────────┘  └──────────────────────────────────┘ │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                     DATA STORAGE LAYER                          │
│                                                                 │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐              │
│  │ PostgreSQL │  │   Redis    │  │ TimeSeries │              │
│  │            │  │            │  │   (SQLite) │              │
│  │ - Prefs    │  │ - Cache    │  │            │              │
│  │ - Learning │  │ - Sessions │  │ - Metrics  │              │
│  │ - History  │  │ - Queue    │  │ - Analytics│              │
│  └────────────┘  └────────────┘  └────────────┘              │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                  FOSS BACKEND SERVICES                          │
│                                                                 │
│  - Radicale (CalDAV Server)                                    │
│  - Vikunja (Task Management)                                   │
│  - Plane (Project Management)                                  │
│  - Nextcloud (Files, Notes, Calendar UI)                       │
│  - Meilisearch (Search Engine)                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## KEY ARCHITECTURAL PRINCIPLES

### 1. **Skills Are Markdown Instructions, Not Code**

**Per Anthropic's design philosophy:**
- Skills are markdown files (SKILL.md) that teach Claude how to use tools
- Claude sees only name/description initially (~20-30 tokens per Skill)
- Full content loaded only when Claude decides the Skill is relevant
- Helper scripts can be executed WITHOUT loading into context window

**"The context window is a public good"** - Skills must be extremely concise.

---

### 2. **Progressive Disclosure**

```
User Request
    ↓
Claude sees all Skill names/descriptions (minimal tokens)
    ↓
Claude decides which Skill(s) are relevant
    ↓
Claude loads only the SKILL.md files needed
    ↓
Claude follows instructions, using MCP tools + Bash/Read/Write
    ↓
(Optional) Claude executes helper scripts if needed
    ↓
Result returned to user
```

**Key Point:** Scripts execute without being read into context - this is how Skills scale!

---

### 3. **Skills vs MCP Servers**

| Component | Purpose | Format | Examples |
|-----------|---------|--------|----------|
| **MCP Server** | Extends Claude's **tools** | TypeScript/Python service | Calendar operations, Database queries |
| **Skill** | Extends Claude's **knowledge** | Markdown instructions | How to schedule tasks, How to prioritize |

**Together:**
- MCP Server gives Claude `calendar_create_event` tool
- Skill teaches Claude WHEN and HOW to use it intelligently
- Result: Claude can schedule tasks like Motion does

---

## LAYER DETAILS

### 1. SKILLS LAYER (This Repository)

**Directory Structure:**
```
.claude/skills/
├── task-scheduler/
│   ├── SKILL.md              # Main instructions (mandatory)
│   ├── find_slots.py         # Helper script (optional)
│   └── config/
│       └── defaults.yaml     # Default settings
│
├── priority-assistant/
│   ├── SKILL.md
│   └── calculate_score.py
│
├── deadline-guardian/
│   ├── SKILL.md
│   └── risk_calculator.py
│
├── project-planner/
│   ├── SKILL.md
│   └── templates/
│       ├── web-project.yaml
│       └── content-project.yaml
│
├── productivity-analyzer/
│   ├── SKILL.md
│   ├── analyze_patterns.py
│   └── data/
│       └── schema.sql
│
├── meeting-optimizer/
│   └── SKILL.md
│
└── context-assistant/
    └── SKILL.md
```

---

### SKILL ANATOMY

Every Skill has the same structure:

**File: `.claude/skills/{skill-name}/SKILL.md`**

```yaml
---
name: skill-name
description: What this Skill does and when Claude should use it (max 1024 chars)
---

# Skill Name

## Purpose
Brief explanation of the Skill's value

## Prerequisites
- MCP servers needed (if any)
- Required environment setup
- Dependencies

## When to Use
Clear triggers for when Claude should invoke this Skill

## Instructions
Step-by-step guidance for Claude (be concise!)

## Helper Scripts
How to use optional scripts (if any)

## Examples
Concrete usage examples

## Error Handling
How to handle common issues
```

---

### EXAMPLE SKILL: task-scheduler

**File: `.claude/skills/task-scheduler/SKILL.md`**

```markdown
---
name: task-scheduler
description: Automatically schedule tasks into CalDAV calendar by finding optimal time slots based on priority, deadline, and availability. Use when user wants to add tasks to calendar or optimize their schedule.
---

# Task Scheduler

## Purpose
Help users automatically schedule tasks into their calendar using AI to find optimal time slots.

## Prerequisites
- Calendar MCP server configured (CalDAV access)
- (Optional) Tasks MCP server for richer task metadata

## When to Use
- User says "schedule task X"
- User asks "when should I work on Y?"
- User wants "to add Z to calendar"
- User requests schedule optimization

## Instructions

### Step 1: Gather Requirements
Ask user for missing information:
- Task title (required)
- Estimated duration in hours (required)
- Deadline (optional but recommended)
- Priority: 1-4, where 1=highest (default: 3)
- Constraints (e.g., "must be morning", "only Fridays")

### Step 2: Fetch Calendar Data
Use Calendar MCP to get existing events:
- Time range: today → deadline (or +7 days if no deadline)
- Include all calendars user wants checked

### Step 3: Find Candidate Slots
Look for free time blocks that:
- Match task duration (continuous block)
- Fall within work hours (default: Mon-Fri 9am-5pm)
- Don't conflict with existing events
- Have 30min buffer before/after (for >2hr tasks)

### Step 4: Score Each Slot
Use this formula (higher = better):

```
Base score: 100

Time of day bonus:
  Morning (9am-12pm):  +20  (best for focus)
  Afternoon (1pm-4pm): +10
  Late day (4pm-5pm):  +5

Deadline urgency:
  Days until deadline: -(days * 2)
  (closer deadline = higher urgency)

Priority bonus:
  P1: +30
  P2: +15
  P3: +5
  P4: +0

Buffer bonus:
  Has 30min before & after: +10
```

Can use helper script for complex scoring:
```bash
python .claude/skills/task-scheduler/score_slot.py \
  --slot "2025-01-15T09:00:00" \
  --duration 3 \
  --deadline "2025-01-20" \
  --priority 2
```

### Step 5: Create Calendar Event
Use Calendar MCP `calendar_create_event`:
- Title: `[Task] {task_title}`
- Start/End: Best scored slot
- Description: Include priority and deadline
- Reminder: 30min before (if >2hr duration)

### Step 6: Explain Decision
Always tell user WHY you chose that slot:

Example:
> "I scheduled 'Write report' for Tuesday 9am-12pm because:
> - Morning is your most productive time for deep work (+20)
> - It's 3 days before your Friday deadline
> - You have no meetings nearby (clear focus time)
> - Priority 2 task gets premium time slot"

## Helper Scripts

### score_slot.py
Calculates priority score for a time slot.

**Usage:**
```bash
python .claude/skills/task-scheduler/score_slot.py \
  --slot "YYYY-MM-DDTHH:MM:SS" \
  --duration HOURS \
  --deadline "YYYY-MM-DD" \
  --priority 1-4
```

**Returns:** JSON with score and reasoning
```json
{
  "score": 145,
  "factors": {
    "base": 100,
    "time_of_day": 20,
    "urgency": 15,
    "priority": 10
  }
}
```

## Examples

### Example 1: Simple Task
```
User: "Schedule 'Write blog post' for 3 hours, due Friday"

Claude:
1. Checks calendar Mon-Fri
2. Finds Tuesday 9am-12pm free
3. Scores: 140 points (morning + 3 days buffer)
4. Creates event
5. Explains: "Tuesday morning is your best time for writing"
```

### Example 2: Urgent High-Priority
```
User: "P1: Fix production bug, 2 hours, ASAP"

Claude:
1. Looks for next available 2hr block
2. Finds today 2pm-4pm (score: 135)
3. Creates event immediately
4. Explains: "Scheduling today due to P1 priority and urgency"
```

### Example 3: Constrained Scheduling
```
User: "Schedule 'Team presentation prep', 4 hours, must be Thursday afternoon, due Friday"

Claude:
1. Filters to Thursday 1pm-5pm only
2. Finds Thursday 1pm-5pm (score: 110)
3. Creates event
4. Explains: "Only Thursday afternoon slot available per your constraint"
```

## Error Handling

**No available slots:**
- Suggest extending work hours OR
- Suggest moving lower-priority tasks OR
- Suggest extending deadline

**Deadline already passed:**
- Alert user clearly
- Ask for new deadline

**Conflicting events:**
- Never double-book
- Find next best available slot
- Ask user if they want to reschedule existing event

## Notes
- Always respect existing calendar commitments
- Default work hours: Mon-Fri 9am-5pm (ask user if different)
- For recurring tasks, ask if user wants series or single instance
- Consider user's timezone (from calendar settings)
```

---

### EXAMPLE SKILL: priority-assistant

**File: `.claude/skills/priority-assistant/SKILL.md`**

```markdown
---
name: priority-assistant
description: Calculate intelligent priority scores for tasks using multiple factors (deadline, importance, effort, dependencies). Use when user asks "what should I work on?" or "prioritize my tasks".
---

# Priority Assistant

## Purpose
Help users understand which tasks deserve their attention right now using multi-factor scoring.

## Prerequisites
- Tasks MCP server (to fetch task list)
- (Optional) Calendar MCP (to check available time)

## When to Use
- User asks "what should I work on?"
- User asks "prioritize my tasks"
- User wants to know "what's most important?"
- User requests task ranking

## Instructions

### Step 1: Fetch All Active Tasks
Use Tasks MCP to get incomplete tasks:
```
Filter: status = "incomplete"
Include: title, deadline, priority, estimated_duration, dependencies, tags
```

### Step 2: Calculate Priority Score for Each

**Scoring Algorithm:**

```
Base score: 100

Factor 1: Deadline Proximity (0-40 points)
  If no deadline: 0
  If deadline:
    days_until = deadline - today
    score = 40 / (days_until + 1)
    (closer deadline = higher score)

Factor 2: User-Assigned Priority (0-30 points)
  P1 (highest): 30
  P2: 20
  P3: 10
  P4: 5
  None: 0

Factor 3: Dependencies (0-20 points)
  If task blocks others: 20
  If task has no dependencies: 0
  (blocking tasks = higher priority)

Factor 4: Quick Win Bonus (0-15 points)
  If duration < 30min: 15
  If duration < 1hr: 10
  If duration < 2hr: 5
  Else: 0

Factor 5: Age in Backlog (0-10 points)
  weeks_old = (today - created_date) / 7
  score = min(weeks_old * 2, 10)
  (older tasks get attention)

Total Score = sum of all factors
```

Use helper script for calculation:
```bash
python .claude/skills/priority-assistant/calculate_score.py \
  --deadline "2025-01-20" \
  --priority 2 \
  --duration 3 \
  --blocks 2 \
  --created "2025-01-01"
```

### Step 3: Rank Tasks
Sort tasks by computed score (highest first).

### Step 4: Present Top 3-5 Tasks
Show user the top priority tasks with:
- Task title
- Computed score
- WHY it's high priority (breakdown of factors)

Example output:
```
Your top priorities right now:

1. [Score: 165] Fix production bug
   Why: P1 priority (30), deadline tomorrow (38), blocks 2 tasks (20)

2. [Score: 142] Complete Q4 report
   Why: Deadline in 2 days (26), P2 priority (20), in backlog 3 weeks (6)

3. [Score: 135] Review PR #234
   Why: Quick win <30min (15), P1 priority (30), blocks deployment (20)
```

### Step 5: Recommend Next Action
Tell user: "I recommend starting with '{top_task}' because {reasoning}"

## Helper Scripts

### calculate_score.py
Calculates priority score for a task.

**Usage:**
```bash
python .claude/skills/priority-assistant/calculate_score.py \
  --deadline "YYYY-MM-DD" \
  --priority 1-4 \
  --duration HOURS \
  --blocks NUM_TASKS \
  --created "YYYY-MM-DD"
```

**Returns:**
```json
{
  "total_score": 165,
  "factors": {
    "deadline": 38,
    "priority": 30,
    "dependencies": 20,
    "quick_win": 15,
    "age": 6
  }
}
```

## Examples

### Example 1: Daily Planning
```
User: "What should I work on today?"

Claude:
1. Fetches 23 incomplete tasks
2. Calculates scores for all
3. Ranks by score
4. Shows top 5
5. Recommends: "Start with 'Fix login bug' (score 178) - P1, deadline today, blocks 3 tasks"
```

### Example 2: Context-Aware Priority
```
User: "I have 30 minutes before my meeting, what should I tackle?"

Claude:
1. Fetches tasks
2. Filters to tasks <30min duration
3. Calculates scores
4. Recommends highest-score quick win
5. "Review PR #156 (15min estimate, score 145)"
```

## Error Handling
- If no tasks: "You have no incomplete tasks!"
- If all tasks have same score: Use age as tiebreaker
- If task missing data: Use defaults (priority=3, no deadline, etc.)

## Notes
- Score algorithm is configurable in calculate_score.py
- Users can adjust weights if they prefer different factors
- Re-run daily as deadlines approach (scores change daily)
```

---

### EXAMPLE SKILL: deadline-guardian

**File: `.claude/skills/deadline-guardian/SKILL.md`**

```markdown
---
name: deadline-guardian
description: Proactively detect at-risk project deadlines by comparing required work vs available time. Warns users days/weeks in advance. Use for daily checks or when user asks about project status.
---

# Deadline Guardian

## Purpose
Detect projects at risk of missing deadlines and warn users with enough time to take action.

## Prerequisites
- Tasks MCP server (to get project tasks)
- Calendar MCP server (to calculate available time)
- (Optional) Projects MCP server (for richer project data)

## When to Use
- Daily automated check (morning)
- User asks "will I meet my deadline?"
- User requests project status
- User asks "what's at risk?"

## Instructions

### Step 1: Identify Projects with Deadlines
Fetch all active projects that have:
- Deadline date set
- Incomplete tasks

### Step 2: For Each Project, Calculate Risk

**Required Time:**
```
required_hours = sum(incomplete_task_durations)
```

**Available Time:**
```
days_until_deadline = deadline - today
work_days = count_weekdays(today, deadline)  # exclude weekends
hours_per_day = 8  # configurable

Use Calendar MCP to get existing meetings between now and deadline
meeting_hours = sum(meeting_durations)

available_hours = (work_days * hours_per_day) - meeting_hours
```

**Risk Score:**
```
buffer_needed = required_hours * 0.20  # 20% buffer
risk_score = (required_hours + buffer_needed) / available_hours

Risk levels:
  risk_score <= 0.7:  ✅ On track
  risk_score <= 0.9:  ⚠️  At risk
  risk_score <= 1.2:  🔴 Critical
  risk_score > 1.2:   ❌ Impossible
```

Use helper script:
```bash
python .claude/skills/deadline-guardian/calculate_risk.py \
  --project-id "proj-123" \
  --deadline "2025-02-01" \
  --required-hours 80 \
  --calendar-range "2025-01-15:2025-02-01"
```

### Step 3: Generate Warnings
For projects that are At Risk or worse:

**Warning Format:**
```
⚠️ DEADLINE RISK: {Project Name}
Status: {At Risk / Critical / Impossible}
Deadline: {date} ({days} days away)

Analysis:
- Required work: {hours}h remaining
- Available time: {hours}h (after meetings)
- Risk score: {score} ({percentage}% over capacity)

Recommendations:
1. {action 1}
2. {action 2}
3. {action 3}
```

**Recommendation Logic:**
- If risk_score > 1.0: Suggest extending deadline OR cutting scope
- If risk_score > 0.9: Suggest reprioritizing other work
- If critical tasks: Suggest getting help / delegating

### Step 4: Notify User
- Critical/Impossible: Notify immediately
- At Risk: Include in daily morning briefing
- On Track: Only show if user specifically asks

## Helper Scripts

### calculate_risk.py
Computes risk score for a project deadline.

**Usage:**
```bash
python .claude/skills/deadline-guardian/calculate_risk.py \
  --project-id PROJECT_ID \
  --deadline YYYY-MM-DD \
  --required-hours NUM \
  --calendar-range START:END
```

**Returns:**
```json
{
  "risk_score": 1.15,
  "risk_level": "critical",
  "details": {
    "required_hours": 60,
    "available_hours": 52,
    "work_days": 10,
    "meeting_hours": 18,
    "buffer_needed": 12
  },
  "recommendations": [
    "Extend deadline by 5 work days",
    "Cut optional features (12h reduction needed)",
    "Delegate 2 tasks to team members"
  ]
}
```

## Examples

### Example 1: Early Warning (2 weeks out)
```
User: "Check my deadlines"

Claude runs deadline-guardian:
- Website Redesign: Feb 15 deadline
- Required: 60h, Available: 52h
- Risk: Critical (115%)

Output:
🔴 CRITICAL: Website Redesign
Your project is at risk! Need 60h but only have 52h available before Feb 15.

Suggestions:
1. Extend deadline to Feb 22 (+5 work days)
2. Cut "testimonials section" feature (-8h)
3. Get help from Sarah on 2 backend tasks (-12h)
```

### Example 2: Daily Morning Check
```
Automated morning run:

Claude checks all projects:
- Q1 Planning: ✅ On track (67% capacity)
- Blog Series: ⚠️ At risk (88% capacity)
- Client Project: ✅ On track (45% capacity)

Daily briefing includes:
"⚠️ Your Blog Series is getting tight - using 88% of available time before Jan 25. Consider bumping 2 low-priority tasks to next week."
```

## Error Handling
- No calendar access: Use work_days * 8 as available hours estimate
- Missing task durations: Warn user to add estimates
- Past deadlines: Alert immediately, ask for new deadline

## Notes
- Run daily at 8am for proactive warnings
- Risk buffer (20%) accounts for unexpected delays
- Consider user's historical velocity for better estimates
```

---

## HELPER SERVICES (Optional)

While Skills are markdown instructions, some complex computations benefit from helper scripts or services.

### When to Use Helper Scripts vs Pure Instructions

**Use Helper Scripts When:**
- Complex mathematical calculations (constraint solving, optimization)
- Performance-critical operations (analyzing thousands of data points)
- External API calls with complex auth
- Statistical analysis or ML inference

**Use Pure Instructions When:**
- Simple logic (if/then decisions)
- Basic data transformation
- Calling MCP tools in sequence
- Formatting output

---

### Example Helper Service: Scheduling Engine

**Purpose:** Solve constraint satisfaction problem for optimal task placement

**Technology:** Google OR-Tools (Python)

**Location:** `.claude/skills/task-scheduler/helpers/schedule_engine.py`

**Usage from Skill:**
```markdown
## Step 4: Find Optimal Slot (Complex Cases)

For schedules with 10+ tasks or complex constraints, use scheduling engine:

```bash
python .claude/skills/task-scheduler/helpers/schedule_engine.py \
  --tasks tasks.json \
  --calendar events.json \
  --output schedule.json
```

Engine will:
1. Load tasks and calendar constraints
2. Use OR-Tools CP-SAT solver
3. Optimize for: earliest completion, minimal fragmentation
4. Output optimized schedule
```

**Implementation:** (Separate from SKILL.md, only loaded when executed)

---

## MCP INTEGRATION LAYER

MCP servers provide the "tools" that Skills teach Claude to use.

### Required MCP Servers

1. **Calendar MCP** - CalDAV operations
   - Existing: `caldav-mcp` ✅
   - Tools: list_events, create_event, update_event, delete_event
   - Need to add: `find_free_slots` tool

2. **Tasks MCP** - Task management
   - Status: Need to build 🔨
   - Backend: Vikunja (REST API)
   - Tools: list, get, create, update, complete, get_dependencies

3. **Projects MCP** - Project management
   - Status: Need to build 🔨
   - Backend: Plane (REST API)
   - Tools: list, get, create, get_timeline, get_capacity

4. **Analytics MCP** - Metrics tracking
   - Status: Need to build 🔨
   - Storage: SQLite + TimeSeries tables
   - Tools: track_event, get_metrics, get_productivity_report

5. **Search MCP** - Full-text and semantic search
   - Status: Need to build 🔨
   - Backend: Meilisearch + Qdrant
   - Tools: query, semantic_search, suggest_related

6. **Notes MCP** - Note and document access
   - Status: Partial (use filesystem MCP) ⚠️
   - Backend: Nextcloud Notes or Obsidian
   - Tools: list, get, create, link_to_task

**See [mcp-integration-guide.md](./mcp-integration-guide.md) for detailed specs.**

---

## DATA FLOW EXAMPLES

### Example 1: User Schedules a Task

```
1. User: "Schedule 'Write blog post' for 3 hours, due Friday"

2. Claude Code:
   - Sees user request
   - Scans available Skills (sees task-scheduler description)
   - Decides task-scheduler is relevant
   - Loads .claude/skills/task-scheduler/SKILL.md

3. Claude follows SKILL.md instructions:
   a) Gathers requirements (already provided by user)
   b) Calls Calendar MCP: calendar_list_events(start=today, end=friday)
   c) Analyzes free slots
   d) Optionally runs: python score_slot.py for each slot
   e) Picks best slot: Tuesday 9am-12pm (score: 145)
   f) Calls Calendar MCP: calendar_create_event(...)
   g) Explains to user why Tuesday 9am was chosen

4. User sees:
   "✅ Scheduled 'Write blog post' for Tuesday 9am-12pm.

   I chose this time because:
   - Morning is your most productive time for writing (+20)
   - It's 3 days before your Friday deadline (good buffer)
   - You have no meetings nearby (uninterrupted focus)
   - Priority 3 task fits well in this premium slot"
```

**Tokens Used:**
- Skill description in initial scan: ~25 tokens
- SKILL.md loaded when needed: ~800 tokens
- Helper script: 0 tokens (executed, not loaded)
- Total: ~825 tokens (vs thousands if code was loaded)

---

### Example 2: Daily Deadline Check

```
1. Automated trigger (8am daily):
   "Check for at-risk deadlines"

2. Claude Code:
   - Loads deadline-guardian Skill
   - Follows SKILL.md instructions

3. Claude executes:
   a) Calls Tasks MCP: tasks_list(filter=has_deadline)
   b) For each project with deadline:
      - Calls Calendar MCP: calendar_list_events(today → deadline)
      - Runs: python calculate_risk.py --project-id X --deadline Y
      - Gets risk score
   c) Identifies: "Website Redesign" = Critical (risk: 1.15)
   d) Generates warning with recommendations
   e) Sends notification to user

4. User receives (via notification or morning briefing):
   "🔴 CRITICAL: Website Redesign deadline at risk!

   You need 60 hours of work but only have 52 hours available before Feb 15.

   Recommendations:
   1. Extend deadline to Feb 22 (+5 work days)
   2. Cut scope: Remove testimonials section (-8h)
   3. Delegate: Assign 2 backend tasks to Sarah (-12h)"
```

---

## DEPLOYMENT ARCHITECTURE

### Single-User Setup (Phase 1)

```
Developer's Machine (Linux/Mac)
├── Claude Code CLI
├── .claude/
│   ├── skills/          ← This repo cloned here
│   │   ├── task-scheduler/
│   │   ├── priority-assistant/
│   │   └── ...
│   └── config.json      ← MCP servers configured
│
├── Docker Compose Stack
│   ├── radicale         (CalDAV server)
│   ├── vikunja          (Tasks)
│   ├── postgresql       (Data storage)
│   └── redis            (Cache)
│
└── Helper Services (optional)
    └── venv/
        └── schedule_engine.py
```

**Setup:**
```bash
# 1. Clone this repo into .claude/skills
cd ~/.claude
git clone https://github.com/you/motion-foss-skills skills/

# 2. Start FOSS backend services
cd ~/productivity-stack
docker-compose up -d

# 3. Configure MCP servers in Claude Code
cat ~/.claude/config.json
{
  "mcpServers": {
    "calendar": {
      "command": "npx",
      "args": ["-y", "@dominik1001/caldav-mcp"],
      "env": {
        "CALDAV_BASE_URL": "http://localhost:5232",
        "CALDAV_USERNAME": "user",
        "CALDAV_PASSWORD": "pass"
      }
    },
    "tasks": {
      "command": "node",
      "args": ["~/mcp-servers/vikunja-tasks/dist/index.js"],
      "env": {
        "VIKUNJA_URL": "http://localhost:3456",
        "VIKUNJA_TOKEN": "your-token"
      }
    }
  }
}

# 4. Test
claude "schedule 'test task' for 1 hour"
```

---

## TECHNOLOGY CHOICES

### Skills Layer
- **Language:** Markdown (SKILL.md files)
- **Helper Scripts:** Python 3.11+ (for OR-Tools, ML, statistics)
- **Alternative Scripts:** Bash, Node.js (whatever's executable)

### MCP Servers
- **Primary:** TypeScript/Node.js (best MCP SDK support)
- **Alternative:** Python (also has official MCP SDK)

### Helper Services
- **Scheduling Engine:** Google OR-Tools (Python)
- **Pattern Recognition:** scikit-learn, pandas (Python)
- **Data Analysis:** SQLite, PostgreSQL

### FOSS Backend
- **Calendar:** Radicale (CalDAV)
- **Tasks:** Vikunja (Go, REST API)
- **Projects:** Plane (TypeScript/Python)
- **Search:** Meilisearch (Rust)
- **Database:** PostgreSQL 15+
- **Cache:** Redis 7+

---

## DEVELOPMENT WORKFLOW

### Creating a New Skill

1. **Create directory:**
   ```bash
   mkdir -p .claude/skills/my-new-skill
   cd .claude/skills/my-new-skill
   ```

2. **Write SKILL.md:**
   ```markdown
   ---
   name: my-new-skill
   description: What it does and when to use it (concise!)
   ---

   # My New Skill

   ## Purpose
   One sentence.

   ## When to Use
   - Trigger 1
   - Trigger 2

   ## Instructions
   Step-by-step for Claude...
   ```

3. **(Optional) Add helper scripts:**
   ```bash
   touch helper.py
   chmod +x helper.py
   ```

4. **Test with Claude Code:**
   ```bash
   claude "trigger phrase that should invoke my skill"
   ```

5. **Iterate:**
   - Check Claude actually loads the Skill (use `/debug` if available)
   - Refine description if Claude doesn't auto-select it
   - Simplify SKILL.md if it's too verbose
   - Move complex logic to helper scripts

### Best Practices (From Anthropic)

1. **"Claude is already very smart"** - Don't explain things Claude already knows
2. **Context window is a public good** - Be extremely concise
3. **Progressive disclosure** - Put rarely-used details in separate files
4. **Test with target models** - Sonnet 4.5, Haiku, etc. behave differently
5. **Clear descriptions** - Skill description determines when Claude uses it
6. **Examples matter** - Concrete examples help Claude understand
7. **Error handling** - Tell Claude what to do when things fail

---

## PERFORMANCE TARGETS

| Operation | Target | How Achieved |
|-----------|--------|--------------|
| Skill discovery | <10ms | Only YAML frontmatter scanned |
| Skill loading | <50ms | Markdown file read on-demand |
| Priority calculation | <100ms | Python script, 100 tasks |
| Schedule optimization | <2s | OR-Tools constraint solver, 50 tasks |
| Calendar sync | <1s | CalDAV MCP, cached |
| Risk detection | <500ms | Simple math, parallel project checks |

---

## TESTING STRATEGY

### 1. Skill Instruction Testing
- Read SKILL.md as a human - is it clear?
- Ask colleague to follow instructions - do they work?
- Test with Claude - does it understand?

### 2. Helper Script Testing
- Unit tests for all Python scripts
- Test edge cases (no deadline, empty calendar, etc.)
- Performance benchmarks

### 3. Integration Testing
- Full flow: user request → skill → MCP → backend → response
- Test with real data (actual calendar, tasks)
- Measure time savings vs manual workflow

### 4. A/B Testing
- Compare AI scheduling vs user's manual schedule
- Measure: time saved, schedule adherence, user satisfaction

---

## ROADMAP ALIGNMENT

### Phase 1: Foundation (Weeks 1-8)
**Focus:** Core Skills as markdown files

- ✅ Write task-scheduler SKILL.md
- ✅ Write priority-assistant SKILL.md
- ✅ Write deadline-guardian SKILL.md
- ✅ Build Tasks MCP server (Vikunja)
- ✅ Build Analytics MCP server (minimal)
- ✅ Test with Claude Code CLI

**Deliverable:** Working Skills that automate basic scheduling

---

### Phase 2: Intelligence (Weeks 9-14)
**Focus:** Add helper services for complex logic

- ✅ Build scheduling engine (OR-Tools)
- ✅ Add real-time re-optimization
- ✅ Build pattern recognition service
- ✅ Update Skills to use helper services

**Deliverable:** Adaptive scheduling that learns

---

### Phase 3: Enhancement (Weeks 15-20)
**Focus:** More Skills, better integration

- ✅ Write productivity-analyzer SKILL.md
- ✅ Write meeting-optimizer SKILL.md
- ✅ Write context-assistant SKILL.md
- ✅ Build Projects MCP server (Plane)
- ✅ Build Search MCP server (Meilisearch)

**Deliverable:** Complete productivity suite

---

## SUCCESS METRICS

### User Impact
- **Time Saved:** 2+ hours/week on task management
- **Schedule Adherence:** +15% tasks completed on time
- **Deadline Success:** +20% projects on schedule
- **Cognitive Load:** "What should I work on?" answered instantly

### Technical Performance
- **Skill Loading:** <50ms per skill
- **Context Usage:** <1000 tokens per skill invocation
- **Helper Script Execution:** <2s for complex operations
- **System Reliability:** 99.5% uptime

### Adoption
- **Daily Active Usage:** Skills invoked 5+ times/day
- **User Satisfaction:** SUS score >70
- **Retention:** 80%+ still using after 30 days

---

## RESOURCES

**Official Anthropic Documentation:**
- Skills Overview: https://docs.claude.com/en/docs/claude-code/skills
- Best Practices: https://docs.claude.com/en/docs/agents-and-tools/agent-skills/best-practices
- MCP Protocol: https://modelcontextprotocol.io

**This Project:**
- Feature Spec: [motion-ai-feature-spec.md](./motion-ai-feature-spec.md)
- MCP Integration: [mcp-integration-guide.md](./mcp-integration-guide.md)
- Parity Checklist: [parity-checklist.md](./parity-checklist.md)

**FOSS Tools:**
- Radicale: https://radicale.org
- Vikunja: https://vikunja.io
- Plane: https://plane.so
- OR-Tools: https://developers.google.com/optimization

---

## APPENDIX: Skills vs Services Comparison

### ❌ WRONG: Skills as Python Services (My Original Approach)

```python
# This is NOT how Skills work!
class TaskSchedulerSkill:
    def __init__(self):
        self.mcp = MCPClient()

    def schedule_task(self, task: Task) -> ScheduledSlot:
        events = self.mcp.call("calendar_list_events", {...})
        slot = self.optimize(task, events)
        return slot
```

**Problems:**
- Requires Claude to understand Python code
- Loads entire codebase into context
- Not how Anthropic designed Skills
- Defeats purpose of progressive disclosure

---

### ✅ CORRECT: Skills as Markdown Instructions

```markdown
---
name: task-scheduler
description: Schedule tasks into calendar optimally
---

# Task Scheduler

## Instructions

1. Get task details from user
2. Call calendar MCP: list_events
3. Find free slots
4. Score each slot using these factors: [...]
5. Create event with calendar MCP
6. Explain choice to user
```

**Benefits:**
- Clear, human-readable instructions
- Minimal context usage (~800 tokens)
- Claude already knows how to call MCP tools
- Easy to update and maintain
- Scales to unlimited Skills

---

**The key insight:** Skills teach Claude how to use tools, they don't replace Claude's intelligence with custom code.
