# MCP Integration Guide
## Model Context Protocol for Motion Alternative Skills

> **Purpose:** Define MCP server requirements and integration points for building Motion-like productivity features

---

## WHAT IS MCP?

**Model Context Protocol (MCP)** is an open standard introduced by Anthropic in November 2024 to standardize how AI systems (like Claude) integrate with external tools, systems, and data sources.

**Key Benefits:**
- ✅ **Standardized interface:** Skills can work with any MCP-compatible backend
- ✅ **Swappable implementations:** Easy to switch from Vikunja to Todoist
- ✅ **Claude Code native:** MCP servers work directly with Claude Code
- ✅ **Community ecosystem:** Growing library of pre-built servers
- ✅ **Type-safe:** JSON Schema validation for inputs/outputs

**Adoption:**
- Anthropic Claude (native support)
- OpenAI (adopted March 2025 for ChatGPT, Agents SDK)
- Growing list of integrations (GitHub, Slack, Google Drive, etc.)

---

## MCP ARCHITECTURE FOR OUR SKILLS

```
┌──────────────────────────────────────────────────────────────┐
│                    Claude Code / User                        │
└────────────────────────┬─────────────────────────────────────┘
                         │
                         │ (invokes)
                         │
┌────────────────────────▼─────────────────────────────────────┐
│                  SKILLS (This Repo)                          │
│                                                              │
│  task-scheduler-skill                                        │
│  priority-assistant-skill                                    │
│  deadline-guardian-skill                                     │
│  project-planner-skill                                       │
│  productivity-analyzer-skill                                 │
│  meeting-optimizer-skill                                     │
│  workflow-automation-skill                                   │
└────────────────────────┬─────────────────────────────────────┘
                         │
                         │ (calls via MCP)
                         │
┌────────────────────────▼─────────────────────────────────────┐
│                    MCP SERVERS                               │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Calendar   │  │    Tasks     │  │   Projects   │      │
│  │ MCP Server   │  │  MCP Server  │  │  MCP Server  │      │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘      │
│         │                  │                  │              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │    Notes     │  │    Search    │  │  Analytics   │      │
│  │  MCP Server  │  │  MCP Server  │  │  MCP Server  │      │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘      │
└─────────┼──────────────────┼──────────────────┼──────────────┘
          │                  │                  │
┌─────────▼──────────────────▼──────────────────▼──────────────┐
│              BACKEND SERVICES (FOSS Stack)                   │
│                                                              │
│  Radicale, Vikunja, Plane, Nextcloud, Meilisearch, etc.    │
└──────────────────────────────────────────────────────────────┘
```

---

## REQUIRED MCP SERVERS

### 1. CALENDAR MCP SERVER

**Purpose:** Interface with CalDAV calendars for scheduling

**Status:** ✅ **Existing implementations available**

