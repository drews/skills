# Motion Alternative: Skills Stack Architecture
## System Design for FOSS Productivity Suite

> **Purpose:** Define the architecture for building Motion-like AI productivity features using modular Skills and FOSS tools

---

## ARCHITECTURE OVERVIEW

```
┌────────────────────────────────────────────────────────────────┐
│                        USER INTERFACES                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │   CLI    │  │   TUI    │  │   Web    │  │  Mobile  │      │
│  │ (primary)│  │(terminal)│  │   App    │  │   App    │      │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘      │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                    SKILLS ORCHESTRATION LAYER                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │             Claude Code Skills (This Repo)               │  │
│  │  - task-scheduler-skill                                  │  │
│  │  - priority-assistant-skill                              │  │
│  │  - deadline-guardian-skill                               │  │
│  │  - project-planner-skill                                 │  │
│  │  - productivity-analyzer-skill                           │  │
│  │  - meeting-optimizer-skill                               │  │
│  │  - workflow-automation-skill                             │  │
│  └──────────────────────────────────────────────────────────┘  │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                   AI & INTELLIGENCE LAYER                       │
│  ┌────────────────────┐  ┌──────────────────────────────────┐ │
│  │   LLM Services     │  │   Intelligence Services          │ │
│  │                    │  │                                  │ │
│  │  - Claude API      │  │  - Scheduling Engine             │ │
│  │  - Local Ollama    │  │  - Priority Scoring              │ │
│  │  - OpenRouter      │  │  - Pattern Recognition           │ │
│  │                    │  │  - Learning/Personalization      │ │
│  └────────────────────┘  │  - Risk Detection                │ │
│                          │  - Capacity Planning             │ │
│                          └──────────────────────────────────┘ │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                   MCP INTEGRATION LAYER                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │  Calendar    │  │    Tasks     │  │   Projects   │        │
│  │ MCP Server   │  │ MCP Server   │  │ MCP Server   │        │
│  │              │  │              │  │              │        │
│  │ - CalDAV     │  │ - Vikunja    │  │ - Plane      │        │
│  │ - Events     │  │ - TaskWarrior│  │ - OpenProject│        │
│  │ - Scheduling │  │ - Todoist    │  │ - Taiga      │        │
│  └──────────────┘  └──────────────┘  └──────────────┘        │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │   Notes      │  │   Search     │  │  Analytics   │        │
│  │ MCP Server   │  │ MCP Server   │  │ MCP Server   │        │
│  │              │  │              │  │              │        │
│  │ - Nextcloud  │  │ - Meilisearch│  │ - Time Track │        │
│  │ - Outline    │  │ - Vector DB  │  │ - Metrics    │        │
│  │ - Obsidian   │  │ - Semantic   │  │ - Reporting  │        │
│  └──────────────┘  └──────────────┘  └──────────────┘        │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                     DATA STORAGE LAYER                          │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                    Persistence                            │  │
│  │                                                           │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐         │  │
│  │  │ PostgreSQL │  │   Redis    │  │  TimeSeries│         │  │
│  │  │            │  │            │  │            │         │  │
│  │  │ - Users    │  │ - Cache    │  │ - Metrics  │         │  │
│  │  │ - Prefs    │  │ - Sessions │  │ - Analytics│         │  │
│  │  │ - Learning │  │ - Queue    │  │ - Logs     │         │  │
│  │  └────────────┘  └────────────┘  └────────────┘         │  │
│  │                                                           │  │
│  │  ┌────────────┐  ┌────────────┐                          │  │
│  │  │ Vector DB  │  │   Files    │                          │  │
│  │  │ (Qdrant)   │  │ (Local FS) │                          │  │
│  │  │            │  │            │                          │  │
│  │  │ - Embeddings│  │ - Backups │                          │  │
│  │  │ - Semantic │  │ - Exports  │                          │  │
│  │  └────────────┘  └────────────┘                          │  │
│  └──────────────────────────────────────────────────────────┘  │
└───────────────────────────┬────────────────────────────────────┘
                            │
┌───────────────────────────┴────────────────────────────────────┐
│                  FOSS BACKEND SERVICES                          │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                  Self-Hosted Tools                        │  │
│  │                                                           │  │
│  │  - Radicale (CalDAV Server)                              │  │
│  │  - Vikunja (Task Management)                             │  │
│  │  - Plane (Project Management)                            │  │
│  │  - Nextcloud (Files, Notes, Calendar UI)                 │  │
│  │  - n8n (Workflow Automation)                             │  │
│  │  - Meilisearch (Search Engine)                           │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## LAYER DETAILS

### 1. USER INTERFACE LAYER

**Supported Interfaces:**

**CLI (Primary - Phase 1)**
```bash
# Example usage
motion schedule "Write quarterly report" --deadline "2025-01-15" --duration 4h
motion next  # Show what to work on now
motion status  # Show today's schedule
motion risks  # Show at-risk deadlines
```

**TUI (Terminal UI - Phase 2)**
- Rich terminal interface using `textual` or `rich`
- Dashboard view with schedule, tasks, priorities
- Keyboard-driven navigation
- Real-time updates

**Web App (Phase 3)**
- Self-hosted web interface
- Calendar view, task list, project boards
- Responsive design for mobile browsers

**Mobile App (Phase 4 - Optional)**
- React Native or Flutter
- Quick capture, notifications, schedule view
- Syncs with self-hosted backend

---

### 2. SKILLS ORCHESTRATION LAYER

**Skills Architecture:**

Each skill is a self-contained module that:
- Exposes specific capabilities (via MCP protocol or direct API)
- Can be invoked by Claude Code or other automation
- Interacts with MCP servers for data access
- Contains its own logic and decision-making
- Can invoke AI services when needed

**Core Skills:**

#### A. `task-scheduler-skill`
**Purpose:** Automatic task scheduling into calendar

**Capabilities:**
- Schedule single task into optimal calendar slot
- Batch schedule multiple tasks
- Re-schedule on conflicts or changes
- Respect constraints (work hours, focus time)

**Inputs:**
- Task (title, deadline, duration, priority, dependencies)
- Current calendar state
- User preferences (work hours, focus patterns)

**Outputs:**
- Scheduled calendar event (CalDAV)
- Explanation of scheduling decision
- Alternative slot suggestions

**Dependencies:**
- Calendar MCP Server (read/write)
- Tasks MCP Server (read)
- Scheduling Engine (constraint solver)

---

#### B. `priority-assistant-skill`
**Purpose:** Intelligent task prioritization

**Capabilities:**
- Calculate priority scores for all tasks
- Rank tasks by computed priority
- Answer "what should I work on next?"
- Explain priority decisions
- Re-prioritize on context changes

**Inputs:**
- All active tasks
- Current time and context
- User preferences

**Outputs:**
- Sorted task list by priority
- Top priority recommendation
- Priority explanations

**Dependencies:**
- Tasks MCP Server
- Priority Scoring Service
- Context detection

---

#### C. `deadline-guardian-skill`
**Purpose:** Proactive deadline risk management

**Capabilities:**
- Detect at-risk deadlines
- Calculate risk scores (probability of missing deadline)
- Send early warnings (days/weeks in advance)
- Suggest remediation actions

**Inputs:**
- All tasks/projects with deadlines
- Calendar availability
- Historical velocity data

**Outputs:**
- Risk reports (per project/deadline)
- Warning notifications
- Remediation suggestions (extend, cut scope, add resources)

**Dependencies:**
- Tasks MCP Server
- Projects MCP Server
- Calendar MCP Server
- Risk Detection Service

---

#### D. `project-planner-skill`
**Purpose:** AI-assisted project planning

**Capabilities:**
- Decompose project into tasks
- Estimate durations based on historical data
- Identify dependencies
- Predict completion timeline
- Generate project templates

**Inputs:**
- Project description
- Similar historical projects
- Team composition (if multi-user)

**Outputs:**
- Task breakdown (created in project management tool)
- Timeline estimate
- Resource allocation suggestions
- Project template (if pattern detected)

**Dependencies:**
- Projects MCP Server
- Tasks MCP Server
- LLM Service (Claude API or Ollama)
- Historical data analytics

---

#### E. `productivity-analyzer-skill`
**Purpose:** Learn user patterns and optimize scheduling

**Capabilities:**
- Track task completion patterns
- Identify peak productivity hours
- Learn task duration estimates
- Detect context preferences
- Personalize scheduling decisions

**Inputs:**
- Historical task completion data
- Time-of-day metrics
- Task types and durations
- User overrides/corrections

**Outputs:**
- Productivity heat map
- Learned preferences (task duration, best times)
- Personalization updates for other skills
- Weekly productivity reports

**Dependencies:**
- Analytics MCP Server
- Time tracking data
- Pattern Recognition Service
- Learning/Personalization Service

---

#### F. `meeting-optimizer-skill`
**Purpose:** Intelligent meeting management

**Capabilities:**
- Find optimal meeting times across attendees
- Create automatic meeting prep tasks
- Suggest meeting durations
- Optimize attendee lists
- Detect unnecessary meetings

**Inputs:**
- Meeting request (attendees, purpose, duration)
- All attendees' calendars
- Historical meeting patterns

**Outputs:**
- Ranked meeting time options
- Meeting prep tasks (scheduled)
- Attendee optimization suggestions
- Meeting efficiency score

**Dependencies:**
- Calendar MCP Server (multi-user)
- Tasks MCP Server
- Meeting Optimization Service

---

#### G. `workflow-automation-skill`
**Purpose:** Event-driven automation and integrations

**Capabilities:**
- Trigger actions on calendar/task events
- Connect skills together (workflows)
- Handle interruption recovery
- Execute scheduled automations
- Manage notifications

**Inputs:**
- Event streams (calendar changes, task updates)
- Automation rules/workflows
- User preferences

**Outputs:**
- Automated actions executed
- Workflow state updates
- Notifications sent

**Dependencies:**
- All MCP Servers (as event sources)
- n8n (workflow engine) or custom
- Notification service

---

### 3. AI & INTELLIGENCE LAYER

**LLM Services:**

**Claude API (Primary for complex reasoning)**
- Project decomposition
- Natural language understanding
- Complex decision explanations
- Template generation

**Local Ollama (For privacy-sensitive or high-volume tasks)**
- Task categorization
- Priority explanations
- Pattern recognition
- Quick classifications

**Usage Strategy:**
- Use Claude API for "Phase 1" complex reasoning (project planning)
- Use Local Ollama for "Phase 2" high-frequency tasks (priority scoring explanations)
- Cache LLM responses aggressively
- Minimize API calls through smart prompting

---

**Intelligence Services:**

These are deterministic or ML-based services (not LLM):

#### Scheduling Engine
**Technology:** Constraint satisfaction solver
- **Option 1:** Google OR-Tools (Python/C++)
- **Option 2:** OptaPlanner (Java)
- **Option 3:** Custom genetic algorithm

**Purpose:**
- Find optimal task placements in calendar
- Respect hard constraints (no conflicts)
- Optimize soft constraints (preferences)
- Handle re-scheduling efficiently

**API:**
```python
schedule_task(
    task: Task,
    calendar: Calendar,
    constraints: Constraints,
    preferences: Preferences
) -> ScheduledSlot
```

---

#### Priority Scoring Service
**Technology:** Weighted scoring algorithm (no ML initially)

**Factors:**
- Deadline proximity: `score = 1 / days_until_deadline`
- User priority: `1-4 (P1=4, P2=3, P3=2, P4=1)`
- Dependencies: `+1 if blocking other tasks`
- Duration: `+0.5 if <30min (quick win)`
- Age in backlog: `+0.1 per week`

**Formula:**
```
priority_score = (
    w1 * deadline_score +
    w2 * user_priority +
    w3 * dependency_score +
    w4 * duration_score +
    w5 * age_score
)
```

**Weights:** Configurable per user, learned over time

---

#### Pattern Recognition Service
**Technology:** Time-series analysis + ML

**Patterns to detect:**
- Peak productivity hours (statistical analysis)
- Task duration patterns (regression)
- Context preferences (clustering)
- Interruption patterns (anomaly detection)

**Models:**
- Task duration: Linear regression per task type
- Productivity: Moving averages + outlier detection
- Personalization: Incremental learning (online ML)

---

#### Risk Detection Service
**Technology:** Statistical modeling

**Risk Calculation:**
```python
available_time = sum(calendar_free_blocks(now, deadline))
required_time = sum(incomplete_task_durations)
buffer_time = required_time * 0.2  # 20% buffer

