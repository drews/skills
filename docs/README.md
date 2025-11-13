# Motion AI Alternative - SaaS vs FOSS Analysis

> **Project Goal:** Build a self-hosted, open-source alternative to Motion using Skills and FOSS tools

---

## 📋 Documentation Index

This analysis breaks down Motion's AI capabilities and designs a FOSS alternative architecture.

### Core Documents

1. **[Motion AI Feature Specification](./motion-ai-feature-spec.md)**
   - Comprehensive breakdown of Motion's AI affordances
   - 22 distinct feature categories with testable criteria
   - AI inputs, outputs, and decision-making processes
   - Implementation complexity assessment
   - Priority matrix for development

2. **[Parity Checklist](./parity-checklist.md)**
   - Testable criteria for each feature (122 test cases)
   - Parity levels (0-5) tracking framework
   - Implementation status tracking
   - Phased development roadmap (6 phases, 34 weeks)
   - Success metrics and KPIs

3. **[Architecture Design](./architecture.md)**
   - Complete system architecture (6 layers)
   - 7 core Skills specifications
   - MCP integration layer design
   - Data flow examples
   - Deployment architectures (single-user & multi-user)
   - Technology stack recommendations

4. **[MCP Integration Guide](./mcp-integration-guide.md)**
   - 6 required MCP servers (Calendar, Tasks, Projects, Notes, Search, Analytics)
   - Detailed API specifications (JSON schemas)
   - Existing vs. need-to-build assessment
   - Implementation guide with code examples
   - Testing and security considerations

---

## 🎯 Executive Summary

### What is Motion?

Motion is a $34/month SaaS productivity tool that uses AI to:
- Automatically schedule tasks into your calendar
- Re-optimize your schedule hundreds of times per day
- Warn about at-risk deadlines weeks in advance
- Answer "what should I work on next?"
- Learn your productivity patterns over time
- Manage projects with automatic planning and capacity allocation

**Key Differentiator:** Continuous AI-powered schedule optimization that eliminates manual task management.

**Time Savings Claimed:** 2.5-5 hours per week

---

### FOSS Alternative Feasibility

**Verdict: ✅ ACHIEVABLE**

We can replicate Motion's core value proposition using:

**FOSS Stack:**
- **Calendar:** Radicale (CalDAV)
- **Tasks:** Vikunja
- **Projects:** Plane or OpenProject
- **Notes:** Nextcloud or Outline
- **Search:** Meilisearch
- **Workflows:** n8n
- **Database:** PostgreSQL + Redis
- **AI:** Claude API + Local Ollama

**Skills Layer (Our Innovation):**
- 7 markdown instruction files (SKILL.md) that teach Claude how to orchestrate FOSS tools
- MCP (Model Context Protocol) servers provide tools (calendar, tasks, projects)
- Skills teach Claude WHEN and HOW to use MCP tools intelligently
- Optional helper scripts for complex computations (constraint solving, ML)
- Progressive disclosure: Skills loaded on-demand, minimal context usage

---

### Key Advantages: FOSS vs Motion

| Feature | Motion (SaaS) | Our FOSS Alternative |
|---------|---------------|----------------------|
| **Data Ownership** | Cloud-hosted | Self-hosted (full control) |
| **Privacy** | 3rd party access | Local-only (optional LLM) |
| **Cost** | $408/year | One-time setup + hosting (~$60/year) |
| **Customization** | Fixed features | Fully customizable |
| **Integration** | Limited APIs | Unlimited (open source) |
| **AI Control** | Black box | Transparent, explainable |
| **Setup** | Instant | Technical (docker-compose) |
| **Maintenance** | Zero | Self-managed |
| **Multi-tenant** | Built-in | Phase 4 addition |

---

### Key Challenges: FOSS Implementation

1. **Complexity Gap**
   - Motion: Single integrated app
   - Ours: Multiple tools + glue layer

2. **AI Re-optimization**
   - Motion: Proprietary constraint solver (hundreds of recalcs/day)
   - Ours: Need custom scheduling engine (OR-Tools)

3. **Setup Barrier**
   - Motion: Sign up and start
   - Ours: Docker-compose, config, learning curve

4. **Real-time Updates**
   - Motion: Instant sync across devices
   - Ours: Need WebSocket/polling for real-time

5. **Mobile Experience**
   - Motion: Native apps
   - Ours: Web PWA initially, native later

---

## 🏗️ Architecture Overview

**Key Insight:** Skills are markdown instructions that teach Claude how to use tools, not programmatic services.

