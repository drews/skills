# Accessibility & Usability Review: Executive Function Skills

**Date**: 2025-12-22
**Scope**: Analysis of drews-skills plugin patterns and optimization recommendations

---

## Executive Summary

Your skills (`getting-started`, `saving-progress`, `sensing-limits`) form a cohesive **executive function support system** addressing task initiation, working memory, and self-regulation. This review identifies friction points and proposes five plugins to close gaps.

**Key finding**: These skills require manual invocation precisely when users are least able to self-monitor—a known paradox in executive function support ([Barkley, 2012][1]).

---

## Behavioral Pattern Analysis

### Observed Intent Categories

| Pattern | Evidence | Cognitive Domain |
|---------|----------|------------------|
| Task initiation support | `getting-started`: "analysis paralysis", "stuck" | Initiation |
| Working memory externalization | `saving-progress`: context saves, open loops | Working Memory |
| Energy/capacity management | `sensing-limits`: spoon theory, task matching | Self-Regulation |
| Cognitive load optimization | CLAUDE.md: hierarchical loading | Processing Efficiency |

### Executive Function Coverage

| EF Domain | Skill | Mechanisms |
|-----------|-------|------------|
| **Initiation** | `getting-started` | 5-minute starters, smallest viable action, decision scaffolding |
| **Working Memory** | `saving-progress` | Checkpoints, open loops tracking, interrupt recovery |
| **Self-Regulation** | `sensing-limits` | Capacity assessment, task-energy matching, break timing |

---

## Friction Point Analysis

| Issue | Severity | Problem |
|-------|----------|---------|
| Manual skill invocation | High | User must remember skill exists when most impaired |
| No automatic persistence | High | State lost on session end unless manually saved |
| Self-reported energy levels | Medium | Requires self-monitoring during depletion |
| Time tracking burden | Medium | User responsible for session awareness |

---

## Research-Backed Recommendations

### 1. Automate State Detection

**Problem**: Invoking `sensing-limits` requires the self-monitoring capacity that depletion impairs.

**Evidence**: Executive function deficits create a paradox—recognizing the need for support requires the very resources that are depleted ([Barkley, 2012][1]).

**Solution**: Infer state from behavioral signals:
- Increasing typos → cognitive fatigue
- Shorter messages → conservation mode
- Repeated clarifications → working memory strain
- Emotional markers ("ugh", "sigh") → depletion

### 2. Automatic Context Checkpointing

**Problem**: `saving-progress` requires invocation *before* interruptions—but interruptions are unplanned.

**Evidence**: Prospective memory (remembering to remember) is particularly vulnerable to depletion ([Kliegel et al., 2008][2]).

**Solution**: Periodic automatic state capture at session boundaries and significant interaction points.

### 3. Temporal Scaffolding

**Problem**: Time blindness is a core executive function challenge not currently addressed.

**Evidence**: Difficulty perceiving and estimating time is central to ADHD ([Ptacek et al., 2019][3]).

**Solution**: Gentle temporal grounding without productivity pressure.

---

## Proposed Plugins

### 1. `auto-checkpoint`

Automatic working memory externalization.

**Triggers**:
- Session start (load last checkpoint)
- Every 15 messages (delta save)
- Before long-running operations
- On "stepping away" phrases
- Session end (full save)

**Output**: Writes to `.context/current.md` with inferred context, last action, open questions, predicted next step.

**Priority**: High impact, addresses #1 friction point.

---

### 2. `capacity-inference`

Detect capacity from behavioral signals.

**Signals monitored**:
- Message length trends
- Error/correction frequency
- Emotional language markers
- Decision latency
- Clarification request frequency

**Output**: Inferred capacity level, automatic scope adjustment suggestions, proactive break recommendations.

**Priority**: High impact, solves self-monitoring paradox.

---

### 3. `momentum-keeper`

Bridge sessions by preparing on-ramps for future-self.

**Features**:
- Pre-close ritual: "Before you go, let's capture..."
- Session-start ritual: "Welcome back. Last time you..."
- Warm-up micro-tasks
- Resume cue generation
- Cross-session task linking

