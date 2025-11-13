---
name: task-scheduler
description: Automatically schedule tasks into CalDAV calendar by finding optimal time slots based on priority, deadline, and availability. Use when user wants to add tasks to calendar, schedule work, or optimize their schedule.
---

# Task Scheduler

## Purpose
Help users automatically schedule tasks into their calendar using AI to find the best available time slots.

## Prerequisites
- Calendar MCP server configured (CalDAV access required)
- Optional: Tasks MCP server for richer task metadata

## When to Use
- User says "schedule [task name]"
- User asks "when should I work on [task]?"
- User wants to "add [task] to calendar"
- User requests "find time for [task]"
- User asks to "optimize my schedule"

## Instructions

### Step 1: Gather Task Requirements

Ask user for any missing information:

**Required:**
- Task title
- Estimated duration (in hours or minutes)

**Recommended:**
- Deadline (when must this be done by?)
- Priority: 1-4, where 1 = highest (default: 3)

**Optional:**
- Constraints (e.g., "must be morning", "only Fridays", "needs uninterrupted time")
- Dependencies (other tasks that must finish first)
- Minimum energy level (focus work vs admin)

### Step 2: Fetch Calendar Data

Use Calendar MCP to get existing events:

**Time range to check:**
- Start: today
- End: deadline (if specified) OR 7 days from now
- Include: all calendars user wants checked

**What to fetch:**
- All scheduled events (meetings, existing tasks, appointments)
- Time zone information
- Work hours preferences (if available)

### Step 3: Find Candidate Time Slots

Look for free blocks that match:

**Must criteria:**
- Duration matches task estimate (continuous block)
- No conflicts with existing calendar events
- Falls within work hours (default: Mon-Fri 9am-5pm, but ask user)

**Should criteria (ideal but not required):**
- Has 30min buffer before/after (for tasks >2hr)
- Doesn't fragment the day excessively
- Grouped with similar task types (batch similar work)

### Step 4: Score Each Candidate Slot

Use this scoring formula (higher = better):

**Base score:** 100 points

**Time of Day Bonus:**
```
Morning (9am-12pm):  +20 points  (best for deep/focus work)
Afternoon (1pm-4pm): +10 points  (good for collaborative work)
Late day (4pm-5pm):  +5 points   (admin/wrap-up tasks)
```

**Deadline Urgency:**
```
days_until_deadline = deadline - slot_date
urgency_bonus = max(0, 30 - (days_until_deadline * 2))

Examples:
- Slot is day before deadline: 30 - (1 * 2) = 28 points
- Slot is 3 days before: 30 - (3 * 2) = 24 points
- Slot is 2 weeks before: 30 - (14 * 2) = 2 points
- Slot is after deadline: 0 points (invalid, don't schedule)
```

**Priority Bonus:**
```
P1 (highest): +30 points
P2: +15 points
P3: +5 points
P4: +0 points
```

**Buffer Bonus:**
```
If slot has 30min before AND after with no meetings: +10 points
If slot has buffer on one side: +5 points
If slot is back-to-back: +0 points
```

**Day Fragmentation Penalty:**
```
If scheduling this task creates isolated 30-60min gaps: -10 points
(We want to minimize calendar fragmentation)
```

**Total slot score = sum of all factors**

### Step 5: Select Best Slot

1. Sort all candidate slots by score (highest first)
2. Pick the top-scoring slot
3. Verify it meets all "must criteria"
4. If multiple slots tie, prefer the earliest one

### Step 6: Create Calendar Event

Use Calendar MCP `calendar_create_event`:

**Event details:**
- Title: `[Task] {task_title}` (prefix helps distinguish tasks from meetings)
- Start time: slot start
- End time: slot start + duration
- Description: Include priority, deadline, and any notes
- Reminder: Set 30min before (if task >2hr duration)
- Color/category: Tag as "scheduled-task" (if calendar supports)

### Step 7: Explain Your Decision

**Always tell the user WHY you chose that specific slot.**

Template:
```
"I scheduled '{task_title}' for {day} {start_time}-{end_time} because:
- {reason_1} (e.g., Morning is your most productive time for focus work)
- {reason_2} (e.g., It's 3 days before your deadline, giving you a buffer)
- {reason_3} (e.g., You have no meetings nearby, ensuring uninterrupted time)
- {reason_4} (e.g., This is a Priority 2 task, so it gets a premium slot)

Score: {final_score} points"
```

## Advanced: Multiple Task Scheduling

If user wants to schedule multiple tasks:

1. Use priority-assistant to rank them first
2. Schedule highest-priority task first (using steps above)
3. After placing it, re-fetch calendar (now includes the task you just scheduled)
4. Schedule next task, and so on
5. This greedy approach ensures highest-priority items get best slots

## Examples

### Example 1: Simple Single Task
```
User: "Schedule 'Write blog post' for 3 hours, due Friday"

Steps:
1. Gather info: title=Write blog post, duration=3hr, deadline=Friday, priority=3 (default)
2. Fetch calendar Mon-Fri
3. Find free 3hr blocks
4. Score each:
   - Tuesday 9am-12pm: 100 + 20 (morning) + 24 (3 days before deadline) + 5 (P3) + 10 (buffer) = 159
   - Wednesday 2pm-5pm: 100 + 10 (afternoon) + 22 (2 days before) + 5 (P3) = 137
   - Thursday 9am-12pm: 100 + 20 (morning) + 20 (1 day before) + 5 (P3) + 10 (buffer) = 155
5. Select Tuesday 9am-12pm (highest score: 159)
6. Create calendar event
7. Explain: "Scheduled Tuesday 9am-12pm because morning is best for writing (focused work), you have 3 days buffer before Friday deadline, and there are no meetings nearby"
```

### Example 2: Urgent High-Priority Task
```
User: "P1 task: Fix production bug, 2 hours, ASAP!"

Steps:
1. Gather: title=Fix production bug, duration=2hr, deadline=ASAP (interpret as today), priority=1
2. Fetch calendar for rest of today
3. Find slots:
   - Today 2pm-4pm: 100 + 10 (afternoon) + 28 (same day urgency) + 30 (P1) + 5 (one-side buffer) = 173
   - Today 4pm-6pm: 100 + 5 (late day) + 28 + 30 = 163
4. Select 2pm-4pm (highest score: 173)
5. Create event
6. Explain: "Scheduled for today 2pm-4pm because this is P1 urgent, and it's your next available 2-hour block. I prioritized urgency over time-of-day optimization"
```

### Example 3: Constrained Scheduling
```
User: "Schedule 'Team presentation prep' for 4 hours, must be Thursday afternoon, due Friday"

Steps:
1. Gather: duration=4hr, constraint="Thursday afternoon only", deadline=Friday, priority=3
2. Fetch calendar, but only look at Thursday 1pm-5pm
3. Find 4hr block: Thursday 1pm-5pm
4. Score: 100 + 10 (afternoon) + 20 (day before deadline) + 5 (P3) = 135
5. Create event
6. Explain: "Scheduled Thursday 1pm-5pm as requested. This is the only 4-hour afternoon block available on Thursday, and it's one day before your Friday deadline"
```

### Example 4: No Available Slots (Overbooked)
```
User: "Schedule 'Write documentation' for 6 hours, due tomorrow"

Steps:
1. Duration=6hr, deadline=tomorrow
2. Fetch calendar for today and tomorrow
3. Find no 6hr continuous blocks
4. (Don't schedule - explain problem)

Output:
"I can't find a continuous 6-hour block before tomorrow's deadline. Here are your options:

Option 1: Split the task
- Schedule 3 hours today 2pm-5pm
- Schedule 3 hours tomorrow 9am-12pm

Option 2: Extend deadline
- If deadline moves to Friday, I can schedule 6 hours Thursday 9am-3pm

Option 3: Work outside normal hours
- Schedule today 8am-2pm (starts before typical 9am work hours)

Which would you prefer?"
```

## Error Handling

**No available slots:**
1. Suggest extending deadline OR
2. Suggest shortening task duration OR
3. Suggest working outside normal hours OR
4. Suggest removing/rescheduling lower-priority items

**Deadline already passed:**
- Alert user clearly: "This deadline has already passed"
- Ask: "Would you like to set a new deadline?"

**Calendar conflicts:**
- Never double-book
- Find next best available slot
- Ask user: "Would you like me to reschedule your existing {conflicting_event}?"

**Work hours undefined:**
- Ask user: "What are your typical work hours? (default: Mon-Fri 9am-5pm)"
- Store preference for future use

**Task duration exceeds available time per day:**
- Offer to split across multiple days
- Show the proposed multi-day schedule

## Notes

- Always respect existing calendar commitments (never overwrite)
- Default work hours: Mon-Fri 9am-5pm (but ask user to confirm)
- Consider user's timezone (from calendar settings or ask)
- For recurring tasks, ask: "Do you want this scheduled once or as a recurring series?"
- This Skill pairs well with priority-assistant: prioritize tasks first, then schedule the top ones
- For complex multi-task optimization (10+ tasks), may need helper script with constraint solver