**Existing Servers:**
- [`caldav-mcp`](https://github.com/dominik1001/caldav-mcp) by dominik1001
- [`mcp-nextcloud-calendar`](https://github.com/Cheffromspace/mcp-nextcloud-calendar) by Cheffromspace
- [`mcp-server-caldav`](https://github.com/railmap/mcp-server-caldav) by railmap

**Required Tools:**

#### `calendar.list_events`
```json
{
  "name": "calendar_list_events",
  "description": "List calendar events within a time range",
  "inputSchema": {
    "type": "object",
    "properties": {
      "calendar": {
        "type": "string",
        "description": "Calendar name/ID (default: primary)"
      },
      "start": {
        "type": "string",
        "description": "Start datetime (ISO 8601)",
        "format": "date-time"
      },
      "end": {
        "type": "string",
        "description": "End datetime (ISO 8601)",
        "format": "date-time"
      }
    },
    "required": ["start", "end"]
  }
}
```

**Response:**
```json
{
  "events": [
    {
      "id": "evt-123",
      "summary": "Team Meeting",
      "start": "2025-01-15T10:00:00Z",
      "end": "2025-01-15T11:00:00Z",
      "location": "Conference Room A",
      "description": "Weekly sync",
      "attendees": ["alice@example.com", "bob@example.com"]
    }
  ]
}
```

---

#### `calendar.create_event`
```json
{
  "name": "calendar_create_event",
  "description": "Create a new calendar event",
  "inputSchema": {
    "type": "object",
    "properties": {
      "calendar": {"type": "string"},
      "summary": {
        "type": "string",
        "description": "Event title"
      },
      "start": {
        "type": "string",
        "format": "date-time"
      },
      "end": {
        "type": "string",
        "format": "date-time"
      },
      "description": {"type": "string"},
      "location": {"type": "string"},
      "attendees": {
        "type": "array",
        "items": {"type": "string"}
      }
    },
    "required": ["summary", "start", "end"]
  }
}
```

---

#### `calendar.update_event`
```json
{
  "name": "calendar_update_event",
  "description": "Update an existing event",
  "inputSchema": {
    "type": "object",
    "properties": {
      "event_id": {"type": "string"},
      "summary": {"type": "string"},
      "start": {"type": "string", "format": "date-time"},
      "end": {"type": "string", "format": "date-time"},
      "description": {"type": "string"}
    },
    "required": ["event_id"]
  }
}
```

---

#### `calendar.delete_event`
```json
{
  "name": "calendar_delete_event",
  "description": "Delete a calendar event",
  "inputSchema": {
    "type": "object",
    "properties": {
      "event_id": {"type": "string"}
    },
    "required": ["event_id"]
  }
}
```

---

#### `calendar.find_free_slots`
**Status:** ⚠️ **May need custom implementation**

```json
{
  "name": "calendar_find_free_slots",
  "description": "Find available time slots in calendar",
  "inputSchema": {
    "type": "object",
    "properties": {
      "duration_minutes": {
        "type": "number",
        "description": "Required duration in minutes"
      },
      "start": {
        "type": "string",
        "format": "date-time",
        "description": "Search window start"
      },
      "end": {
        "type": "string",
        "format": "date-time",
        "description": "Search window end"
      },
      "calendars": {
        "type": "array",
        "items": {"type": "string"},
        "description": "Calendars to check (for multi-calendar free/busy)"
      },
      "work_hours_only": {
        "type": "boolean",
        "description": "Only return slots during work hours"
      }
    },
    "required": ["duration_minutes", "start", "end"]
  }
}
```

**Response:**
```json
{
  "free_slots": [
    {
      "start": "2025-01-15T09:00:00Z",
      "end": "2025-01-15T11:00:00Z",
      "duration_minutes": 120
    },
    {
      "start": "2025-01-15T14:00:00Z",
      "end": "2025-01-15T16:30:00Z",
      "duration_minutes": 150
    }
  ]
}
```

---

#### `calendar.subscribe_changes` (Optional - Webhooks)
**Status:** ⚠️ **Advanced feature**

For real-time schedule re-optimization, we need to detect calendar changes as they happen.

**Options:**
1. **Polling:** Check for changes every 1-5 minutes (simple but inefficient)
2. **Webhooks:** CalDAV server pushes changes (requires server support)
3. **Event-driven:** MCP server exposes subscription mechanism

```json
{
  "name": "calendar_subscribe_changes",
  "description": "Subscribe to calendar change notifications",
  "inputSchema": {
    "type": "object",
    "properties": {
      "webhook_url": {
        "type": "string",
        "description": "URL to receive change notifications"
      },
      "calendars": {
        "type": "array",
        "items": {"type": "string"}
      }
    },
    "required": ["webhook_url"]
  }
}
```

---

**Configuration Example (caldav-mcp):**

```json
{
  "mcpServers": {
    "caldav": {
      "command": "npx",
      "args": ["-y", "@dominik1001/caldav-mcp"],
      "env": {
        "CALDAV_BASE_URL": "http://localhost:5232",
        "CALDAV_USERNAME": "user",
        "CALDAV_PASSWORD": "pass"
      }
    }
  }
}
```

**Recommendation:**
- **Use existing:** Start with `caldav-mcp` or `mcp-nextcloud-calendar`
- **Extend if needed:** Add `find_free_slots` tool via fork/PR
- **Backends supported:** Any CalDAV server (Radicale, Nextcloud, Google Calendar, iCloud)

---

### 2. TASKS MCP SERVER

**Purpose:** Interface with task management systems

**Status:** ❌ **Needs to be built**

**Target Backends:**
- Vikunja (REST API)
- Taskwarrior (CLI interface)
- Todoist (API - optional)
- Nextcloud Tasks (CalDAV VTODO)

**Required Tools:**

#### `tasks.list`
```json
{
  "name": "tasks_list",
  "description": "List tasks with optional filtering",
  "inputSchema": {
    "type": "object",
    "properties": {
      "status": {
        "type": "string",
        "enum": ["incomplete", "complete", "all"],
        "default": "incomplete"
      },
      "project": {"type": "string"},
      "priority": {
        "type": "number",
        "description": "Filter by priority (1-4)"
      },
      "deadline_before": {
        "type": "string",
        "format": "date-time"
      },
      "deadline_after": {
        "type": "string",
        "format": "date-time"
      },
      "tags": {
        "type": "array",
        "items": {"type": "string"}
      },
      "assigned_to": {"type": "string"},
      "sort": {
        "type": "string",
        "enum": ["deadline", "priority", "created", "title"]
      }
    }
  }
}
```

**Response:**
```json
{
  "tasks": [
    {
      "id": "task-123",
      "title": "Write blog post",
      "description": "Write Q1 recap blog post",
      "status": "incomplete",
      "priority": 2,
      "deadline": "2025-01-20T23:59:59Z",
      "estimated_duration_minutes": 180,
      "project": "Marketing",
      "tags": ["content", "high-priority"],
      "assigned_to": "alice",
      "created": "2025-01-10T09:00:00Z",
      "dependencies": ["task-120"]
    }
  ],
  "total": 15
}
```

---

#### `tasks.get`
```json
{
  "name": "tasks_get",
  "description": "Get a single task by ID",
  "inputSchema": {
    "type": "object",
    "properties": {
      "task_id": {"type": "string"}
    },
    "required": ["task_id"]
  }
}
```

---

#### `tasks.create`
```json
{
  "name": "tasks_create",
  "description": "Create a new task",
  "inputSchema": {
    "type": "object",
    "properties": {
      "title": {"type": "string"},
      "description": {"type": "string"},
      "priority": {"type": "number", "minimum": 1, "maximum": 4},
      "deadline": {"type": "string", "format": "date-time"},
      "estimated_duration_minutes": {"type": "number"},
      "project": {"type": "string"},
      "tags": {"type": "array", "items": {"type": "string"}},
      "assigned_to": {"type": "string"},
      "depends_on": {
        "type": "array",
        "items": {"type": "string"},
        "description": "Task IDs this task depends on"
      }
    },
    "required": ["title"]
  }
}
```

---

#### `tasks.update`
```json
{
  "name": "tasks_update",
  "description": "Update an existing task",
  "inputSchema": {
    "type": "object",
    "properties": {
      "task_id": {"type": "string"},
      "title": {"type": "string"},
      "description": {"type": "string"},
      "priority": {"type": "number"},
      "deadline": {"type": "string", "format": "date-time"},
      "estimated_duration_minutes": {"type": "number"},
      "status": {"type": "string", "enum": ["incomplete", "complete"]}
    },
    "required": ["task_id"]
  }
}
```

---

#### `tasks.complete`
```json
{
  "name": "tasks_complete",
  "description": "Mark a task as complete",
  "inputSchema": {
    "type": "object",
    "properties": {
      "task_id": {"type": "string"},
      "completed_at": {
        "type": "string",
        "format": "date-time",
        "description": "Optional completion time (defaults to now)"
      },
      "actual_duration_minutes": {
        "type": "number",
        "description": "How long it actually took (for learning)"
      }
    },
    "required": ["task_id"]
  }
}
```

---

#### `tasks.get_dependencies`
```json
{
  "name": "tasks_get_dependencies",
  "description": "Get dependency graph for a task or project",
  "inputSchema": {
    "type": "object",
    "properties": {
      "task_id": {"type": "string"},
      "project": {"type": "string"},
      "include_completed": {"type": "boolean", "default": false}
    }
  }
}
```

**Response:**
```json
{
  "graph": {
    "nodes": [
      {"id": "task-120", "title": "Design mockups", "status": "complete"},
      {"id": "task-123", "title": "Write blog post", "status": "incomplete"}
    ],
    "edges": [
      {"from": "task-120", "to": "task-123", "type": "blocks"}
    ]
  },
  "critical_path": ["task-120", "task-123"],
  "blocked_tasks": ["task-123"]
}
```

---

**Implementation Notes:**

**For Vikunja:**
```python
# Vikunja REST API wrapper
class VikunjaTasksServer:
    def __init__(self, base_url, api_token):
        self.base_url = base_url
        self.token = api_token

    async def list_tasks(self, status="incomplete", **filters):
        # Call Vikunja API: GET /api/v1/tasks
        # Transform to MCP response format
        pass
```

**For Taskwarrior:**
```python
# Taskwarrior CLI wrapper
class TaskwarriorTasksServer:
    async def list_tasks(self, status="incomplete", **filters):
        # Execute: task status:pending export
        # Parse JSON output
        # Transform to MCP format
        pass
```

---

### 3. PROJECTS MCP SERVER

**Purpose:** Interface with project management tools

**Status:** ❌ **Needs to be built**

**Target Backends:**
- Plane (REST API)
- OpenProject (REST API)
- Taiga (REST API)

**Required Tools:**

#### `projects.list`
```json
{
  "name": "projects_list",
  "description": "List all projects",
  "inputSchema": {
    "type": "object",
    "properties": {
      "status": {
        "type": "string",
        "enum": ["active", "completed", "archived", "all"]
      },
      "owned_by": {"type": "string"}
    }
  }
}
```

---

#### `projects.get`
```json
{
  "name": "projects_get",
  "description": "Get project details including tasks",
  "inputSchema": {
    "type": "object",
    "properties": {
      "project_id": {"type": "string"}
    },
    "required": ["project_id"]
  }
}
```

**Response:**
```json
{
  "project": {
    "id": "proj-456",
    "name": "Website Redesign",
    "description": "Complete overhaul of company website",
    "status": "active",
    "deadline": "2025-02-15T23:59:59Z",
    "created": "2024-12-01T00:00:00Z",
    "owner": "alice",
    "team": ["alice", "bob", "carol"],
    "tasks": [
      {"id": "task-101", "title": "Design mockups", "status": "complete"},
      {"id": "task-102", "title": "Frontend dev", "status": "in_progress"},
      {"id": "task-103", "title": "Backend API", "status": "incomplete"}
    ],
    "progress": {
      "completed_tasks": 5,
      "total_tasks": 15,
      "percentage": 33
    }
  }
}
```

---

#### `projects.create`
```json
{
  "name": "projects_create",
  "description": "Create a new project",
  "inputSchema": {
    "type": "object",
    "properties": {
      "name": {"type": "string"},
      "description": {"type": "string"},
      "deadline": {"type": "string", "format": "date-time"},
      "owner": {"type": "string"},
      "team": {"type": "array", "items": {"type": "string"}}
    },
    "required": ["name"]
  }
}
```

---

#### `projects.create_task`
```json
{
  "name": "projects_create_task",
  "description": "Create a task within a project",
  "inputSchema": {
    "type": "object",
    "properties": {
      "project_id": {"type": "string"},
      "title": {"type": "string"},
      "description": {"type": "string"},
      "assigned_to": {"type": "string"},
      "estimated_duration_hours": {"type": "number"},
      "depends_on": {"type": "array", "items": {"type": "string"}}
    },
    "required": ["project_id", "title"]
  }
}
```

---

#### `projects.get_timeline`
```json
{
  "name": "projects_get_timeline",
  "description": "Get project timeline with milestones and dependencies",
  "inputSchema": {
    "type": "object",
    "properties": {
      "project_id": {"type": "string"}
    },
    "required": ["project_id"]
  }
}
```

**Response:**
```json
{
  "timeline": {
    "start": "2024-12-01T00:00:00Z",
    "planned_end": "2025-02-15T23:59:59Z",
    "predicted_end": "2025-02-22T23:59:59Z",
    "confidence": 0.75,
    "milestones": [
      {
        "name": "Design Complete",
        "date": "2024-12-15T00:00:00Z",
        "status": "complete"
      },
      {
        "name": "Development Complete",
        "date": "2025-02-01T00:00:00Z",
        "status": "at_risk"
      }
    ],
    "critical_path": ["task-101", "task-102", "task-105"]
  }
}
```

---

#### `projects.get_capacity`
```json
{
  "name": "projects_get_capacity",
  "description": "Get team capacity and workload for project",
  "inputSchema": {
    "type": "object",
    "properties": {
      "project_id": {"type": "string"}
    },
    "required": ["project_id"]
  }
}
```

**Response:**
```json
{
  "capacity": {
    "team_members": [
      {
        "user": "alice",
        "capacity_hours_per_week": 40,
        "allocated_hours": 45,
        "utilization_percent": 112,
        "status": "overallocated"
      },
      {
        "user": "bob",
        "capacity_hours_per_week": 40,
        "allocated_hours": 25,
        "utilization_percent": 62,
        "status": "underallocated"
      }
    ],
    "project_total": {
      "required_hours": 200,
      "available_hours": 160,
      "deficit_hours": 40
    }
  }
}
```

---

### 4. NOTES MCP SERVER

**Purpose:** Access notes and documentation

**Status:** ⚠️ **Partial - existing servers for some backends**

**Existing Servers:**
- Nextcloud Notes (via general Nextcloud MCP if available)
- Obsidian (filesystem-based, can use existing filesystem MCP)

**Target Backends:**
- Nextcloud Notes
- Outline
- Obsidian
- Standard Notes

**Required Tools:**

#### `notes.list`
```json
{
  "name": "notes_list",
  "description": "List notes with optional filtering",
  "inputSchema": {
    "type": "object",
    "properties": {
      "folder": {"type": "string"},
      "tags": {"type": "array", "items": {"type": "string"}},
      "search": {"type": "string"}
    }
  }
}
```

#### `notes.get`
```json
{
  "name": "notes_get",
  "description": "Get note content by ID",
  "inputSchema": {
    "type": "object",
    "properties": {
      "note_id": {"type": "string"}
    },
    "required": ["note_id"]
  }
}
```

#### `notes.create`
```json
{
  "name": "notes_create",
  "description": "Create a new note",
  "inputSchema": {
    "type": "object",
    "properties": {
      "title": {"type": "string"},
      "content": {"type": "string", "description": "Markdown content"},
      "folder": {"type": "string"},
      "tags": {"type": "array", "items": {"type": "string"}}
    },
    "required": ["title", "content"]
  }
}
```

#### `notes.link_to_task`
```json
{
  "name": "notes_link_to_task",
  "description": "Create or get notes linked to a task",
  "inputSchema": {
    "type": "object",
    "properties": {
      "task_id": {"type": "string"},
      "create_if_missing": {"type": "boolean", "default": true}
    },
    "required": ["task_id"]
  }
}
```

---

### 5. SEARCH MCP SERVER

**Purpose:** Semantic and full-text search across all content

**Status:** ❌ **Needs to be built**

**Target Backends:**
- Meilisearch (full-text search)
- Qdrant (vector/semantic search)
- PostgreSQL (full-text search fallback)

**Required Tools:**

#### `search.query`
```json
{
  "name": "search_query",
  "description": "Search across tasks, projects, notes, calendar",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": {"type": "string"},
      "types": {
        "type": "array",
        "items": {
          "type": "string",
          "enum": ["tasks", "projects", "notes", "events"]
        },
        "description": "Content types to search (default: all)"
      },
      "limit": {"type": "number", "default": 10}
    },
    "required": ["query"]
  }
}
```

**Response:**
```json
{
  "results": [
    {
      "type": "task",
      "id": "task-123",
      "title": "Fix login bug",
      "snippet": "...authentication system bug that we discussed...",
      "relevance_score": 0.95,
      "created": "2025-01-10T09:00:00Z"
    },
    {
      "type": "note",
      "id": "note-456",
      "title": "Meeting Notes: Bug Triage",
      "snippet": "...login bug is highest priority...",
      "relevance_score": 0.87,
      "created": "2025-01-09T14:30:00Z"
    }
  ],
  "total": 12
}
```

---

#### `search.semantic_search`
```json
{
  "name": "search_semantic_search",
  "description": "Semantic search using vector embeddings",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": {"type": "string"},
      "types": {"type": "array", "items": {"type": "string"}},
      "limit": {"type": "number", "default": 10}
    },
    "required": ["query"]
  }
}
```

---

#### `search.suggest_related`
```json
{
  "name": "search_suggest_related",
  "description": "Find related content based on current item",
  "inputSchema": {
    "type": "object",
    "properties": {
      "item_type": {"type": "string", "enum": ["task", "project", "note"]},
      "item_id": {"type": "string"},
      "limit": {"type": "number", "default": 5}
    },
    "required": ["item_type", "item_id"]
  }
}
```

---

### 6. ANALYTICS MCP SERVER

**Purpose:** Track and analyze productivity metrics

**Status:** ❌ **Needs to be built**

**Storage:**
- TimeSeries database (e.g., InfluxDB, TimescaleDB)
- Or PostgreSQL with time-series queries

**Required Tools:**

#### `analytics.track_event`
```json
{
  "name": "analytics_track_event",
  "description": "Track a productivity event",
  "inputSchema": {
    "type": "object",
    "properties": {
      "event_type": {
        "type": "string",
        "enum": [
          "task_created",
          "task_completed",
          "task_rescheduled",
          "calendar_event_created",
          "user_override",
          "interruption",
          "focus_time_start",
          "focus_time_end"
        ]
      },
      "timestamp": {"type": "string", "format": "date-time"},
      "metadata": {
        "type": "object",
        "description": "Event-specific data"
      }
    },
    "required": ["event_type"]
  }
}
```

---

#### `analytics.get_metrics`
```json
{
  "name": "analytics_get_metrics",
  "description": "Get productivity metrics for a time range",
  "inputSchema": {
    "type": "object",
    "properties": {
      "start": {"type": "string", "format": "date-time"},
      "end": {"type": "string", "format": "date-time"},
      "metrics": {
        "type": "array",
        "items": {
          "type": "string",
          "enum": [
            "tasks_completed",
            "tasks_created",
            "schedule_adherence",
            "focus_time_hours",
            "interruptions_count",
            "task_completion_velocity"
          ]
        }
      }
    },
    "required": ["start", "end"]
  }
}
```

**Response:**
```json
{
  "metrics": {
    "tasks_completed": 23,
    "tasks_created": 18,
    "schedule_adherence": 0.85,
    "focus_time_hours": 32.5,
    "interruptions_count": 7,
    "task_completion_velocity": 3.5
  },
  "time_range": {
    "start": "2025-01-01T00:00:00Z",
    "end": "2025-01-07T23:59:59Z"
  }
}
```

---

#### `analytics.get_productivity_report`
```json
{
  "name": "analytics_get_productivity_report",
  "description": "Get comprehensive productivity analysis",
  "inputSchema": {
    "type": "object",
    "properties": {
      "period": {
        "type": "string",
        "enum": ["week", "month", "quarter"]
      },
      "end_date": {
        "type": "string",
        "format": "date",
        "description": "Report end date (default: today)"
      }
    }
  }
}
```

**Response:**
```json
{
  "report": {
    "period": "week",
    "start": "2025-01-08",
    "end": "2025-01-14",
    "summary": {
      "tasks_completed": 23,
      "on_time_completion_rate": 0.87,
      "total_focus_hours": 32.5,
      "productivity_score": 8.2
    },
    "productivity_by_hour": [
      {"hour": 9, "score": 9.1, "tasks_completed": 3},
      {"hour": 10, "score": 9.5, "tasks_completed": 4},
      {"hour": 11, "score": 8.8, "tasks_completed": 3}
    ],
    "productivity_by_day": [
      {"day": "Monday", "score": 8.5, "tasks_completed": 5},
      {"day": "Tuesday", "score": 9.2, "tasks_completed": 6}
    ],
    "insights": [
      "Your most productive hours are 9-11am (score: 9.2)",
      "Friday productivity 20% lower than average",
      "Deep work tasks completed fastest in morning"
    ]
  }
}
```

---

## MCP SERVER IMPLEMENTATION PRIORITIES

### Phase 1: MVP (Immediate)
1. ✅ **Calendar MCP** - Use existing `caldav-mcp`
2. 🔨 **Tasks MCP** - Build for Vikunja (priority)
3. 🔨 **Analytics MCP** - Build minimal version (event tracking only)

### Phase 2: Enhanced (Month 2-3)
4. 🔨 **Projects MCP** - Build for Plane
5. 🔨 **Search MCP** - Build with Meilisearch
6. ⚡ **Calendar MCP** - Extend with `find_free_slots`

### Phase 3: Complete (Month 4+)
7. 🔨 **Notes MCP** - Build for Nextcloud/Outline
8. ⚡ **Analytics MCP** - Add full reporting
9. ⚡ **All MCPs** - Add webhook/subscription support

---

## BUILDING A NEW MCP SERVER

### Technology Choices

**TypeScript/Node.js (Recommended)**
- Official MCP SDK available: `@modelcontextprotocol/sdk`
- Good ecosystem for APIs
- Easy deployment (npm)

**Python (Alternative)**
- MCP SDK: Official Python SDK available
- Better for data science / ML integration
- Good for CalDAV, task CLIs

---

### Example: Building Tasks MCP for Vikunja

**1. Project Setup:**
```bash
mkdir mcp-vikunja-tasks
cd mcp-vikunja-tasks
npm init -y
npm install @modelcontextprotocol/sdk axios
```

**2. Implementation (TypeScript):**
```typescript
// src/index.ts
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import axios from "axios";

const VIKUNJA_URL = process.env.VIKUNJA_URL || "http://localhost:3456";
const VIKUNJA_TOKEN = process.env.VIKUNJA_TOKEN;

const api = axios.create({
  baseURL: `${VIKUNJA_URL}/api/v1`,
  headers: {
    "Authorization": `Bearer ${VIKUNJA_TOKEN}`,
    "Content-Type": "application/json"
  }
});

const server = new Server(
  {
    name: "vikunja-tasks",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

// Define tool: tasks_list
server.setRequestHandler("tools/list", async () => {
  return {
    tools: [
      {
        name: "tasks_list",
        description: "List tasks from Vikunja",
        inputSchema: {
          type: "object",
          properties: {
            status: {
              type: "string",
              enum: ["incomplete", "complete", "all"],
              default: "incomplete"
            },
            project_id: { type: "string" },
            sort: { type: "string" }
          }
        }
      },
      {
        name: "tasks_create",
        description: "Create a new task",
        inputSchema: {
          type: "object",
          properties: {
            title: { type: "string" },
            description: { type: "string" },
            project_id: { type: "string" },
            deadline: { type: "string", format: "date-time" },
            priority: { type: "number" }
          },
          required: ["title"]
        }
      }
      // ... more tools
    ]
  };
});

// Handle tool calls
server.setRequestHandler("tools/call", async (request) => {
  const { name, arguments: args } = request.params;

  if (name === "tasks_list") {
    const response = await api.get("/tasks", {
      params: {
        filter_by: args.status,
        project: args.project_id,
        sort_by: args.sort
      }
    });

    return {
      content: [{
        type: "text",
        text: JSON.stringify(response.data, null, 2)
      }]
    };
  }

  if (name === "tasks_create") {
    const response = await api.post("/tasks", {
      title: args.title,
      description: args.description,
      project_id: args.project_id,
      due_date: args.deadline,
      priority: args.priority
    });

    return {
      content: [{
        type: "text",
        text: JSON.stringify(response.data, null, 2)
      }]
    };
  }

  throw new Error(`Unknown tool: ${name}`);
});

// Start server
const transport = new StdioServerTransport();
await server.connect(transport);
```

**3. Configuration (for Claude Code):**
```json
{
  "mcpServers": {
    "vikunja": {
      "command": "node",
      "args": ["/path/to/mcp-vikunja-tasks/dist/index.js"],
      "env": {
        "VIKUNJA_URL": "http://localhost:3456",
        "VIKUNJA_TOKEN": "your-api-token-here"
      }
    }
  }
}
```

**4. Usage from Skills:**
```python
# In a skill
from mcp_client import MCPClient

mcp = MCPClient()

# List tasks
result = mcp.call("tasks_list", {
    "status": "incomplete",
    "sort": "deadline"
})

tasks = result["content"][0]["text"]  # JSON string
tasks_data = json.loads(tasks)

# Create task
mcp.call("tasks_create", {
    "title": "New task from AI",
    "deadline": "2025-01-20T23:59:59Z",
    "priority": 2
})
```

---

## MCP TESTING & DEBUGGING

### Testing MCP Server Locally

**MCP Inspector Tool:**
```bash
# Install MCP inspector
npm install -g @modelcontextprotocol/inspector

# Test your server
mcp-inspector node dist/index.js
```

Opens web UI to test tools interactively.

### Manual Testing (curl equivalent):
```bash
# MCP uses JSON-RPC over stdio, so manual testing is tricky
# Best to use inspector or write integration tests
```

### Integration Tests:
```python
# test_vikunja_mcp.py
import pytest
from mcp_client import MCPClient

@pytest.fixture
def mcp():
    return MCPClient(server="vikunja")

def test_list_tasks(mcp):
    result = mcp.call("tasks_list", {"status": "incomplete"})
    assert "tasks" in json.loads(result["content"][0]["text"])

def test_create_task(mcp):
    result = mcp.call("tasks_create", {
        "title": "Test Task",
        "priority": 3
    })
    data = json.loads(result["content"][0]["text"])
    assert data["id"] is not None
```

---

## EXISTING MCP SERVERS WE CAN USE

Based on research, here are existing MCP servers relevant to our needs:

### Available Now:
1. ✅ **CalDAV** (calendar) - `caldav-mcp`, `mcp-nextcloud-calendar`
2. ✅ **Filesystem** (for file-based notes) - Official MCP filesystem server
3. ✅ **GitHub** (for code/docs) - Official integration
4. ✅ **Slack** (notifications) - Community server
5. ✅ **Google Drive** (docs) - Community server
6. ✅ **PostgreSQL** (direct DB access) - Community server

### Need to Build:
1. ❌ **Vikunja Tasks**
2. ❌ **Plane Projects**
3. ❌ **Meilisearch**
4. ❌ **Analytics/Metrics**

---

## INTEGRATION PATTERNS

### Pattern 1: Direct MCP Call (from Skill)
```python
# skill calls MCP directly
result = mcp.call("calendar_create_event", {...})
```

### Pattern 2: Abstraction Layer
```python
# skill uses abstraction, abstractions use MCP
from data_access import CalendarService

calendar = CalendarService()  # uses MCP internally
calendar.create_event(...)    # cleaner interface
```

**Recommendation:** Use Pattern 2 for cleaner skill code

---

### Pattern 3: Event-Driven (MCP → Skill)
```python
# MCP server emits events, skills subscribe

@workflow.on_event("calendar.event_extended")
def handle_event_extended(event):
    # Trigger rescheduling
    task_scheduler.reschedule()
```

---

## SECURITY CONSIDERATIONS

### Authentication
- MCP servers authenticate to backends (API tokens, passwords)
- Stored in environment variables (not in code)
- Claude Code manages MCP server credentials

### Authorization
- Skills have full access to MCP servers they connect to
- No fine-grained permissions within MCP (yet)
- Trust boundary: User trusts skills they install

### Data Privacy
- All data stays local (self-hosted backends)
- MCP servers don't send data externally
- LLM calls (if used in skills) are separate concern

---

## NEXT STEPS

### Immediate Actions:
1. ✅ **Install existing CalDAV MCP server**
   ```bash
   npm install -g @dominik1001/caldav-mcp
   ```

2. 🔨 **Build Vikunja Tasks MCP server**
   - Start with TypeScript/Node.js
   - Implement core tools: list, create, update, complete
   - Test with MCP Inspector
   - Publish to npm

3. 🔨 **Build minimal Analytics MCP server**
   - Start with simple event tracking
   - Store in PostgreSQL
   - Basic metrics retrieval

### Week 2-3:
4. 🔨 **Build Plane Projects MCP server**
5. 🔨 **Build Meilisearch MCP server**

### Documentation:
6. 📝 **Write MCP integration guide for skill developers**
7. 📝 **Document available MCP servers and their capabilities**

---

## RESOURCES

**Official:**
- MCP Documentation: https://modelcontextprotocol.io
- MCP GitHub: https://github.com/modelcontextprotocol
- MCP SDK (TypeScript): `@modelcontextprotocol/sdk`
- MCP SDK (Python): `mcp` package

**Community:**
- Awesome MCP Servers: https://mcpservers.org
- Example servers: https://github.com/modelcontextprotocol/servers

**For This Project:**
- CalDAV MCP: https://github.com/dominik1001/caldav-mcp
- Vikunja API Docs: https://vikunja.io/docs/api-documentation/
- Plane API Docs: https://plane.so/developers/api-reference