```
┌─────────────────────────────────────────────────────────┐
│                 Claude Code (Orchestrator)               │
│   Reads Skills, follows instructions, uses MCP tools    │
└────────────────────────┬────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────┐
│            Skills Layer (Markdown Instructions)          │
│                   .claude/skills/*.md                    │
│                                                          │
│  • task-scheduler/SKILL.md      (how to schedule)       │
│  • priority-assistant/SKILL.md  (how to prioritize)     │
│  • deadline-guardian/SKILL.md   (how to detect risks)   │
│  • project-planner/SKILL.md     (how to plan projects)  │
│  • productivity-analyzer/SKILL.md (how to learn)        │
│  • meeting-optimizer/SKILL.md   (how to find slots)     │
│  • context-assistant/SKILL.md   (how to filter tasks)   │
│                                                          │
│  + Optional helper scripts (executed, not loaded)       │
└────────────────────────┬────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────┐
│              MCP Servers (Extend Claude's Tools)         │
│                                                          │
│  Calendar │ Tasks │ Projects │ Notes │ Search │ Analytics
└────────────────────────┬────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────┐
│                FOSS Backend Services                     │
│                                                          │
│  Radicale │ Vikunja │ Plane │ Nextcloud │ Meilisearch  │
└──────────────────────────────────────────────────────────┘
```

**Key Innovation:** Skills act as intelligent orchestration layer, coordinating FOSS tools to deliver Motion-like AI experiences.

---

## 📊 Feature Parity Assessment

### Must-Have Features (Core Value)

| Feature | Complexity | Status | Target Phase |
|---------|------------|--------|--------------|
| Intelligent Task Scheduling | High | Not Started | Phase 1 |
| Real-Time Re-optimization | High | Not Started | Phase 2 |
| Deadline Risk Detection | Medium | Not Started | Phase 1 |
| Multi-Factor Priority Scoring | Low | Not Started | Phase 1 |
| Explainable Decisions | Low | Not Started | Phase 1 |

### High Priority Features

| Feature | Complexity | Status | Target Phase |
|---------|------------|--------|--------------|
| Task Duration Learning | Medium | Not Started | Phase 3 |
| Productivity Pattern Recognition | High | Not Started | Phase 3 |
| "What to Work On Next" | Medium | Not Started | Phase 3 |
| Project Timeline Prediction | High | Not Started | Phase 4 |
| User Override & Learning | Very High | Not Started | Phase 2 |

**Current Parity Score: 0 / 110 (0%)**

**Target Milestones:**
- Phase 1 (Week 8): 30/110 (27%) - Usable CLI tool
- Phase 2 (Week 14): 50/110 (45%) - Real-time intelligence
- Phase 3 (Week 20): 70/110 (64%) - Competitive alternative
- Phase 4 (Week 26): 85/110 (77%) - Production-ready
- Phase 5 (Week 34): 95/110 (86%) - Feature-complete

---

## 🚀 Development Roadmap

### Phase 1: Foundation (Weeks 1-8)
**Goal:** Basic AI scheduling from CLI

**Deliverables:**
- ✅ Multi-factor priority scoring
- ✅ Intelligent task scheduling (constraint solver)
- ✅ Deadline risk detection
- ✅ Explainable decisions
- ✅ CLI interface
- ✅ CalDAV + Vikunja integration via MCP

**Success Criteria:**
- Schedule 20 tasks across 1 week without conflicts
- Calculate risk scores for projects
- Explain every scheduling decision

---

### Phase 2: Real-Time Intelligence (Weeks 9-14)
**Goal:** Adaptive scheduling that responds to changes

**Deliverables:**
- ✅ Real-time schedule re-optimization (<2s)
- ✅ User override tracking
- ✅ Learning from corrections
- ✅ Event-driven rescheduling

**Success Criteria:**
- Re-schedule on calendar changes within 2 seconds
- Override rate decreases from 30% → <10% (after 4 weeks)

---

### Phase 3: Productivity Enhancement (Weeks 15-20)
**Goal:** AI that understands your work patterns

**Deliverables:**
- ✅ Task duration learning
- ✅ Productivity pattern recognition
- ✅ "What to work on next" guidance
- ✅ Context-aware filtering

**Success Criteria:**
- Duration estimates within 15% accuracy (after 10 samples)
- Correctly identifies peak productivity hours
- Top priority recommendation accepted 80%+ of time

---

### Phase 4: Project Intelligence (Weeks 21-26)
**Goal:** Full project planning and tracking

**Deliverables:**
- ✅ Project timeline prediction
- ✅ Automatic project decomposition (LLM)
- ✅ Interruption recovery
- ✅ Plane/OpenProject integration

**Success Criteria:**
- Timeline predictions within 20% of actuals
- Generated project tasks require <10% editing
- Interruptions handled automatically