risk_score = required_time / (available_time - buffer_time)

# risk_score > 1.0 = impossible
# risk_score > 0.9 = critical
# risk_score > 0.7 = at-risk
# risk_score <= 0.7 = on-track
```

**Enhancements:**
- Monte Carlo simulation for confidence intervals
- Velocity-based adjustments (if team consistently slower/faster)
- Historical risk modeling (learn from past projects)

---

#### Learning & Personalization Service
**Technology:** User preference model + incremental learning

**What to learn:**
- Task duration corrections (user always takes 2x estimate)
- Scheduling preferences (user moves deep work to morning)
- Context patterns (user does admin on Fridays)
- Override patterns (user consistently overrides AI decision X)

**Storage:**
```json
{
  "user_id": "user123",
  "preferences": {
    "task_duration_multipliers": {
      "code_review": 1.5,
      "meetings": 1.0,
      "writing": 1.2
    },
    "scheduling_preferences": {
      "deep_work_hours": ["09:00-12:00"],
      "admin_hours": ["14:00-16:00"],
      "no_meetings": ["Friday"]
    },
    "learned_patterns": [
      {
        "pattern": "task_type=code_review AND time=morning",
        "action": "prefer_schedule",
        "confidence": 0.85,
        "sample_size": 23
      }
    ]
  }
}
```

---

### 4. MCP INTEGRATION LAYER

**MCP (Model Context Protocol) Servers:**

MCP servers provide standardized access to external tools/data sources. Each skill interacts with data through MCP servers rather than direct API calls.

**Benefits:**
- Standardized interface across different backends
- Easy to swap implementations (e.g., Vikunja → Todoist)
- Built-in authentication and rate limiting
- Can be used by Claude Code directly

---

#### Calendar MCP Server
**Backends Supported:**
- Radicale (CalDAV)
- Nextcloud Calendar
- Google Calendar (via CalDAV)
- Apple Calendar (via CalDAV)

**Capabilities:**
```json
{
  "tools": [
    "calendar.list_events",
    "calendar.get_event",
    "calendar.create_event",
    "calendar.update_event",
    "calendar.delete_event",
    "calendar.find_free_slots",
    "calendar.subscribe_changes"
  ],
  "resources": [
    "calendar://{calendar_id}/events/{event_id}"
  ]
}
```

**Example Usage:**
```python
# Via MCP
events = mcp.call("calendar.list_events", {
    "start": "2025-01-10T00:00:00Z",
    "end": "2025-01-17T00:00:00Z",
    "calendar": "work"
})

