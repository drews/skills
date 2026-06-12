---
name: deadline-guardian
description: Proactively detect at-risk project deadlines by comparing required work vs available time. Warns users days or weeks in advance with actionable recommendations. Use for daily deadline checks or when user asks about project status.
---

# Deadline Guardian

## Purpose
Detect projects and tasks at risk of missing deadlines early enough for the user to take corrective action.

## Prerequisites
- Tasks MCP server (to get tasks and project breakdowns)
- Calendar MCP server (to calculate available working time)
- Optional: Projects MCP server (for richer project data)

## When to Use
- Daily automated morning check (recommended: 8am)
- User asks "will I meet my deadline?"
- User requests "project status"
- User asks "what's at risk?"
- User says "check my deadlines"
- Weekly planning sessions

## Instructions

### Step 1: Identify Items with Deadlines

Fetch from Tasks/Projects MCP:
- All projects with deadline dates set AND incomplete tasks
- All individual tasks with deadline dates set AND status = incomplete

Group tasks by project (if they belong to projects).

### Step 2: For Each Deadline, Calculate Risk

**A. Calculate Required Time**
```
required_hours = sum(all_incomplete_task_durations)
```

If task durations are missing, use intelligent estimates:
- Small task: 1 hour
- Medium task: 3 hours
- Large task: 8 hours
- (Better: ask user to provide estimates)

**B. Calculate Available Time**
```
Step 1: Calculate work days
  days_until_deadline = deadline_date - today
  work_days = count_weekdays(today, deadline_date)
  # Excludes weekends by default

Step 2: Calculate available hours per day
  hours_per_workday = 8  # Configurable, ask user

Step 3: Get calendar commitments
  Use Calendar MCP: list_events(start=today, end=deadline)
  meeting_hours = sum(duration of all meetings)

Step 4: Calculate net available time
  gross_hours = work_days * hours_per_workday
  available_hours = gross_hours - meeting_hours
```

**C. Calculate Risk Score**
```
Step 1: Add safety buffer (account for unknowns)
  buffer_percentage = 0.20  # 20% buffer for unexpected delays
  buffer_needed = required_hours * buffer_percentage

Step 2: Calculate risk score
  risk_score = (required_hours + buffer_needed) / available_hours

Step 3: Classify risk level
  risk_score ≤ 0.7:  ✅ On Track (using <70% of capacity)
  risk_score ≤ 0.9:  ⚠️  At Risk (using 70-90% capacity)
  risk_score ≤ 1.2:  🔴 Critical (using 90-120% capacity)
  risk_score > 1.2:   ❌ Impossible (need >120% capacity)
```

### Step 3: Generate Warnings

For projects/tasks that are "At Risk" or worse, create warning message:

**Warning Template:**
```
{emoji} {RISK_LEVEL}: {Project/Task Name}
Status: {At Risk / Critical / Impossible}
Deadline: {date} ({days_remaining} days away)

Analysis:
- Required work remaining: {required_hours}h
- Available time (after meetings): {available_hours}h
- Capacity usage: {percentage}% ({risk_level})
- Buffer needed for unknowns: {buffer_hours}h (20%)

{explanation_of_why_at_risk}
```

**Recommendations:**

Generate 2-3 actionable suggestions based on risk level:

If risk_score > 1.2 (Impossible):
1. "Extend deadline by {X} days" (calculate needed days)
2. "Cut scope: Remove {Y} hours of optional work"
3. "Get help: Delegate {Z} tasks to team members"

If risk_score > 0.9 (Critical or At Risk):
1. "Reprioritize: Defer {X} lower-priority tasks"
2. "Increase focus: Block more calendar time for this project"
3. "Reduce scope: Identify optional features to cut"

### Step 4: Prioritize Warnings

Show warnings in this order:
1. **Impossible** items first (immediate action needed)
2. **Critical** items second (action needed this week)
3. **At Risk** items third (monitor closely)
4. **On Track** items only if user explicitly asks

### Step 5: Daily Briefing (Automated Use)

For daily morning check, format as briefing:

```
📊 Deadline Status Report - {Today's Date}

Critical Items (Action Required):
{list critical and impossible items}

At Risk (Monitor Closely):
{list at-risk items}

On Track:
{count of on-track items} projects on schedule ✅

Recommendations for Today:
{top 3 actions to mitigate risks}
```

## Calculation Examples

### Example 1: Healthy Project
```
Project: Website Redesign
Deadline: Jan 31 (15 days away)
Required work: 40 hours remaining
Work days available: 11 (excluding weekends)
Meetings in next 15 days: 12 hours
Available hours: (11 days * 8 hr/day) - 12hr = 88 - 12 = 76 hours
Buffer needed: 40 * 0.20 = 8 hours
Risk score: (40 + 8) / 76 = 48/76 = 0.63

Status: ✅ On Track (63% capacity)
Message: "Website Redesign is on track. You're using only 63% of available capacity."
```

