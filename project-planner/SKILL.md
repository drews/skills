---
name: project-planner
description: AI-assisted project planning that breaks down high-level projects into actionable tasks with estimates and dependencies. Use when user wants to plan a new project, needs task breakdown, or asks "how should I approach this?"
---

# Project Planner

## Purpose
Help users decompose complex projects into structured, actionable task lists with realistic estimates and clear dependencies.

## Prerequisites
- Projects MCP server (to create/store projects and tasks)
- Tasks MCP server (for task creation and management)
- Optional: Access to similar past projects for pattern matching

## When to Use
- User says "I need to plan {project}"
- User asks "how do I approach {project}?"
- User wants "task breakdown for {project}"
- User requests "project timeline estimate"
- User says "help me organize {complex work}"
- User asks "what tasks are involved in {goal}?"

## Instructions

### Step 1: Gather Project Information

Ask user for project details:

**Required:**
- Project name/title
- High-level description or goal
- Deadline (if known)

**Helpful:**
- Success criteria (what defines "done"?)
- Constraints (budget, team size, technology stack)
- Known requirements or deliverables
- Existing similar projects to reference

### Step 2: Analyze and Break Down Project

**Think through the project structure:**

1. **Identify major phases** (typically 3-7 phases)
   - Example for software: Planning → Design → Development → Testing → Deployment
   - Example for content: Research → Outline → Draft → Edit → Publish

2. **For each phase, list key tasks**
   - Be specific and actionable
   - Tasks should be completable in 0.5-8 hours (break larger work down)
   - Use verb-first naming: "Write...", "Review...", "Design...", "Deploy..."

3. **Estimate duration for each task**
   - Consider user's experience level
   - Add 20% buffer for unknowns
   - Round to realistic increments (0.5hr, 1hr, 2hr, 4hr, 8hr)

4. **Identify dependencies**
   - What must be done before what?
   - Mark tasks that block other work
   - Note tasks that can run in parallel

5. **Assign priority/criticality**
   - Critical path tasks (blockers) → P1
   - Important but not blocking → P2
   - Nice-to-have features → P3
   - Optional enhancements → P4

### Step 3: Estimate Total Time and Timeline

**Calculate project duration:**

```
Total effort = sum(all_task_durations)

If sequential (solo work):
  project_duration_days = total_effort / hours_per_day

If parallel (team work):
  project_duration_days = max(critical_path_duration)

Add 20% contingency for unknowns
```

**Compare to deadline:**
- Is deadline realistic?
- If not, what needs to change? (scope, team size, deadline)

### Step 4: Create Project Structure

Use Projects and Tasks MCP to create:

**Project record:**
- Name, description, deadline
- Overall status: "planning"
- Metadata: total estimated hours, phase count

**Phase groupings** (if MCP supports milestones/phases)
- Example: Milestone "Design Complete" after design tasks

**Individual tasks:**
For each task, create with:
- Title (action-oriented)
- Description (what needs to be done, why)
- Estimated duration
- Priority (P1-P4)
- Dependencies (task IDs that must complete first)
- Phase/milestone assignment
- Initial status: "not started"

### Step 5: Present Plan to User

Show structured overview:

```
Project: {Name}
Timeline: {estimated_duration} ({total_hours}h of work)
Deadline: {deadline_date} {feasibility_note}

Phase 1: {Phase Name} ({total_hours}h)
├─ [P1] Task 1 (2h) - {brief description}
├─ [P2] Task 2 (4h) - {brief description} [depends on Task 1]
└─ [P2] Task 3 (3h) - {brief description}

Phase 2: {Phase Name} ({total_hours}h)
├─ [P1] Task 4 (1h) - {brief description}
├─ [P2] Task 5 (6h) - {brief description} [depends on Task 4]
└─ [P3] Task 6 (2h) - {brief description} [optional]

...

Critical Path: Task 1 → Task 2 → Task 4 → Task 5 ({sum}h)
First Task to Start: {task_name}
Recommended Team Size: {N} people (to meet deadline)
```

### Step 6: Identify Risks and Recommendations

Call out:
- **Risk areas:** What could cause delays?
- **Unknowns:** What needs clarification?
- **Dependencies:** External blockers?
- **Resource needs:** Special skills or tools required?

Provide recommendations:
- "Start with {task} to de-risk the project"
- "Consider adding {X} to the team for {phase}"
- "Budget extra time for {uncertain_area}"

### Step 7: Offer Next Actions

Ask user:
1. "Does this breakdown look right, or should I adjust?"
2. "Should I create these tasks in your task manager?"
3. "Want me to schedule the first few tasks on your calendar?"

## Examples

