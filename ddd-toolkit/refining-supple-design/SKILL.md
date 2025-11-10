# Refining Supple Design

## Description

Applies iterative refinement patterns to create domain models that are easy to understand, easy to change, and reveal deep domain insights. Implements code rigor through intention-revealing interfaces, side-effect-free functions, and assertions.

## When to Use

- Code is hard to understand or reason about
- Making changes is risky or fragile
- Domain model feels awkward or doesn't reflect natural domain concepts
- After implementing initial tactical patterns (Entities, Value Objects, Aggregates)
- During refactoring to improve design quality
- When domain understanding has deepened since initial implementation

## Key Goal

Create a "supple" design—one that invites change, clearly reveals its intent, and makes complex interactions easy to anticipate. Achieve this through continuous refactoring toward deeper insight and applying rigorous design principles.

## Quick Start

1. Review existing domain model for clarity and flexibility
2. Apply Intention-Revealing Interfaces: Rename methods/classes to clearly express purpose
3. Make operations Side-Effect-Free when possible
4. Add Assertions to make pre/post-conditions and invariants explicit
5. Seek Closure of Operations where domain-appropriate
6. Refactor along Conceptual Contours (stable axes of change)
7. Iterate: Refactor Toward Deeper Insight as domain knowledge grows

## Core Patterns

- **Intention-Revealing Interfaces**: Names that describe purpose, not mechanism
- **Side-Effect-Free Functions**: Operations that return results without observable side effects
- **Assertions**: Explicit pre-conditions, post-conditions, and invariants
- **Closure of Operations**: Operations where output type matches input type
- **Declarative Design**: Express logic in declarative style where possible
- **Conceptual Contours**: Align decomposition with stable domain concepts
- **Refactoring Toward Deeper Insight**: Continuous refinement as understanding deepens

## Detailed Reference

For comprehensive guidance on supple design principles, refactoring patterns, and code quality practices, see `SuppleDesignPatterns.md` in this directory.

## Related Skills

- **[Modeling Building Blocks](../modeling-building-blocks/SKILL.md)**: Apply supple design to Entities, Value Objects, and Services
- **[Crafting Core Domain](../crafting-core-domain/SKILL.md)**: Focus highest design effort on Core Domain elements
- **[Structuring Domain Layers](../structuring-domain-layers/SKILL.md)**: Maintain clean boundaries while refactoring

## Related Concepts

- Refactoring
- Clean Code
- Design Patterns
- Functional Programming Principles
- Ubiquitous Language
