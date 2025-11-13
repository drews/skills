---
name: meeting-optimizer
description: Find optimal meeting times across multiple attendees' calendars, minimize schedule disruption, and suggest meeting efficiency improvements. Use when scheduling meetings or user asks to "find time for meeting".
---

# Meeting Optimizer

## Purpose
Help users find the best times for meetings that minimize disruption to focus time and maximize attendee convenience.

## Prerequisites
- Calendar MCP server with multi-user calendar access

## When to Use
- User wants to "schedule a meeting with {people}"
- User asks "when can we meet?"
- User needs to "find time for {meeting}"
- User asks "what's the best time for this meeting?"

## Instructions

### Step 1: Gather Meeting Requirements

Ask for:
- **Attendees:** Who needs to be there? (get email/calendar IDs)
- **Duration:** How long? (default: 30min)
- **Timeframe:** When should this happen? (this week, next week, ASAP)
- **Constraints:** Any preferences? (morning only, avoid Mondays, etc.)

### Step 2: Fetch All Attendees' Calendars

Use Calendar MCP to get free/busy for each attendee:
- Time range: requested timeframe
- Include: all scheduled events for each person

### Step 3: Find Common Free Slots

Identify time blocks where ALL attendees are free:
- Must be continuous block of required duration
- Must fall within work hours (default: 9am-5pm in all timezones)
- Exclude lunch hours (12pm-1pm) by default

### Step 4: Score Each Slot

**Scoring factors (higher = better):**

```
Base: 100 points

Time of day preferences:
  Mid-morning (10am-11am): +20 (post-coffee, pre-lunch energy)
  Afternoon (2pm-3pm): +15 (post-lunch settled)
  Early morning (9am-10am): +10
  Late morning (11am-12pm): +5
  Late afternoon (3pm-5pm): +5
  End of day (after 4pm): -10 (people want to wrap up)

Calendar fragmentation penalty:
  If slot creates <1hr gaps before/after in any attendee's day: -15
  (Don't fragment focus time)

Buffer availability:
  If 30min buffer before AND after (no meetings): +10
  (Travel time, prep time, bio breaks)

Timezone fairness:
  If slot is during poor hours for any attendee (before 9am or after 5pm their time): -20

Work day preference:
  Tuesday-Thursday: +10 (people prefer mid-week meetings)
  Monday: +0
  Friday: -5 (people prefer focus time end of week)

Urgency bonus:
  If meeting is urgent/same-day: prefer earlier slots (+time_until_slot)
```

### Step 5: Rank and Present Options

Sort slots by score, show top 3-5 options:

```
Best meeting times for {attendees}:

Option 1 (Score: 145): Tuesday 10:30am-11:00am
  ✓ Mid-morning when everyone is fresh
  ✓ No calendar conflicts
  ✓ 30min buffer after for all attendees
  ✓ Mid-week (Tuesday)

Option 2 (Score: 135): Wednesday 2:00pm-2:30pm
  ✓ Post-lunch, everyone settled
  ✓ No conflicts
  ✓ Good for collaborative discussion

Option 3 (Score: 125): Thursday 9:00am-9:30am
  ⚠️ Early for some attendees
  ✓ Still available if options 1-2 don't work

Recommendation: Tuesday 10:30am (highest score, least disruptive)
```

### Step 6: Create Meeting (If User Confirms)

Use Calendar MCP to:
1. Create calendar event for all attendees
2. Add meeting title, description
3. Send calendar invites
4. Set reminder (15min before)

## Advanced Features

### Automatic Prep Task Creation

For important meetings, create prep task:
```
If meeting >30min AND involves external people:
  Create task: "Prepare for {meeting_name}"
  Schedule: Day before meeting, 30min duration
  Link to meeting in description
```

### Meeting Efficiency Suggestions

Analyze meeting and suggest improvements:

```
If meeting >1hr:
  "Consider breaking this into 2 shorter meetings to maintain energy"

If >8 attendees:
  "Large meeting - consider:
   - Async update instead?
   - Smaller working group + share notes?
   - Clear agenda with time boxes?"

If recurring weekly 1hr:
  "Consider reducing to 30min or bi-weekly to reclaim time"
```

### Conflict Resolution

If attendee has conflict:
```
"Sarah has a conflict at your preferred time (10:30am meeting).

Options:
1. Schedule without Sarah (she's optional)
2. Move to Option 2 (2pm when everyone's free)
3. Ask Sarah if her 10:30am meeting can move?"
```

## Examples

### Example 1: Simple Team Meeting
```
User: "Schedule 30min meeting with Alice, Bob, and Carol for this week"

Steps:
1. Get calendars for Alice, Bob, Carol
2. Find common free 30min slots this week (Mon-Fri)
3. Score each slot
4. Present top 3

Output:
"Best times this week:
1. Wednesday 10am - Score 145 (mid-morning, mid-week, clean calendars)
2. Thursday 2:30pm - Score 135 (post-lunch, all available)
3. Friday 10am - Score 120 (Friday less ideal but works)

Create meeting for Wednesday 10am?"
```

### Example 2: Cross-Timezone Meeting
```
User: "Find time for call with Tokyo team (9 people)"

Constraints:
- Tokyo is +16 hours ahead
- Need to find overlap in reasonable hours

Analysis:
- Tokyo work hours: 9am-6pm JST = 5pm-2am local (previous day)
- Only viable overlap: Local 5pm-6pm = Tokyo 9am-10am next day
- This is end-of-day local but start-of-day Tokyo

Options:
1. 5:30pm local / 9:30am Tokyo tomorrow
2. Consider async update instead (large group, timezone challenges)

Recommendation:
"Given the timezone difference, I suggest:
- Option A: 5:30pm local (Tokyo 9:30am) - but this is end-of-day for you
- Option B: Async Loom video + Q&A in Slack (more flexible)

Which do you prefer?"
```

### Example 3: No Common Slots
```
User: "Meet with David and Elena today"

Result: No common free time found

Output:
"No common free slots found for all three of you today.

Options:
1. Make it optional - meet with just David at 2pm (Elena can review notes)
2. Extend to tomorrow - Wednesday 10am works for all
3. Move Elena's 3pm meeting - then 3pm would work

What would you like to do?"
```

## Error Handling

**Can't access attendee calendars:**
```
"I can access your calendar but not {attendee}'s.
Should I:
1. Suggest times based on your availability only?
2. Wait for {attendee} to share calendar access?"
```

**All good slots create fragmentation:**
```
Accept fragmentation if necessary, but warn:
"All available times create short gaps in someone's schedule.
Best option (least disruptive): Tuesday 11:30am
  ⚠️ Creates 45min gap before lunch for Bob

Still proceed?"
```

**Timezone confusion:**
```
Always confirm timezones:
"Creating meeting for Tuesday 10am Pacific (1pm Eastern, 6pm London).
All attendees see this in their local time. Correct?"
```

## Notes

- Prioritize 30min meetings over 1hr (Parkinson's Law - work expands to fill time)
- Avoid back-to-back meetings (no bio breaks, context switching cost)
- Mid-week > Monday/Friday for scheduling
- Respect timezone fairness in global teams
- Consider async alternatives for large meetings (>8 people)
