---
name: priority-assistant
description: Calculate intelligent priority scores for tasks using deadline, importance, effort, and dependencies. Use when user asks "what should I work on?" or "prioritize my tasks" or wants to know what's most important.
---

# Priority Assistant

## Purpose
Help users understand which tasks deserve attention right now using multi-factor scoring.

## Prerequisites
- Tasks MCP server configured (to fetch task list)
- Optional: Calendar MCP (to check available time for context)

## When to Use
- User asks "what should I work on?"
- User says "prioritize my tasks"
- User wants to know "what's most important?"
- User requests task ranking or sorting
- Morning planning or daily standup context

## Instructions

### Step 1: Fetch Active Tasks
Use Tasks MCP to get all incomplete tasks:
- Filter: status = "incomplete"
- Include fields: title, deadline, priority, estimated_duration, dependencies, tags, created_date

### Step 2: Calculate Priority Score

For each task, compute score using these factors:

**Base score:** 100 points

**Factor 1: Deadline Proximity (0-40 points)**
```
If no deadline: 0 points
If has deadline:
  days_until = deadline - today
  score = 40 / (days_until + 1)

Examples:
  - Due today: 40 / 1 = 40 points
  - Due tomorrow: 40 / 2 = 20 points
  - Due in week: 40 / 8 = 5 points
```

**Factor 2: User Priority (0-30 points)**
```
P1 (highest): 30 points
P2: 20 points
P3: 10 points
P4: 5 points
None/unset: 0 points
```

**Factor 3: Dependencies (0-20 points)**
```
If this task blocks other tasks: 20 points
If no dependencies: 0 points

Rationale: Unblock others first
```

**Factor 4: Quick Win Bonus (0-15 points)**
```
Duration < 30min: 15 points
Duration < 1hr: 10 points
Duration < 2hr: 5 points
Duration >= 2hr: 0 points

Rationale: Small wins build momentum
```

**Factor 5: Age in Backlog (0-10 points)**
```
weeks_old = (today - created_date) / 7
score = min(weeks_old * 2, 10)

Examples:
  - 1 week old: 2 points
  - 3 weeks old: 6 points
  - 5+ weeks old: 10 points (capped)

Rationale: Don't let old tasks languish
```

**Total Score = sum of all factors (max 215 points)**

### Step 3: Rank Tasks
Sort all tasks by computed score, highest first.

### Step 4: Present Results
Show user the top 3-5 tasks with:
1. Task title
2. Total score
3. **Explanation** of why it scored high (break down the factors)

Format example:
```
Your top priorities right now:

1. [Score: 165] Fix production login bug
   Why: P1 priority (30 pts) + deadline today (40 pts) + blocks 3 tasks (20 pts)

2. [Score: 142] Complete quarterly report
   Why: Deadline Friday (26 pts) + P2 priority (20 pts) + 3 weeks in backlog (6 pts)

3. [Score: 135] Review Sarah's PR #234
   Why: Quick win <30min (15 pts) + P1 priority (30 pts) + blocks deployment (20 pts)
```

### Step 5: Recommend Action
End with clear recommendation:
"I recommend starting with '{top_task_title}' because {brief_reasoning}"

## Context-Aware Filtering (Optional)

If user specifies context (time available, location, energy):

**Time constraint:**
- "I have 30 minutes" → Filter to tasks with duration ≤ 30min, then prioritize

**Location:**
- "I'm at coffee shop" → Filter out tasks requiring office (if tagged)

**Energy level:**
- "I'm tired" → Filter out cognitively demanding tasks (if tagged)

## Examples

### Example 1: Morning Planning
```
User: "What should I work on today?"

Steps:
1. Fetch 18 incomplete tasks
2. Calculate scores for all 18
3. Rank by score
4. Show top 5

Output:
"Your top 5 priorities today:
1. [178] Fix API timeout bug - P1, due today, blocks release
2. [156] Review design mockups - Due tomorrow, quick win (30min)
3. [142] Update documentation - P2, due Friday, 2 weeks old
4. [128] Respond to client email - P2, due today
5. [115] Plan sprint tasks - Due Thursday, blocks team

I recommend starting with 'Fix API timeout bug' because it's blocking your release and due today."
```

### Example 2: Quick Win Hunting
```
User: "I have 30 minutes before my meeting, what can I knock out?"

Steps:
1. Fetch incomplete tasks
2. Filter to duration ≤ 30min
3. Calculate scores
4. Show top matches

Output:
"You can tackle these in 30 minutes:
1. [145] Review PR #234 (15min) - P1, blocks deployment
2. [130] Respond to client (20min) - P2, due today
3. [118] Update Jira tickets (25min) - Quick admin task

Go with 'Review PR #234' - high priority, unblocks the team, and only takes 15 minutes."
```

### Example 3: Context Filtering
```
User: "I'm working from home today, what should I focus on?"

Steps:
1. Fetch tasks
2. Filter out tasks tagged "requires-office"
3. Prioritize remaining
4. Show top 5

Output:
"Remote-friendly priorities:
1. [165] Write blog post - P2, due Friday, deep work task
2. [142] Code review - P1, due tomorrow
3. [128] Design API spec - P2, focused work
..."
```

## Error Handling

**No incomplete tasks:**
"Great news! You have no incomplete tasks. Time to take a break or plan new work."

**All tasks same score:**
Use secondary tiebreaker: age (older tasks first)

**Missing task data:**
Use sensible defaults:
- No priority → treat as P3
- No deadline → 0 deadline points
- No duration → estimate 1 hour
- No created date → assume today (0 age points)

**Tasks MCP unavailable:**
"I can't access your task list right now. Please check that the Tasks MCP server is configured and running."

## Notes

- Scoring algorithm is intentionally transparent and explainable
- Weights are tunable: adjust point values in algorithm to match user preferences
- Re-run this daily as deadlines approach (scores change over time)
- For recurring use, consider storing user's preferred factor weights
- This Skill composes well with task-scheduler: prioritize first, then schedule top items