---

### Phase 5: Advanced Features (Weeks 27-34)
**Goal:** Complete productivity suite

**Deliverables:**
- ✅ Resource capacity planning
- ✅ Smart templates & SOPs
- ✅ Meeting optimization
- ✅ Web UI (React)

**Success Criteria:**
- Full Motion feature parity on core workflows
- Users save 2+ hours/week
- System Usability Scale (SUS) > 70

---

### Phase 6: Scale & Polish (Weeks 35+)
**Goal:** Production-ready for multi-user

**Deliverables:**
- ✅ Multi-user support
- ✅ Team collaboration features
- ✅ Performance optimization
- ✅ Mobile app (optional)
- ✅ Plugin marketplace

---

## 🛠️ MCP Server Requirements

### ✅ Available Now (Use Existing)

1. **Calendar MCP** - `caldav-mcp`
   - Supports: Radicale, Nextcloud, Google Calendar, iCloud
   - Tools: list_events, create_event, update_event, delete_event
   - **Need to add:** find_free_slots tool

### 🔨 Need to Build (Priority Order)

2. **Tasks MCP** - For Vikunja
   - Tools: list, get, create, update, complete, get_dependencies
   - Estimated: 2-3 days

3. **Analytics MCP** - For metrics tracking
   - Tools: track_event, get_metrics, get_productivity_report
   - Storage: PostgreSQL or TimescaleDB
   - Estimated: 2-3 days

4. **Projects MCP** - For Plane/OpenProject
   - Tools: list, get, create, create_task, get_timeline, get_capacity
   - Estimated: 3-4 days

5. **Search MCP** - For Meilisearch
   - Tools: query, semantic_search, suggest_related
   - Estimated: 2 days

6. **Notes MCP** - For Nextcloud/Outline
   - Tools: list, get, create, link_to_task
   - Estimated: 1-2 days

**Total Development Time:** ~2 weeks for all MCP servers

---

## 💡 Key Insights

### Motion's "Secret Sauce"

After analyzing Motion's features, the core differentiator is:

1. **Constraint Satisfaction Solver**
   - Sophisticated algorithm (likely similar to Google OR-Tools)
   - Optimizes task placement given constraints
   - Runs continuously (hundreds of times/day)

2. **Multi-Factor Scoring**
   - Weights: deadline proximity, priority, duration, dependencies
   - Personalized per user over time
   - Explains reasoning transparently

3. **Real-Time Event Processing**
   - Calendar webhooks or aggressive polling
   - Triggers immediate re-optimization
   - Maintains schedule validity at all times

4. **Pattern Recognition**
   - Learns task durations from actuals
   - Identifies productivity patterns
   - Adapts scheduling to user preferences

**We can build all of this with FOSS tools.**

---

### What We'll Do Better

1. **Data Ownership:** Your data never leaves your infrastructure
2. **Transparency:** Every AI decision is explainable and inspectable
3. **Customization:** Modify scheduling algorithm, add custom skills
4. **Integration:** Connect to any tool via MCP
5. **Privacy:** Optional local LLM (Ollama) for zero external API calls
6. **Cost:** Free after initial setup (vs $408/year)

---

### What Will Be Challenging

1. **Setup Complexity:** Docker-compose vs "sign up and go"
2. **UX Polish:** Will take time to match Motion's refined interface
3. **Mobile Experience:** Web-first, native apps later
4. **Real-time Sync:** Requires infrastructure (WebSockets, etc)
5. **Onboarding:** Learning curve for self-hosting

**Mitigation:** Phase 4 introduces hosted version (optional) for less technical users.

---

## 📈 Success Metrics

### User Impact (vs Manual Workflow)

- **Time Saved:** 2+ hours/week on task management
- **Schedule Adherence:** +15% tasks completed on time
- **Deadline Success:** +20% projects delivered on schedule
- **Cognitive Load:** Immediate answer to "what should I work on?"

### Technical Performance

- **Schedule Calculation:** <500ms per task
- **Re-optimization:** <2s for 50-task schedule
- **Calendar Sync:** <1s
- **System Uptime:** 99.5%

### Adoption

- **User Satisfaction:** SUS score >70
- **Retention:** 80%+ users active after 30 days
- **Feature Usage:** Top 3 skills used daily

---

## 📝 What Are Skills? (Per Anthropic)

**Skills are markdown instruction files** that extend Claude Code's knowledge, not code.

### Key Principles:

1. **Progressive Disclosure:**
   - Claude sees only Skill name/description initially (~25 tokens)
   - Full SKILL.md loaded only when relevant
   - Helper scripts executed WITHOUT loading into context
   - Scales to unlimited Skills

