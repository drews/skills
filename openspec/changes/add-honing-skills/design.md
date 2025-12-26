## Context

This design addresses the need for meta-cognitive guidance in strategically utilizing Agent Skills. Currently, users have access to individual specialized skills (getting-started, saving-progress, sensing-limits, operating-systems) but lack a framework for understanding when and how to combine these skills for complex multi-faceted tasks.

**Stakeholders:**
- Users tackling complex projects requiring multiple skills
- Users learning to develop strategic thinking about tool/skill selection
- Skill developers who want to document interaction patterns

**Constraints:**
- Must remain skill-based (SKILL.md format) rather than creating new infrastructure
- Should enhance rather than replace existing skills
- Must provide actionable guidance, not just theory
- Should work within Claude Code's skill discovery and loading mechanisms

## Goals / Non-Goals

**Goals:**
- Provide clear decision frameworks for when to combine skills
- Document proven skill combination patterns for common scenarios
- Build user mental models for strategic skill deployment
- Serve as "context attractor" - natural entry point for complex tasks
- Create reusable orchestration patterns

**Non-Goals:**
- Not creating new skill execution infrastructure
- Not replacing individual specialized skills
- Not automating skill invocation (stays advisory)
- Not creating a complex skill dependency management system

## Decisions

### Decision: Skill vs Infrastructure Approach
**Chosen:** Implement as a regular SKILL.md file within the existing skill framework

**Rationale:**
- Leverages existing skill discovery mechanisms
- No new infrastructure needed
- Users can invoke naturally via skill triggering
- Maintains consistency with other skills
- Easy to version and distribute

**Alternatives considered:**
- **New orchestration layer**: Rejected - too complex, unnecessary abstraction
- **Documentation-only**: Rejected - less discoverable, not integrated into workflow
- **Hard-coded skill chains**: Rejected - inflexible, doesn't teach strategic thinking

### Decision: Pattern Library Approach
**Chosen:** Maintain concrete, scenario-based pattern library within the skill

**Rationale:**
- Users learn best from concrete examples
- Patterns can be matched against current situation
- Allows incremental learning
- Easy to extend over time

**Structure:**
```markdown
## Skill Combination Library

### Pattern: [Name]
**When to use:** [Trigger conditions]
**Skills involved:** [List]
**Sequence:** [Step-by-step]
**Example:** [Concrete scenario]
```

### Decision: Relationship Documentation Strategy
**Chosen:** Bidirectional light references

**In honing-skills:**
- References to other skills when discussing patterns
- Map of skill ecosystem with relationships

**In existing skills:**
- Light mention that honing-skills exists for orchestration
- Link from "Relationship to Other Skills" section

**Rationale:**
- Avoids heavy coupling between skills
- Makes honing-skills discoverable from other skills
- Maintains single source of truth in honing-skills
- Doesn't clutter individual skills with orchestration details

### Decision: Triggering Strategy
**Chosen:** Broad pattern matching on complexity, multi-faceted tasks, and strategic thinking keywords

**Trigger patterns:**
- Complexity signals: "complex", "many parts", "multiple aspects"
- Strategy requests: "how should I approach", "what's the best way", "strategy"
- Skill orchestration: "which skills", "combine skills", "skill workflow"
- Overwhelm: "don't know which skill", "too many options"

**Rationale:**
- Captures both explicit and implicit requests for meta-cognitive help
- Doesn't over-trigger on simple single-skill scenarios
- Allows users to develop awareness of when orchestration helps

## Architecture

### Information Architecture

```
honing-skills/SKILL.md
│
├── Meta-Cognitive Skill Recognition
│   ├── Multi-faceted task identification
│   ├── Complexity breakdown strategies
│   └── Component skill mapping
│
├── Skill Orchestration Patterns
│   ├── Sequential application
│   ├── Parallel considerations
│   └── Nested relationships
│
├── Strategic Decision Framework
│   ├── Task characteristic analysis
│   ├── Capacity-aware selection
│   └── Outcome-driven mapping
│
├── Mental Model Development
│   ├── Pattern identification teaching
│   ├── Meta-awareness cultivation
│   └── Common pitfall avoidance
│
├── Skill Combination Library
│   ├── Interrupted project recovery
│   ├── Overwhelming new project
│   ├── Capacity-constrained execution
│   └── [Additional patterns...]
│
└── Skill Interaction Documentation
    ├── Relationship map
    ├── Natural transitions
    └── Conflict patterns
```

### Skill Ecosystem Integration

```
User Task (Complex)
       ↓
   honing-skills (Meta-cognitive layer)
       ↓
   Strategic Plan
       ↓
   ┌─────────┬─────────┬─────────┐
   ↓         ↓         ↓         ↓
getting-  saving-  sensing-  domain-
started   progress  limits   specific
```

## Risks / Trade-offs

### Risk: Over-abstraction
**Description:** Users might get lost in meta-thinking instead of taking action

**Mitigation:**
- Always provide concrete next steps
- Include "just do it" escape hatch for simple-seeming tasks
- Balance theory with practice-oriented guidance
- Include anti-pattern: "Don't use honing-skills when X"

### Risk: Skill bloat
**Description:** Skill becomes too long and overwhelming

**Mitigation:**
- Use progressive disclosure (high-level first, details on demand)
- Reference external patterns rather than inlining everything
- Keep individual sections focused and scannable
- Consider future split if it exceeds ~500 lines

### Risk: Maintenance burden
**Description:** Keeping skill combination patterns up-to-date as skills evolve

**Mitigation:**
- Document patterns at strategic level, not implementation detail
- Focus on durable patterns (sequential, parallel, nested)
- Review when new skills added to ecosystem
- Keep patterns modular for easy updates

### Trade-off: Simplicity vs Completeness
**Chosen:** Favor simplicity and actionability

**Rationale:**
- Better to have 5 clear patterns than 20 confusing ones
- Users can request additional patterns over time
- Clearer decision frameworks beat comprehensive taxonomies
- Can always expand later based on usage

## Migration Plan

N/A - This is a new capability with no existing implementation to migrate.

**Rollout approach:**
1. Create skill in honing-skills/ directory
2. Add to marketplace.json in exec-func-skills plugin
3. Test locally with `--plugin-dir`
4. Deploy via marketplace update
5. Monitor usage and gather feedback for pattern expansion

## Open Questions

1. **Should honing-skills recommend specific skill sequences explicitly?**
   - Current approach: Yes, provide concrete recommendations
   - Alternative: Only provide framework and let users decide
   - Decision: Recommend explicitly, but explain reasoning so users learn

2. **How detailed should the skill relationship map be?**
   - Option A: Simple directed graph showing natural flows
   - Option B: Detailed matrix of all skill interactions
   - Current approach: Option A for clarity, can expand based on feedback

3. **Should there be versioning for pattern library?**
   - Current approach: No versioning, patterns evolve in place
   - Can revisit if patterns need significant breaking changes

4. **Integration with future skills?**
   - Honing-skills should be updated when significant new skills are added
   - Consider whether new skills should reference honing-skills in their SKILL.md
   - Pattern: New domain skills = update skill combination library
