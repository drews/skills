---
name: checking-in
description: Matches task selection to available capacity and energy levels. Use when user mentions "tired", "low energy", "burnt out", "overwhelmed", "no spoons", "need a break", asks "what can I do right now", requests capacity assessment, or needs recommendations for tasks appropriate to their current state.
---

# Checking In

Helps match task selection and planning to current capacity, recognizing that available energy fluctuates and task difficulty should adapt accordingly.

## When to Use This Skill

Use this skill when detecting:
- **Energy mentions**: "tired", "exhausted", "low energy", "burnt out", "drained"
- **Capacity language**: "overwhelmed", "too much", "can't handle", "at my limit"
- **Spoon theory**: "out of spoons", "need to conserve energy", "spoon budget"
- **State-aware requests**: "what can I actually do right now", "given how I'm feeling"
- **Break needs**: "need rest", "should I take a break", "pushing too hard"

## Core Principles

### 1. Energy is Variable and Valid
Current capacity is not constant. Tired/low-energy states don't mean failure—they mean the task-matching algorithm needs different parameters.

### 2. Match Tasks to Capacity, Not Aspirations
Recommend what's actually doable now, not what "should" be doable. Better to complete a small thing than abandon a big thing.

### 3. Preserve Future Capacity
Pushing through exhaustion often costs more later. Sometimes the right recommendation is "rest" or "smaller scope."

### 4. Energy Types Matter
Physical, mental, emotional, and social energy are different. Low in one doesn't mean low in all.

## Quick Capacity Assessment

### The Three-Question Check-In
Fast assessment to calibrate recommendations:

1. **Physical energy**: "How does your body feel right now?" (rested/okay/tired/exhausted)
2. **Mental energy**: "How's your brain?" (sharp/functional/foggy/fried)
3. **Emotional energy**: "How's your resilience right now?" (solid/managing/fragile/depleted)

Don't need detailed answers—rough sense is enough.

### Energy Level Framework

**High Capacity**
- Physical: Rested, energized
- Mental: Sharp, focused, can handle complexity
- Emotional: Resilient, stable
- Appropriate tasks: Complex problem-solving, learning new things, high-stakes decisions, creative work

**Medium Capacity**
- Physical: Functional but not peak
- Mental: Can focus with effort, prefer familiar territory
- Emotional: Okay but not much buffer
- Appropriate tasks: Routine work, incremental progress on known tasks, low-stakes decisions, maintenance

**Low Capacity**
- Physical: Tired, moving slowly
- Mental: Foggy, hard to focus, slow processing
- Emotional: Little resilience, easily frustrated
- Appropriate tasks: Rote tasks, cleanup, low-cognitive-load activities, enjoyable work

**Depleted/Crisis**
- Physical: Exhausted
- Mental: Can't focus, memory issues
- Emotional: Fragile, overwhelmed
- Appropriate response: Rest, basic self-care, defer everything possible

## Task-to-Capacity Matching

### Task Classification System

Categorize tasks by what they demand:

**Cognitive Load**
- High: Novel problems, learning, complex analysis, architecture decisions
- Medium: Familiar work with some complexity, moderate problem-solving
- Low: Rote tasks, following clear procedures, maintenance work
- Minimal: Organizing, cleanup, passive reviewing

**Emotional Load**
- High: Conflict, high-stakes communication, rejection risk, vulnerability
- Medium: Collaboration, routine communication, minor discomfort
- Low: Solo work, supportive interactions, comfortable activities
- Minimal: Neutral tasks, no interpersonal component

**Physical Load**
- High: Sustained focus, precision, long sessions
- Medium: Moderate attention, some breaks okay
- Low: Flexible attention, interruption-tolerant
- Minimal: Can do while low-energy, rest-compatible

### Matching Algorithm

```
Current Capacity + Task Requirements → Recommendation

High capacity + High-load task = Good match, proceed
High capacity + Low-load task = Good match (saves energy for later)
Low capacity + High-load task = Defer or scope down
Low capacity + Low-load task = Good match, proceed
Depleted + Any task = Recommend rest first
```

## Low-Energy Mode Strategies

### When Capacity is Limited

**Option 1: Scope Down**
- Instead of "write documentation" → "bullet points of what docs should cover"
- Instead of "design solution" → "list what the solution needs to handle"
- Instead of "implement feature" → "write test case showing what feature should do"

**Option 2: Switch Domains**
- If mental energy is low but physical is okay → cleanup, organization, physical tasks
- If emotional energy is low but mental is okay → solo technical work, avoid people tasks
- If everything is low → rest or enjoyable passive activities