### Example 2: At-Risk Project
```
Project: Q4 Report
Deadline: Jan 20 (8 days away)
Required work: 30 hours remaining
Work days: 6
Meetings: 18 hours
Available: (6 * 8) - 18 = 30 hours
Buffer: 30 * 0.20 = 6 hours
Risk score: (30 + 6) / 30 = 36/30 = 1.20

Status: 🔴 Critical (120% capacity needed)

Warning:
"🔴 CRITICAL: Q4 Report deadline at risk!

You need 30 hours of work but only have 30 hours available before Jan 20.
With a 20% buffer for unknowns, you're at 120% capacity.

Recommendations:
1. Extend deadline to Jan 24 (+3 work days, reduces risk to 85%)
2. Cut optional sections to reduce scope by 6 hours
3. Get help: Delegate data collection task (8hr) to colleague"
```

### Example 3: Impossible Deadline
```
Task: Build new feature
Deadline: Tomorrow
Required: 16 hours
Work days: 1
Meetings: 3 hours
Available: (1 * 8) - 3 = 5 hours
Buffer: 16 * 0.20 = 3.2 hours
Risk score: (16 + 3.2) / 5 = 19.2/5 = 3.84

Status: ❌ Impossible (384% capacity)

Warning:
"❌ IMPOSSIBLE: Build new feature deadline cannot be met!

You need 16 hours but only have 5 hours before tomorrow's deadline.
Even without a safety buffer, this requires 320% of your capacity.

Actions:
1. EXTEND deadline to Jan 25 (5 days, brings risk to 82%)
2. REDUCE scope drastically (cut 12 hours of work)
3. GET IMMEDIATE HELP (split work with 2 teammates)

This deadline is not achievable solo. Please escalate."
```

## Daily Check Workflow

**Automated Morning Routine:**

```
Every day at 8am:

1. Run deadline-guardian
2. Check all projects/tasks with deadlines in next 30 days
3. Identify any Critical or Impossible items
4. Format as morning briefing
5. Present to user (or send notification)

Morning Briefing includes:
- Critical items needing immediate action
- Summary of at-risk items
- Count of on-track items (for confidence)
- Top 3 recommended actions for the day
```

## Advanced: Velocity-Based Forecasting

If user has historical completion data:

```
Calculate actual velocity:
  completed_hours_last_week / estimated_hours_last_week = velocity_factor

Example:
  - Last week estimated 40 hours of work
  - Actually completed work that was estimated at 32 hours
  - Velocity: 32/40 = 0.8 (user works at 80% of estimates)

Adjust risk calculation:
  adjusted_required = required_hours / velocity_factor
  # If velocity = 0.8, then required hours increase by 25%

Use adjusted_required in risk score calculation
```

This makes predictions more accurate for users who consistently under/over-estimate.

## Error Handling

**No calendar access:**
```
Fall back to simplified calculation:
available_hours = work_days * 8
(Skip meeting-hours deduction, but warn user this is optimistic)
```

**Tasks missing duration estimates:**
```
Warn user:
"⚠️ Can't accurately assess risk - {X} tasks are missing time estimates.
Please add estimates to: {list_tasks}"

Proceed with conservative estimates (e.g., 3hrs per task)
```

**Past deadlines:**
```
Alert immediately:
"🚨 OVERDUE: {task/project} deadline was {date} ({days_ago} days ago)

This is already late. Please update the deadline or mark as complete."
```

**Holidays/Time off:**
```
If user has blocked calendar with PTO/vacation:
  Exclude those days from work_days calculation
  Note in warning: "Accounting for {X} days PTO in this period"
```

## Integration with Other Skills

**Compose with priority-assistant:**
```
1. Run deadline-guardian to find at-risk items
2. Run priority-assistant to rank tasks by urgency
3. Result: Focus on high-priority tasks in at-risk projects
```

**Compose with task-scheduler:**
```
1. deadline-guardian identifies critical project
2. task-scheduler blocks calendar time for critical tasks
3. Result: Automatically allocate time to at-risk work
```

## Notes

- Run daily for proactive monitoring (prevention > reaction)
- The 20% buffer is critical - most projects hit unexpected delays
- For teams: check capacity across all members, not just user
- Store risk score history to track if situation improving/worsening
- Consider user's historical velocity if data available
- Be proactive but not alarmist - focus on actionable warnings
- Always provide 2-3 concrete remediation options
