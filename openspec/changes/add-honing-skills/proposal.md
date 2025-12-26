# Change: Add Honing Skills Meta-Cognitive Capability

## Why

Users working on complex tasks often need to strategically combine multiple Agent Skills to achieve their goals, but lack guidance on when and how to invoke different skills together. A meta-cognitive skill that serves as a "context attractor" can help users develop strategic thinking about skill selection, sequencing, and combination patterns.

This capability fills the gap between having individual specialized skills and knowing how to orchestrate them effectively for sophisticated workflows.

## What Changes

- **Add new capability**: `honing-skills` - A meta-cognitive skill that teaches strategic skill utilization
- **Skill structure**: Create `honing-skills/SKILL.md` with guidance on skill orchestration patterns
- **Marketplace update**: Add to `exec-func-skills` plugin in marketplace.json
- **Documentation**: Provide skill combination patterns, sequencing strategies, and decision frameworks

The skill will focus on:
- Recognizing when multiple skills should work together
- Understanding skill interaction patterns (sequential, parallel, nested)
- Building mental models for strategic skill deployment
- Developing meta-awareness about cognitive tool selection

## Impact

- **Affected specs**: New capability `honing-skills`
- **Affected code**:
  - New file: `honing-skills/SKILL.md`
  - Modified: `.claude-plugin/marketplace.json` (add skill to exec-func-skills plugin)
- **User benefit**: Enhanced ability to tackle complex multi-faceted tasks through strategic skill orchestration
- **Complementary skills**: Works alongside all existing skills by providing meta-cognitive guidance