**Option 3: Lower Stakes**
- Do the version where mistakes are cheap
- Prototype instead of final
- Draft instead of polished
- Experiment instead of commit

**Option 4: Automation/Tools**
- Let tools do the heavy lifting
- Use templates and patterns
- Copy from previous work
- Lean on AI assistance more

### The "Rest is Productive" Reframe

Sometimes the most productive thing is to stop. Rest now vs. burnout later:
- **Strategic rest**: Preserves capacity for future high-value work
- **Efficiency gain**: Better to work 2 good hours than 6 struggling hours
- **Compound effect**: Small rest deficits compound into major crashes

## Spoon Budgeting

### Spoon Theory Application

If user references spoons or energy budgeting:

**Track Spending**
- Each task costs spoons based on load
- Fixed costs (getting started, context switching) matter
- Some activities replenish spoons (rest, joy, flow states)

**Budget Throughout Day**
```
Available spoons: [user's sense of daily budget]
Already spent: [morning tasks]
Remaining: [what's left]

Planned expenses:
- [Task A]: ~X spoons
- [Task B]: ~Y spoons
- [Buffer for unexpected]: ~Z spoons

Assessment: [over/under/at budget]
```

**Spending Strategies**
- **Front-load**: Hardest tasks when fresh (if sustainable)
- **Spread**: Distribute load across time
- **Batch**: Group similar tasks to reduce switching costs
- **Deficit spending**: Sometimes necessary, but track the debt

## Break Timing and Types

### When to Recommend Breaks

- **Proactive**: Before fatigue → better than reactive
- **Signs of diminishing returns**: Struggling with normally easy tasks
- **Fixed intervals**: Time-based (Pomodoro-style)
- **Task completion**: Natural breakpoints after finishing something

### Types of Breaks

**Micro-breaks (1-5 min)**
- Stand, stretch, water
- Eyes away from screen
- Quick physical reset
- Between focused sessions

**Short breaks (10-20 min)**
- Walk around
- Different activity entirely
- Snack, social, nature
- Between major tasks

**Long breaks (30+ min)**
- Meal, exercise, rest
- Full context switch
- Between work blocks
- Replenishment activities

**Recovery days**
- Full rest/low demand
- After intense periods
- Before big pushes
- Preventive maintenance

## Hyperfocus Awareness

### Supporting Hyperfocus Productively

When user is in flow but may not notice time passing:

**Gentle Awareness (Not Interruption)**
- "You've been focused for [time]—doing great. Remember to hydrate."
- Offer breaks but don't force them
- Check in on physical needs (food, water, rest)
- Help notice if hyperfocus becomes counterproductive

**Hyperfocus Recovery**
- Coming out of deep focus can be disorienting
- May need explicit reminder to rest
- Often followed by energy crash—plan accordingly
- Mark accomplishments during focused period

### Anti-Patterns

❌ Don't break flow without good reason
❌ Don't make user feel bad about sustained focus
✅ Do help notice physical needs
✅ Do support sustainable work patterns

## Capacity-Aware Decision Making

### When Energy Affects Decisions

Low capacity changes decision quality:
- **Analysis paralysis increases** when tired
- **Risk tolerance changes** (often more risk-averse when depleted)
- **Emotional reactivity** higher when low resilience
- **Time horizons shrink** (harder to think long-term)

**Recommendation Pattern**:
"This is a significant decision and you mentioned being [tired/overwhelmed]. Options:
1. Make a small/reversible version of the decision now
2. List the decision factors now, decide later when fresh
3. Set a clear 'decide by' deadline to prevent indefinite deferral"

## Integration with Other Skills

### Coordinating Across Skills

**With task-initiator**:
- If capacity is low, emphasis on absolute easiest starting point
- Adjust "5-minute starter" to current energy (maybe "2-minute starter")
- Build in more scaffolding when depleted

**With context-checkpoint**:
- Consider capacity when planning resume timing
- Low-energy resumes need more explicit context loading
- May recommend shorter work sessions when capacity is limited

## Anti-Patterns to Avoid

❌ Don't guilt or shame about low energy
❌ Don't push "just power through" mindset
❌ Don't ignore physical/rest needs
❌ Don't treat all low-energy states the same (causes vary)
✅ Do normalize capacity fluctuation
✅ Do match tasks to actual current state
✅ Do preserve long-term sustainability

## Success Indicators

This skill is working when:
- User reports better task-capacity matches
- User completes more tasks (even if individually smaller)
- User experiences less burnout/crash cycles
- User feels permission to work with their energy, not against it
- User can name their capacity level and adjust accordingly
