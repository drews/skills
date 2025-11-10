# Domain-Driven Design Toolkit

A comprehensive collection of Claude Agent Skills that operationalize Eric Evans' Domain-Driven Design pattern language into actionable, discoverable capabilities.

## Overview

This toolkit transforms DDD theory into five focused, practical skills covering the complete spectrum from strategic design to tactical implementation to continuous refinement. Each skill follows the progressive disclosure pattern: a concise SKILL.md entry point paired with a comprehensive reference document.

## The Five Skills

### 1. 🏗️ [Structuring Domain Layers](./structuring-domain-layers/SKILL.md)

**Isolates the domain model from infrastructure concerns**

- Layered Architecture patterns
- Hexagonal Architecture (Ports and Adapters)
- Dependency inversion for clean boundaries
- Preventing logic diffusion across technical layers

📚 [Detailed Reference: LayeredArchitecture.md](./structuring-domain-layers/LayeredArchitecture.md)

---

### 2. 🗺️ [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

**Defines boundaries and relationships between Bounded Contexts**

- Bounded Context identification
- Context Map creation
- 7 relationship patterns: Partnership, Shared Kernel, Customer/Supplier, Conformist, Anticorruption Layer, Open-Host Service, Separate Ways
- Strategic integration guidance

📚 [Detailed Reference: ContextMap.md](./mapping-strategic-contexts/ContextMap.md)

---

### 3. 🎯 [Crafting Core Domain](./crafting-core-domain/SKILL.md)

**Focuses effort on the most valuable domain areas**

- Ubiquitous Language establishment
- Core Domain vs. Supporting vs. Generic subdomain distinction
- Domain Vision Statement
- Distillation techniques: Highlighted Core, Segregated Core, Abstract Core
- Hands-On Modelers and Model-Driven Design

📚 [Detailed Reference: CoreDistillation.md](./crafting-core-domain/CoreDistillation.md)

---

### 4. 🧱 [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

**Implements tactical patterns within a Bounded Context**

- Entities vs. Value Objects (with decision workflow)
- Aggregates and consistency boundaries
- Repositories for persistence abstraction
- Domain Services, Domain Events, Factories
- Modules for code organization

📚 [Detailed Reference: TacticalBuildingBlocks.md](./modeling-building-blocks/TacticalBuildingBlocks.md)

---

### 5. ✨ [Refining Supple Design](./refining-supple-design/SKILL.md)

**Creates flexible, expressive domain models through iterative refinement**

- Intention-Revealing Interfaces
- Side-Effect-Free Functions
- Assertions (Design by Contract)
- Closure of Operations
- Declarative Design
- Conceptual Contours
- Refactoring Toward Deeper Insight

📚 [Detailed Reference: SuppleDesignPatterns.md](./refining-supple-design/SuppleDesignPatterns.md)

---

## Quick Navigation by Need

### "I'm starting a new project..."
1. Start with [Crafting Core Domain](./crafting-core-domain/SKILL.md) to identify your Core Domain
2. Use [Structuring Domain Layers](./structuring-domain-layers/SKILL.md) to set up architectural boundaries
3. Apply [Modeling Building Blocks](./modeling-building-blocks/SKILL.md) to implement your model

### "I'm working with multiple teams/systems..."
1. Use [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md) to define Bounded Contexts
2. Create a Context Map to clarify integration patterns
3. Apply [Structuring Domain Layers](./structuring-domain-layers/SKILL.md) within each context

### "My code is hard to change..."
1. Review [Refining Supple Design](./refining-supple-design/SKILL.md) for design principles
2. Apply Intention-Revealing Interfaces and Side-Effect-Free Functions
3. Refactor along Conceptual Contours