free_slots = mcp.call("calendar.find_free_slots", {
    "duration_minutes": 120,
    "start": "2025-01-10T09:00:00Z",
    "end": "2025-01-10T17:00:00Z"
})
```

---

#### Tasks MCP Server
**Backends Supported:**
- Vikunja
- Taskwarrior
- Todoist
- Nextcloud Tasks

**Capabilities:**
```json
{
  "tools": [
    "tasks.list",
    "tasks.get",
    "tasks.create",
    "tasks.update",
    "tasks.delete",
    "tasks.complete",
    "tasks.search",
    "tasks.get_dependencies"
  ],
  "resources": [
    "task://{task_id}"
  ]
}
```

**Example Usage:**
```python
# Via MCP
tasks = mcp.call("tasks.list", {
    "filter": {
        "status": "incomplete",
        "deadline_before": "2025-01-20"
    },
    "sort": "deadline"
})

mcp.call("tasks.complete", {"task_id": "task-123"})
```

---

#### Projects MCP Server
**Backends Supported:**
- Plane
- OpenProject
- Taiga
- Linear

**Capabilities:**
```json
{
  "tools": [
    "projects.list",
    "projects.get",
    "projects.create",
    "projects.update",
    "projects.create_task",
    "projects.get_timeline",
    "projects.get_capacity"
  ],
  "resources": [
    "project://{project_id}",
    "project://{project_id}/tasks"
  ]
}
```

---

#### Notes MCP Server
**Backends Supported:**
- Nextcloud Notes
- Outline
- Obsidian (via filesystem)
- Standard Notes

**Capabilities:**
```json
{
  "tools": [
    "notes.list",
    "notes.get",
    "notes.create",
    "notes.update",
    "notes.search",
    "notes.link_to_task"
  ]
}
```

---

#### Search MCP Server
**Backends Supported:**
- Meilisearch
- Qdrant (vector search)
- PostgreSQL full-text search

**Capabilities:**
```json
{
  "tools": [
    "search.query",
    "search.semantic_search",
    "search.index_content",
    "search.suggest_related"
  ]
}
```

---

#### Analytics MCP Server
**Purpose:** Track and analyze user activity

**Capabilities:**
```json
{
  "tools": [
    "analytics.track_event",
    "analytics.get_metrics",
    "analytics.get_productivity_report",
    "analytics.get_time_tracking"
  ]
}
```

**Events tracked:**
- Task created/completed
- Schedule changes
- User overrides
- Interruptions
- Focus time blocks

---

### 5. DATA STORAGE LAYER

**PostgreSQL (Primary Database)**
- User accounts and preferences
- Learned preferences (personalization)
- Workflow definitions
- Audit logs

**Redis (Cache & Queue)**
- Calendar event cache
- Task list cache
- Job queue for background processing
- Session storage

**TimeSeries DB (Metrics)**
- Productivity metrics over time
- Task completion velocity
- Schedule adherence
- System performance

**Vector DB (Semantic Search)**
- Task/note/project embeddings
- Semantic search
- Related content suggestions

**File Storage**
- Exports and backups
- Generated reports
- User-uploaded files

---

### 6. FOSS BACKEND SERVICES

**Core Stack:**

```yaml
# docker-compose.yml example
services:
  # Calendar
  radicale:
    image: tomsquest/docker-radicale
    volumes:
      - ./data/calendar:/data

  # Tasks
  vikunja:
    image: vikunja/vikunja
    environment:
      VIKUNJA_DATABASE_TYPE: postgres

  # Projects
  plane:
    image: makeplane/plane-frontend
    depends_on:
      - plane-api

  # Notes & Files
  nextcloud:
    image: nextcloud:apache
    volumes:
      - ./data/nextcloud:/var/www/html

  # Workflow Automation
  n8n:
    image: n8nio/n8n
    environment:
      N8N_BASIC_AUTH_ACTIVE: "true"

  # Search
  meilisearch:
    image: getmeili/meilisearch
    environment:
      MEILI_MASTER_KEY: ${MEILI_KEY}

  # Vector Search
  qdrant:
    image: qdrant/qdrant

  # Database
  postgres:
    image: postgres:15
    volumes:
      - ./data/postgres:/var/lib/postgresql/data

  # Cache
  redis:
    image: redis:7-alpine
