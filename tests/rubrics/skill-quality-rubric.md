# Skill Quality Evaluation Rubric

This rubric defines the criteria for evaluating Claude Code skills. It combines automated technical validation with LLM-as-judge qualitative assessment.

## Scoring System

Each criterion is scored on a 0-4 scale:
- **4 - Excellent**: Exceeds expectations, exemplary quality
- **3 - Good**: Meets expectations, solid quality
- **2 - Adequate**: Meets minimum requirements, room for improvement
- **1 - Poor**: Below expectations, needs significant improvement
- **0 - Failing**: Does not meet basic requirements

## Technical Validation (Automated)

### T1. YAML Frontmatter Validity
- **4**: Valid YAML, all required fields present, follows schema exactly
- **3**: Valid YAML, all required fields, minor formatting inconsistencies
- **2**: Valid YAML, missing optional fields
- **1**: Valid YAML but missing required fields
- **0**: Invalid YAML syntax

**Required fields**: `name`, `description`
**Optional fields**: `version`, `tags`, `author`

### T2. Markdown Structure Validity
- **4**: Perfect markdown, no linting errors, consistent formatting
- **3**: Valid markdown, 1-2 minor linting warnings
- **2**: Valid markdown, 3-5 linting warnings
- **1**: Valid markdown, 6+ linting warnings
- **0**: Invalid markdown syntax

### T3. Link Integrity
- **4**: All links valid and working (internal and external)
- **3**: All internal links valid, 1-2 external links may be stale
- **2**: 1-2 broken internal links
- **1**: 3+ broken internal links
- **0**: Critical links broken (referenced skills, documentation)

### T4. File Structure Compliance
- **4**: Follows spec exactly, all optional directories used appropriately
- **3**: Follows spec, appropriate use of optional directories
- **2**: Follows spec, no optional directories when they'd add value
- **1**: Missing SKILL.md or incorrect placement
- **0**: Does not follow agent skills spec structure

**Expected structure**:
```
skill-name/
  ├── SKILL.md          # Required
  ├── scripts/          # Optional
  ├── references/       # Optional
  └── assets/           # Optional
```

## Qualitative Assessment (LLM-as-Judge)

### Q1. Clarity of Purpose
*Does the skill clearly communicate what it does and when to use it?*

- **4**: Crystal clear purpose, trigger phrases perfectly matched to use case
- **3**: Clear purpose, good trigger phrases, minor ambiguity
- **2**: Purpose stated but could be clearer, trigger phrases generic
- **1**: Purpose unclear or buried, trigger phrases missing/poor
- **0**: Purpose unstated or incomprehensible

**Evaluation criteria**:
- Description field is specific and actionable
- "When to Use" section includes concrete trigger phrases
- Examples clearly illustrate the use case
- No confusion about what problem this skill solves

### Q2. Instructional Completeness
*Does the skill provide sufficient guidance for effective use?*

- **4**: Comprehensive guidance covering all scenarios, with examples
- **3**: Good coverage of common scenarios, examples present
- **2**: Basic coverage, missing edge cases, few examples
- **1**: Incomplete guidance, significant gaps
- **0**: Insufficient guidance to use the skill

**Evaluation criteria**:
- Core approach/principles clearly explained
- Techniques include concrete examples
- Edge cases and variations addressed
- Success indicators provided

### Q3. Actionability
*Can an LLM agent execute this skill effectively based on the instructions?*

- **4**: Highly actionable, specific patterns/templates, clear decision points
- **3**: Actionable, good patterns, most decisions clear
- **2**: Somewhat actionable, vague in places, missing decision guidance
- **1**: Difficult to action, mostly abstract concepts
- **0**: Not actionable, no concrete guidance

**Evaluation criteria**:
- Includes templates, patterns, or concrete examples
- Decision points are explicit (when to do X vs Y)
- Steps are specific enough to follow
- Outputs/deliverables are clearly defined

### Q4. Internal Consistency
*Is the skill internally consistent and well-organized?*

- **4**: Perfect consistency, logical flow, no contradictions
- **3**: Consistent, good flow, minor organizational issues
- **2**: Mostly consistent, some organizational confusion
- **1**: Inconsistent terminology or contradictory guidance
- **0**: Major inconsistencies or contradictions

**Evaluation criteria**:
- Terminology used consistently throughout
- Sections follow logical progression
- Examples align with stated principles
- No contradictory advice

### Q5. Anti-Pattern Awareness
*Does the skill explicitly address common mistakes and pitfalls?*

- **4**: Comprehensive anti-patterns section with clear rationale
- **3**: Good anti-patterns section covering main pitfalls
- **2**: Anti-patterns mentioned but underdeveloped
- **1**: Anti-patterns absent or token mention only
- **0**: No anti-pattern guidance

**Evaluation criteria**:
- Dedicated anti-patterns section exists
- Common mistakes explicitly called out
- Rationale for why they're problematic
- Alternative approaches suggested

### Q6. Integration Awareness
*Does the skill acknowledge its relationship to other skills and contexts?*

- **4**: Excellent integration guidance, cross-references, boundary clarity
- **3**: Good integration section, relevant cross-references
- **2**: Basic integration mention, few cross-references
- **1**: Integration barely mentioned
- **0**: No integration guidance

**Evaluation criteria**:
- "Relationship to Other Skills" section present
- Appropriate cross-references to related skills
- Clear boundaries (when to use this vs another skill)
- Handoff patterns to other skills when needed

### Q7. Example Quality
*Are examples concrete, realistic, and illustrative?*

- **4**: Excellent examples, diverse scenarios, deeply illustrative
- **3**: Good examples covering main use cases
- **2**: Basic examples, somewhat generic
- **1**: Poor examples or too abstract
- **0**: No examples or completely unhelpful

**Evaluation criteria**:
- Examples are concrete (not placeholder-heavy)
- Examples show realistic scenarios
- Examples illustrate key concepts effectively
- Sufficient variety of examples

### Q8. Cognitive Load Management
*Is the skill easy to understand and remember?*

- **4**: Excellent structure, scannable, memorable patterns, clear hierarchy
- **3**: Good structure, mostly scannable, clear sections
- **2**: Acceptable structure, some density or confusion
- **1**: Poor structure, hard to navigate, overwhelming
- **0**: Incomprehensible structure or extreme cognitive load

**Evaluation criteria**:
- Clear section hierarchy
- Scannable headings and formatting
- Information chunked appropriately
- Not overwhelming in length or complexity
- Key patterns are memorable/distinctive

## Composite Scoring

### Technical Score
Average of T1-T4 (must be ≥ 3.0 to pass)

### Qualitative Score
Average of Q1-Q8 (must be ≥ 2.5 to pass)

### Overall Score
Weighted average: (Technical × 0.3) + (Qualitative × 0.7)

### Pass/Fail Thresholds
- **Excellent** (3.5+): Ready for production, exemplary quality
- **Good** (3.0-3.49): Ready for production
- **Needs Improvement** (2.5-2.99): Address issues before merge
- **Poor** (<2.5): Significant rework required

## Regression Detection

For existing skills being modified:
- **Critical regression**: Overall score drops by ≥0.5 points
- **Warning**: Overall score drops by ≥0.3 points
- **Minor regression**: Overall score drops by <0.3 points

CI should fail on critical regressions and warn on all regressions.