### "Should this be an Entity or Value Object?"
- Use the decision workflow in [Modeling Building Blocks](./modeling-building-blocks/SKILL.md#conditional-workflow-entity-vs-value-object)

### "How should our contexts integrate?"
- See the relationship patterns in [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md#context-relationship-patterns)

---

## Learning Path

### Beginner Path (New to DDD)
1. **Crafting Core Domain** - Understand Ubiquitous Language and Core vs. Supporting
2. **Modeling Building Blocks** - Learn Entities, Value Objects, Aggregates
3. **Structuring Domain Layers** - Isolate domain from infrastructure
4. **Refining Supple Design** - Apply quality principles

### Intermediate Path (Implementing DDD)
1. **Structuring Domain Layers** - Establish clean architecture
2. **Modeling Building Blocks** - Implement tactical patterns
3. **Refining Supple Design** - Improve design quality
4. **Mapping Strategic Contexts** - Handle multiple contexts

### Advanced Path (Scaling DDD)
1. **Mapping Strategic Contexts** - Define system landscape
2. **Crafting Core Domain** - Strategic distillation
3. **Refining Supple Design** - Advanced refactoring
4. **Modeling Building Blocks** - Complex Aggregate design

---

## Key Concepts Glossary

For a comprehensive glossary of all DDD terms, see [GLOSSARY.md](./GLOSSARY.md).

**Quick Reference:**
- **Aggregate**: Cluster of objects treated as a unit for consistency
- **Bounded Context**: Explicit boundary for a model and its Ubiquitous Language
- **Core Domain**: The most valuable, differentiating part of the business
- **Entity**: Object with unique identity and lifecycle
- **Repository**: Abstraction for object persistence
- **Ubiquitous Language**: Shared vocabulary used by everyone
- **Value Object**: Immutable object describing characteristics

---

## File Structure

```
ddd-toolkit/
├── README.md (this file)
├── GLOSSARY.md
├── structuring-domain-layers/
│   ├── SKILL.md
│   └── LayeredArchitecture.md
├── mapping-strategic-contexts/
│   ├── SKILL.md
│   └── ContextMap.md
├── crafting-core-domain/
│   ├── SKILL.md
│   └── CoreDistillation.md
├── modeling-building-blocks/
│   ├── SKILL.md
│   └── TacticalBuildingBlocks.md
└── refining-supple-design/
    ├── SKILL.md
    └── SuppleDesignPatterns.md
```

---

## Coverage Map: Eric Evans' DDD Book

| Book Part | Covered By | Files |
|-----------|------------|-------|
| Part I: Putting the Domain Model to Work | Crafting Core Domain | SKILL.md, CoreDistillation.md |
| Part II: Building Blocks of a Model-Driven Design | Structuring Domain Layers, Modeling Building Blocks | LayeredArchitecture.md, TacticalBuildingBlocks.md |
| Part III: Refactoring Toward Deeper Insight | Refining Supple Design | SuppleDesignPatterns.md |
| Part IV: Strategic Design | Mapping Strategic Contexts | ContextMap.md |
| Part V: Distillation | Crafting Core Domain | CoreDistillation.md |

---

## Usage with Claude

Each skill can be invoked by referencing its SKILL.md file. Claude will load the concise overview and can then access the detailed reference file as needed.

**Example interaction:**
```
User: "I'm designing an order system. Should Order be an Entity or Value Object?"
Claude: [Loads modeling-building-blocks/SKILL.md]
        [References the Entity vs. Value Object decision workflow]
        [Provides guidance based on TacticalBuildingBlocks.md]
```

---

## Contributing

When extending this toolkit:
1. Follow the two-file pattern (SKILL.md + detailed reference)
2. Use gerund form for skill directory names
3. Include: Description, When to Use, Key Goal, Quick Start, Detailed Reference
4. Provide code examples with before/after comparisons
5. Add success indicators and common pitfalls
6. Cross-reference related skills

---

## Further Reading

- Eric Evans, *Domain-Driven Design: Tackling Complexity in the Heart of Software* (2003)
- Vaughn Vernon, *Implementing Domain-Driven Design* (2013)
- Vaughn Vernon, *Domain-Driven Design Distilled* (2016)
- Martin Fowler, *Patterns of Enterprise Application Architecture* (2002)

---

## License

This toolkit is designed for use with Claude Agent SDK as educational and practical guidance for implementing Domain-Driven Design patterns.

---

**Version**: 1.0
**Last Updated**: 2025-11-10
**Total Lines**: 3,498+ lines of comprehensive DDD guidance