```

---

## DATA FLOW EXAMPLES

### Example 1: Scheduling a Task

```
1. User Input (CLI):
   $ motion schedule "Write blog post" --deadline "2025-01-20" --duration 3h

2. Skills Layer:
   task-scheduler-skill receives command
   ↓
   Calls Tasks MCP Server to create task
   ↓
   Calls Calendar MCP Server to get current events
   ↓
   Calls Scheduling Engine with:
     - Task requirements (3h, deadline 2025-01-20)
     - Calendar state (existing meetings)
     - User preferences (work hours, focus patterns from DB)
   ↓
   Scheduling Engine returns: 2025-01-15 09:00-12:00 (best slot)
   ↓
   Calls Calendar MCP Server to create event
   ↓
   Generates explanation: "Scheduled here because: morning focus time,
                           3-day buffer before deadline, no conflicts"

3. Output to User:
   ✓ Task scheduled: Wed Jan 15, 9:00am-12:00pm
   Reason: Morning is your most productive time for deep work
   Buffer: 5 days before deadline (low risk)
```

---

### Example 2: Real-Time Rescheduling

```
1. Event Detection:
   Meeting runs 30 minutes late (detected by Calendar MCP webhook)

2. Workflow Automation Skill:
   Receives calendar.event_extended event
   ↓
   Triggers task-scheduler-skill.reschedule()