**Integration**: Uses `auto-checkpoint` data, feeds `getting-started` with pre-loaded context.

**Priority**: Medium impact, builds on auto-checkpoint.

---

### 4. `time-anchor`

Non-intrusive temporal awareness.

**Features**:
- Session duration display (opt-in)
- Natural pause point suggestions
- Hyperfocus detection ("2 hours deep—hydrate?")
- Work block framing

**Anti-patterns avoided**:
- No countdown timers (pressure)
- No productivity metrics (judgment)
- No streaks/gamification (external motivation)

**Priority**: Lower urgency, quality-of-life enhancement.

---

### 5. `decision-buffer`

Protect against depleted-state decision-making.

**Decision classification**:
- Reversibility (easy to undo vs. permanent)
- Blast radius (one file vs. architecture)
- Stakes (experiment vs. production)

**Low-capacity mode**:
- Flag high-stakes decisions
- Offer "decide later" with context save
- Suggest smallest reversible version

**Priority**: Situational value.

---

## Implementation Priority

| Plugin | Impact | Effort | Order |
|--------|--------|--------|-------|
| `auto-checkpoint` | High | Medium | 1 |
| `capacity-inference` | High | High | 2 |
| `momentum-keeper` | Medium | Low | 3 |
| `time-anchor` | Medium | Medium | 4 |
| `decision-buffer` | Low | Low | 5 |

---

## WCAG 2.2 & COGA Alignment

Your existing skills align well with cognitive accessibility standards:

| Guideline | Status |
|-----------|--------|
| [3.2.6 Consistent Help][4] | Strong |
| [3.3.7 Redundant Entry][5] | Strong |
| [2.2.6 Timeouts][6] | Strong |
| [COGA: Clear Purpose][7] | Strong |

---

## Wellbeing Design Principles

Your skills demonstrate strong wellbeing orientation:

| Principle | Your Implementation |
|-----------|---------------------|
| **Autonomy** | Offer options, never mandate |
| **Competence** | Celebrate small completions |
| **Non-judgment** | Permission-giving language throughout |
| **Sustainability** | Rest framed as productive |

---

## References

1. Barkley, R.A. (2012). *Executive Functions: What They Are, How They Work, and Why They Evolved*. Guilford Press. https://www.guilford.com/books/Executive-Functions/Russell-Barkley/9781462505357

2. Kliegel, M., McDaniel, M.A., & Einstein, G.O. (Eds.). (2008). *Prospective Memory: Cognitive, Neuroscience, Developmental, and Applied Perspectives*. Psychology Press. https://www.taylorfrancis.com/books/mono/10.4324/9780203809945/prospective-memory-matthias-kliegel-mark-mcdaniel-gilles-einstein

3. Ptacek, R., Weissenberger, S., Braaten, E., et al. (2019). Clinical Implications of the Perception of Time in Attention Deficit Hyperactivity Disorder (ADHD): A Review. *Medical Science Monitor*, 25, 3918–3924. https://pmc.ncbi.nlm.nih.gov/articles/PMC6556068/

4. W3C. (2021). *Making Content Usable for People with Cognitive and Learning Disabilities*. W3C Working Group Note. https://www.w3.org/TR/coga-usable/

5. Miserandino, C. (2003). The Spoon Theory. *But You Don't Look Sick*. https://en.wikipedia.org/wiki/Spoon_theory

[1]: https://www.guilford.com/books/Executive-Functions/Russell-Barkley/9781462505357
[2]: https://www.taylorfrancis.com/books/mono/10.4324/9780203809945/prospective-memory-matthias-kliegel-mark-mcdaniel-gilles-einstein
[3]: https://pmc.ncbi.nlm.nih.gov/articles/PMC6556068/
[4]: https://www.w3.org/WAI/WCAG22/Understanding/consistent-help
[5]: https://www.w3.org/WAI/WCAG22/Understanding/redundant-entry
[6]: https://www.w3.org/WAI/WCAG22/Understanding/timeouts
[7]: https://www.w3.org/TR/coga-usable/