### Example 1: Software Feature
```
User: "Help me plan building a user authentication system"

Analysis:
- This is a software development project
- Typical phases: Planning, Design, Implementation, Testing, Deployment
- Security-critical, needs careful design

Project: User Authentication System
Estimated: 32-40 hours (4-5 days solo work)
Recommended: 2 developers, 2-3 weeks with testing

Phase 1: Planning & Design (8h)
├─ [P1] Research authentication best practices (2h)
├─ [P1] Design database schema for users/sessions (2h) [depends on research]
├─ [P1] Design API endpoints (POST /login, /logout, /register) (2h)
└─ [P2] Document security requirements (2h)

Phase 2: Backend Implementation (16h)
├─ [P1] Set up database tables (1h) [depends on schema design]
├─ [P1] Implement password hashing (bcrypt) (2h)
├─ [P1] Build registration endpoint (3h) [depends on db + hashing]
├─ [P1] Build login endpoint with JWT tokens (3h) [depends on registration]
├─ [P1] Build logout & token invalidation (2h) [depends on login]
├─ [P2] Implement "forgot password" flow (3h)
└─ [P2] Add rate limiting for security (2h)

Phase 3: Frontend Integration (6h)
├─ [P1] Create login form UI (2h)
├─ [P1] Create registration form UI (2h)
└─ [P2] Add password strength indicator (2h)

Phase 4: Testing & Security (6h)
├─ [P1] Write unit tests for auth logic (2h)
├─ [P1] Security audit & penetration testing (3h)
└─ [P1] Fix any security issues found (1h)

Phase 5: Deployment (4h)
├─ [P1] Set up production environment variables (1h)
├─ [P1] Deploy to staging for testing (1h)
├─ [P1] Deploy to production (1h)
└─ [P2] Set up monitoring & alerts (1h)

Critical Path: Research → Design → DB Setup → Registration → Login → Security Audit → Deploy (21h)

Risks:
- Security vulnerabilities could delay deployment
- Third-party OAuth integration not scoped (would add 8-12h)

Recommendations:
1. Start with research & design to avoid rework
2. Have security review before deployment (not after)
3. Budget 20% contingency (6-8h) for unexpected security issues
4. Consider using proven auth library (Auth0, Clerk) to reduce time by 50%
```

### Example 2: Content Project
```
User: "I need to create a comprehensive blog series on AI productivity tools, deadline in 3 weeks"

Project: AI Productivity Tools Blog Series
Estimated: 24-30 hours
Deadline: 3 weeks (feasible with focused work)

Phase 1: Research (8h)
├─ [P1] Research top 20 AI productivity tools (3h)
├─ [P1] Test 10 most promising tools hands-on (4h) [depends on research]
└─ [P2] Collect user reviews & case studies (1h)

Phase 2: Planning (4h)
├─ [P1] Outline series structure (4-5 posts) (2h) [depends on research]
├─ [P1] Define target audience & key messages (1h)
└─ [P2] Create content calendar (1h)

Phase 3: Writing (10h)
├─ [P1] Write Post 1: Overview & Tool Categories (2h) [depends on outline]
├─ [P1] Write Post 2: Top 5 Tools Deep Dive (3h)
├─ [P1] Write Post 3: Implementation Guide (2h)
├─ [P2] Write Post 4: Advanced Tips & Tricks (2h)
└─ [P3] Write Post 5: Future Trends (1h) [optional]

Phase 4: Editing & Production (4h)
├─ [P1] Self-edit all posts (2h) [depends on writing]
├─ [P2] Create featured images (5 images) (1h)
└─ [P2] Get peer review feedback (1h)

Phase 5: Publishing (2h)
├─ [P1] Format posts in CMS (1h) [depends on editing]
├─ [P1] SEO optimization (titles, meta, keywords) (0.5h)
└─ [P1] Schedule posts & promote on social (0.5h)

Timeline: 3 weeks is achievable if you work 8-10h/week on this
First task: Start with research (spend 3h exploring tools this week)
Suggested schedule: Research week 1, Writing week 2, Edit/publish week 3
```

## Template Projects

If user is working on common project types, suggest templates:

**Available templates:**
- Software Feature Development
- Blog/Content Series
- Website Launch
- Event Planning
- Product Launch
- Research Project
- Marketing Campaign

Example:
```
User: "Help me plan a website launch"

Response: "I can use the Website Launch template which includes typical phases like:
- Design & Planning
- Content Creation
- Development
- Testing
- Marketing Prep
- Launch & Monitoring

Should I use this template as a starting point, or would you prefer a custom breakdown?"
```

## Advanced: Learning from Past Projects

If user has completed similar projects before:

1. Query Projects MCP for projects matching type/keywords
2. Analyze actual vs estimated time for similar tasks
3. Use historical data to adjust estimates
4. Warn about tasks that consistently take longer than expected

Example:
```
"I noticed your last 3 'blog writing' tasks averaged 3.5 hours (not 2 hours as estimated).
I've adjusted this project's estimates based on your actual pace."
```

## Error Handling

**User provides vague project description:**
```
Ask clarifying questions:
- "What's the end deliverable?"
- "Who is this for (customer, team, personal)?"
- "What does success look like?"
```

**Project is too large (>100 hours):**
```
Suggest:
"This is a large project (est. {hours}h). Should I:
1. Break it into sub-projects (phases as projects)?
2. Identify just Phase 1 tasks for now?
3. Continue with full breakdown?"
```

**Deadline is unrealistic:**
```
Be direct:
"⚠️ Reality check: This project needs {required_hours}h of work.
With your deadline of {days} days, you'd need to work {hours_per_day}h/day.

Options:
1. Extend deadline to {realistic_date} ({realistic_hours}h/day)
2. Reduce scope by {X}%
3. Add {N} team members to share work"
```

**No clear phases/structure:**
```
Fall back to simple task list:
"I'll create a flat task list since this project doesn't have clear phases:
- Task 1
- Task 2
- ...
You can reorganize later as the project becomes clearer."
```

## Integration with Other Skills

**After creating plan:**
1. Use priority-assistant to rank tasks by urgency
2. Use task-scheduler to block calendar time for first week of tasks
3. Use deadline-guardian to monitor project progress

**Compose Skills:**
```
User: "Plan my website project and schedule the first week of work"

Steps:
1. project-planner: Break down project into tasks
2. priority-assistant: Rank tasks by priority
3. task-scheduler: Schedule top 5 tasks on calendar
4. Result: User has plan + scheduled time to start
```

## Notes

- Be realistic, not optimistic, with estimates (add 20% buffer)
- Always identify the critical path (what determines project length)
- Suggest starting with highest-risk or highest-uncertainty tasks first
- For team projects, consider skill levels when assigning estimates
- Store project plans for future reference and learning
- Encourage user feedback: "Did these estimates match reality?"