3. Task Scheduler Skill:
   Fetches affected tasks (scheduled after the meeting)
   ↓
   Calls Scheduling Engine to re-optimize remaining day
   ↓
   Options:
     A. Shift all tasks 30 min later (may run past end-of-day)
     B. Compress tasks (shorter durations if feasible)
     C. Move some tasks to next day
   ↓
   Checks user's learned preference: "usually extends workday"
   ↓
   Applies option A + notifies user

4. Output:
   🔔 Schedule updated: Meeting ran late
   - "Review PRs" moved from 2:30pm → 3:00pm
   - "Update docs" moved from 4:00pm → 4:30pm
   (workday extended by 30 minutes to 6:00pm)
```

---

### Example 3: Deadline Risk Warning

```
1. Scheduled Check (Daily):
   deadline-guardian-skill runs at 8am daily

2. Skill Execution:
   Fetches all projects/tasks with deadlines
   ↓
   For each deadline:
     Calculate available_time (calendar free blocks)
     Calculate required_time (sum of incomplete task durations)
     risk_score = required_time / available_time
   ↓
   Finds: Project "Website Redesign" risk_score = 1.2 (120%)

3. Risk Analysis:
   Deadline: Jan 25 (15 days away)
   Required: 60 hours of work remaining
   Available: 50 hours (after meetings/commitments)
   Risk: IMPOSSIBLE (need 20% more time)

