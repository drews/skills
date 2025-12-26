## ADDED Requirements

### Requirement: Meta-Cognitive Skill Recognition
The skill SHALL help users recognize when complex tasks require strategic combination of multiple Agent Skills rather than single-skill approaches.

#### Scenario: Multi-faceted task identification
- **WHEN** user presents a task that has multiple dimensions (planning, execution, capacity assessment)
- **THEN** skill identifies which component skills are relevant and suggests an orchestration strategy

#### Scenario: Overwhelmed by complexity
- **WHEN** user expresses feeling overwhelmed by a task with many moving parts
- **THEN** skill breaks down the complexity into skill-addressable components and provides a strategic approach

### Requirement: Skill Orchestration Patterns
The skill SHALL provide concrete patterns for combining skills in sequential, parallel, and nested configurations.

#### Scenario: Sequential skill application
- **WHEN** task requires multi-stage progression (e.g., planning then execution then review)
- **THEN** skill guides user through sequential skill invocation with clear transitions between stages

#### Scenario: Parallel skill considerations
- **WHEN** task has independent concerns that can be addressed simultaneously
- **THEN** skill identifies opportunities for parallel skill application and explains coordination points

#### Scenario: Nested skill relationships
- **WHEN** one skill naturally contains decision points that trigger other skills
- **THEN** skill explains the nesting structure and how to navigate between layers

### Requirement: Strategic Decision Framework
The skill SHALL provide decision-making frameworks for selecting appropriate skills based on task characteristics, user state, and desired outcomes.

#### Scenario: Task characteristic analysis
- **WHEN** user describes a task or goal
- **THEN** skill analyzes key characteristics (complexity, urgency, energy requirement, clarity) and recommends skill combinations

#### Scenario: Capacity-aware skill selection
- **WHEN** user indicates current capacity constraints (low energy, time pressure, cognitive load)
- **THEN** skill adjusts recommendations to match available capacity and suggests appropriate skill sequences

#### Scenario: Outcome-driven skill mapping
- **WHEN** user specifies desired outcome without knowing how to achieve it
- **THEN** skill works backwards from outcome to identify skill pathway and key decision points

### Requirement: Mental Model Development
The skill SHALL build user's mental models for strategic skill deployment through explicit reasoning and pattern recognition.

#### Scenario: Pattern identification teaching
- **WHEN** user successfully applies a skill combination
- **THEN** skill highlights the pattern used and when it applies more broadly

#### Scenario: Meta-awareness cultivation
- **WHEN** user is learning to use skills strategically
- **THEN** skill provides explicit meta-commentary on skill selection reasoning

#### Scenario: Common pitfall avoidance
- **WHEN** user approaches a task in a way that's likely to fail
- **THEN** skill identifies the pitfall and suggests alternative skill-based approaches

### Requirement: Context Attractor Mechanism
The skill SHALL serve as a "context attractor" by centralizing meta-cognitive strategy and tactics for skill utilization, making it the natural entry point for complex multi-skill scenarios.

#### Scenario: Entry point for complexity
- **WHEN** user faces a complex task and isn't sure where to start
- **THEN** skill becomes the natural first stop to develop a strategic approach

#### Scenario: Reference point for skill ecosystem
- **WHEN** user wants to understand how different skills relate and interact
- **THEN** skill provides map of skill ecosystem with relationship patterns

### Requirement: Skill Combination Library
The skill SHALL maintain a library of proven skill combination patterns for common complex scenarios.

#### Scenario: Interrupted project recovery
- **WHEN** user needs to resume work after interruption
- **THEN** skill recommends `saving-progress` → `getting-started` → domain-specific skill sequence

#### Scenario: Overwhelming new project
- **WHEN** user faces large ambiguous project
- **THEN** skill recommends `sensing-limits` → `getting-started` → iterative execution pattern

#### Scenario: Capacity-constrained execution
- **WHEN** user wants to make progress despite low energy or limited time
- **THEN** skill recommends `sensing-limits` → filtered `getting-started` → minimal viable action approach

### Requirement: Skill Interaction Documentation
The skill SHALL document how different skills interact, including which skills naturally lead to others and potential conflict patterns.

#### Scenario: Natural skill transitions
- **WHEN** user completes one skill's guidance
- **THEN** skill identifies logical next skills and transition criteria

#### Scenario: Skill conflict awareness
- **WHEN** user might invoke conflicting skill approaches
- **THEN** skill identifies the conflict and suggests resolution strategy