2. **"Context Window is a Public Good":**
   - Be extremely concise in SKILL.md
   - Claude is already smart - don't explain what it knows
   - Move complex logic to helper scripts
   - Put rarely-used details in separate files

3. **Skills vs MCP Servers:**
   - **MCP Server:** Extends Claude's **tools** (new capabilities)
   - **Skill:** Extends Claude's **knowledge** (how to use tools)
   - Together: MCP gives tools, Skills teach when/how to use them

### Example Skill Structure:

```markdown
---
name: task-scheduler
description: Schedule tasks into calendar using AI. Use when user wants to add tasks or optimize schedule.
---

# Task Scheduler

## When to Use
- User says "schedule task X"
- User asks "when should I work on Y?"

## Instructions
1. Get task details (title, duration, deadline, priority)
2. Call Calendar MCP: calendar_list_events
3. Find free slots matching task duration
4. Score slots by: time-of-day, deadline urgency, priority
5. Create event with Calendar MCP
6. Explain WHY you chose that slot
```

**Result:** Claude can now intelligently schedule tasks like Motion does!

---

## 🎓 Skills as Reusable Patterns

This project demonstrates that complex AI experiences (like Motion) can be decomposed into **teachable patterns**:

- Each Skill is a recipe Claude can follow
- Skills compose together (deadline-guardian uses priority-assistant)
- Learned preferences stored separately, shared across Skills
- Skills are portable: copy SKILL.md to any Claude Code project

**If successful:**
- These Skills become templates for similar workflows
- Community can create Skill variations
- Skills marketplace emerges (already happening!)
- Pattern: "Don't write code, write instructions"

---

## 🔗 Next Steps

### Immediate (This Week)

1. ✅ **Review this analysis** - Validate approach
2. 🔨 **Set up development environment**
   - Install Docker
   - Deploy FOSS stack (Radicale, Vikunja)
   - Configure CalDAV MCP server in Claude Code

3. 🔨 **Write first Skill: priority-assistant/SKILL.md**
   - Simplest starting point (no helper scripts needed)
   - Markdown file with instructions for Claude
   - Teaches Claude how to calculate and explain task priorities
   - Validates that Claude can follow Skill instructions

### Week 2

4. 🔨 **Build Tasks MCP Server for Vikunja**
   - TypeScript/Node.js MCP server implementation
   - Core tools: list, create, update, complete
   - Test with MCP Inspector
   - Configure in Claude Code

5. 🔨 **Write task-scheduler/SKILL.md**
   - Markdown instructions for scheduling logic
   - Teaches Claude how to find optimal calendar slots
   - Includes simple scoring algorithm in instructions
   - (Optional) Add helper script if scoring gets complex

### Week 3-4

6. 🔨 **Write deadline-guardian/SKILL.md**
7. 🔨 **Add helper scripts for complex calculations**
   - Python script for risk score calculation
   - Keep SKILL.md concise, delegate math to script
8. 🧪 **User testing with 2-3 beta users**
   - Test if Claude correctly follows Skill instructions
   - Refine SKILL.md descriptions based on when Claude uses them
   - Measure context token usage

---

## 📚 Resources

### Documentation
- Motion Feature Spec: [motion-ai-feature-spec.md](./motion-ai-feature-spec.md)
- Architecture: [architecture.md](./architecture.md)
- MCP Integration: [mcp-integration-guide.md](./mcp-integration-guide.md)
- Parity Checklist: [parity-checklist.md](./parity-checklist.md)

### External
- MCP Protocol: https://modelcontextprotocol.io
- CalDAV MCP: https://github.com/dominik1001/caldav-mcp
- Vikunja API: https://vikunja.io/docs/api-documentation/
- Google OR-Tools: https://developers.google.com/optimization
- Plane API: https://plane.so/developers/api-reference

### FOSS Tools
- Radicale: https://radicale.org
- Vikunja: https://vikunja.io
- Plane: https://plane.so
- Nextcloud: https://nextcloud.com
- Meilisearch: https://www.meilisearch.com

---

## 🤝 Contributing

This is an exploratory project to understand Motion's AI affordances and design a FOSS alternative.

**Current Status:** 📋 Planning & Architecture (Week 0)

**What's Needed:**
- Feedback on architecture design
- MCP server implementations
- Scheduling engine expertise (OR-Tools, OptaPlanner)
- Beta testers (once MVP ready)

**Contact:** [Add your contact info]

---

## 📄 License

[To be determined - likely MIT or Apache 2.0 for maximum permissiveness]

---

**Last Updated:** 2025-01-11

**Status:** Analysis Complete ✅ | Ready to begin implementation 🚀