4. Remediation Suggestions:
   LLM Service generates options:
     A. Extend deadline to Feb 1 (+7 days)
     B. Cut optional features (reduce scope 20%)
     C. Add team member (split work with Sarah)

5. Output:
   ⚠️  RISK ALERT: Website Redesign
   Status: Deadline at risk (impossible to meet)
   Details: Need 60h, only 50h available before Jan 25

   Suggestions:
   1. Extend deadline to Feb 1
   2. Cut scope: Remove testimonials section (-8h)
   3. Get help: Assign 2 tasks to Sarah
```

---

## SKILL DEVELOPMENT GUIDELINES

### Creating a New Skill

**1. Skill Structure:**
```
skills/
  my-skill/
    skill.yaml           # Skill metadata
    main.py             # Entry point
    requirements.txt    # Python dependencies
    README.md           # Documentation
    tests/              # Unit tests
```

**2. skill.yaml Format:**
```yaml
name: my-skill
version: 1.0.0
description: Brief description of skill capabilities
author: Your Name
keywords: [productivity, automation]

interface:
  type: cli  # cli, mcp, api
  entry: python main.py

capabilities:
  - action: my_action
    description: What this action does
    inputs:
      - name: input1
        type: string
        required: true
    outputs:
      - name: result
        type: object

dependencies:
  mcp_servers:
    - calendar
    - tasks
  services:
    - scheduling_engine
  python_packages:
    - requests
    - click
```

**3. main.py Template:**
```python
#!/usr/bin/env python3
"""
My Skill - Brief description
"""

import click
from mcp_client import MCPClient

class MySkill:
    def __init__(self):
        self.mcp = MCPClient()

    def perform_action(self, input1: str) -> dict:
        """
        Main skill logic here
        """
        # Access data via MCP
        data = self.mcp.call("tasks.list", {})

        # Perform processing
        result = self.process(data)

        return result

@click.group()
def cli():
    """My Skill CLI"""
    pass

@cli.command()
@click.argument('input1')
def action(input1):
    """Perform the main action"""
    skill = MySkill()
    result = skill.perform_action(input1)
    click.echo(result)

if __name__ == '__main__':
    cli()
```

---

### Skill Communication Patterns

**Pattern 1: Direct Invocation (CLI)**
```bash
# User directly calls skill
$ motion task-scheduler schedule "Task name" --deadline 2025-01-20
```

**Pattern 2: Inter-Skill Invocation**
```python
# One skill calls another skill
from skills import get_skill

scheduler = get_skill('task-scheduler')
result = scheduler.schedule_task(task_data)
```

**Pattern 3: Event-Driven (via Workflow Automation)**
```python
# Skill registers for events
workflow.on_event('calendar.event_extended',
                   task_scheduler.reschedule_affected_tasks)
```

**Pattern 4: MCP Server (Claude Code integration)**
```json
// Skill exposes MCP server
// Claude Code can call it directly
{
  "name": "task-scheduler",
  "tools": [{
    "name": "schedule_task",
    "description": "Schedule a task into calendar",
    "inputSchema": {
      "type": "object",
      "properties": {
        "task_title": {"type": "string"},
        "duration_hours": {"type": "number"},
        "deadline": {"type": "string"}
      }
    }
  }]
}
```

---

## DEPLOYMENT ARCHITECTURE

### Single-User Deployment (Phase 1)

```
┌─────────────────────────────────────┐
│     Single Machine (Linux/Mac)      │
│                                     │
│  ┌───────────────────────────────┐  │
│  │  Docker Compose Stack         │  │
│  │  - Radicale                   │  │
│  │  - Vikunja                    │  │
│  │  - PostgreSQL                 │  │
│  │  - Redis                      │  │
│  │  - Meilisearch                │  │
│  └───────────────────────────────┘  │
│                                     │
│  ┌───────────────────────────────┐  │
│  │  Skills Runtime               │  │
│  │  - Python venv                │  │
│  │  - Claude Code integration    │  │
│  └───────────────────────────────┘  │
│                                     │
│  User accesses via:                 │
│  - Terminal (CLI/TUI)               │
│  - localhost:3000 (web UI)          │
└─────────────────────────────────────┘
```

**Setup:**
```bash
# Clone repo
git clone https://github.com/you/motion-foss-skills
cd motion-foss-skills

# Start services
docker-compose up -d

# Install skills
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python setup.py install

# Initialize
motion init
motion config set calendar.url "http://localhost:5232"
motion config set tasks.type "vikunja"
```

---

### Multi-User Deployment (Phase 3+)

```
┌─────────────────────────────────────────────────────────┐
│                    Cloud/VPS Server                      │
│                                                          │
│  ┌────────────────────────────────────────────────────┐ │
│  │  Reverse Proxy (Caddy/Nginx)                       │ │
│  │  - HTTPS/TLS termination                           │ │
│  │  - Authentication (OAuth2)                         │ │
│  └────────────────────────────────────────────────────┘ │
│                         │                                │
│  ┌────────────────────────────────────────────────────┐ │
│  │  Skills API Service (per-user isolation)          │ │
│  └────────────────────────────────────────────────────┘ │
│                         │                                │
│  ┌────────────────────────────────────────────────────┐ │
│  │  Backend Services (multi-tenant)                   │ │
│  │  - Radicale (per-user CalDAV)                      │ │
│  │  - Vikunja (multi-user mode)                       │ │
│  │  - PostgreSQL (shared, isolated by user_id)        │ │
│  └────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
                         │
                         │ (Users connect via)
                         │
        ┌────────────────┼────────────────┐
        │                │                │
   ┌────▼────┐      ┌────▼────┐     ┌────▼────┐
   │ Web App │      │ Mobile  │     │   CLI   │
   │ (React) │      │  App    │     │(Claude  │
   │         │      │         │     │ Code)   │
   └─────────┘      └─────────┘     └─────────┘
```

---

## TECHNOLOGY CHOICES

### Languages & Frameworks

**Python (Primary)**
- Skills implementation
- MCP server implementations
- CLI tools
- Reasoning: Best ecosystem for AI/ML, scheduling algorithms

**TypeScript/Node.js (Secondary)**
- Web frontend (React/Next.js)
- Some MCP servers (if better libraries available)

**Rust (Optional for performance-critical)**
- Scheduling engine (if Python too slow)
- Real-time event processing

---

### Key Libraries

**Scheduling & Optimization:**
- Google OR-Tools: Constraint programming
- PuLP: Linear programming (simpler alternative)
- Schedule: Simple Python job scheduling

**AI/ML:**
- LangChain: LLM orchestration
- scikit-learn: Pattern recognition, ML models
- pandas: Data analysis
- numpy: Numerical computing

**Calendar/Tasks:**
- caldav: CalDAV protocol (Python)
- ics: iCalendar format parsing
- python-dateutil: Date/time manipulation

**Web & API:**
- FastAPI: API server (if exposing HTTP API)
- Click: CLI framework
- Rich/Textual: Terminal UI

**Data & Storage:**
- SQLAlchemy: Database ORM
- Redis-py: Redis client
- Qdrant-client: Vector search

---

## TESTING STRATEGY

### Unit Tests
- Each skill has comprehensive unit tests
- Mock MCP servers for isolation
- Test all decision algorithms

### Integration Tests
- Test skill → MCP server → backend flow
- Test inter-skill communication
- Test event-driven workflows

### System Tests
- Full stack tests (docker-compose environment)
- Real user scenarios
- Performance benchmarks

### A/B Testing
- Compare AI scheduling vs manual
- Measure time savings
- Validate learned preferences

---

## PERFORMANCE TARGETS

| Operation | Target | Measurement |
|-----------|--------|-------------|
| Schedule single task | <500ms | Time from request to calendar update |
| Re-optimize schedule (50 tasks) | <2s | Real-time re-scheduling latency |
| Priority calculation (100 tasks) | <100ms | Scoring all tasks |
| Calendar sync | <1s | Fetch latest events via CalDAV |
| LLM call (project decomposition) | <10s | Claude API response time |
| Search query | <200ms | Meilisearch full-text search |
| Page load (web UI) | <1s | Initial render time |

---

## SECURITY & PRIVACY

### Data Protection
- All data stored locally (self-hosted)
- Optional encryption at rest (PostgreSQL)
- Encrypted API calls (HTTPS/TLS)
- No data sent to third parties (except opt-in LLM APIs)

### Authentication
- Single-user: Local authentication
- Multi-user: OAuth2 or LDAP
- API keys for programmatic access

### Privacy
- LLM calls can use local Ollama (no external API)
- Analytics data stays local
- Opt-in telemetry only

---

## ROADMAP ALIGNMENT

### Phase 1: MVP (Months 1-3)
- CLI interface
- Core skills: scheduler, priority, deadline guardian
- Single-user deployment
- CalDAV + Vikunja integration

### Phase 2: Intelligence (Months 4-6)
- Learning & personalization
- Real-time re-optimization
- Productivity analyzer skill
- Local LLM integration

### Phase 3: Polish (Months 7-9)
- Web UI
- Project planner skill
- Advanced integrations (Plane, Nextcloud)
- Performance optimization

### Phase 4: Scale (Months 10-12)
- Multi-user support
- Team collaboration features
- Mobile app (optional)
- Marketplace/plugin system

---

## SUCCESS METRICS

### User Metrics
- Time saved per week (target: 2+ hours)
- Schedule adherence (target: +15%)
- Deadline success rate (target: +20%)
- User satisfaction (SUS score target: 70+)

### Technical Metrics
- System uptime (target: 99.5%)
- Response times (all under targets above)
- Error rate (target: <1%)
- Test coverage (target: >80%)

### Adoption Metrics
- Active users
- Skills invocation frequency
- Feature usage (which skills used most)
- Retention (continued use after 30 days)
