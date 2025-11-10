# Modeling Building Blocks

## Description

Guides the creation of tactical domain elements within a Bounded Context, distinguishing Entities by identity, Value Objects by attributes, and structuring complex objects into Aggregates for consistency.

## When to Use

- Designing classes and object relationships
- Deciding whether something should be an Entity or Value Object
- Defining consistency boundaries in the domain model
- Implementing domain logic and business rules
- Structuring data access patterns
- Modeling domain processes that don't fit naturally into objects
- Capturing important domain events

## Key Goal

Create a rich, expressive domain model using proven tactical patterns that clearly distinguish identity-based objects (Entities) from descriptive objects (Value Objects), enforce consistency through Aggregates, and provide clean abstractions for storage (Repositories) and processes (Services).

## Quick Start

1. Identify core concepts in your domain
2. Ask: "Does this need an identity?" → Entity (yes) or Value Object (no)
3. Group related Entities and Value Objects into Aggregates
4. Define one Entity as the Aggregate Root
5. Create Repositories only for Aggregate Roots
6. Model processes as Domain Services when they don't fit into Entities/Value Objects
7. Capture significant domain events as Domain Events

## Tactical Patterns

- **Entities**: Objects with unique identity and lifecycle
- **Value Objects**: Immutable objects describing characteristics
- **Aggregates**: Clusters of objects treated as a unit for consistency
- **Repositories**: Abstraction for object persistence and retrieval
- **Domain Services**: Operations that don't naturally belong to Entities/Value Objects
- **Domain Events**: Records of something that happened in the domain
- **Factories**: Encapsulate complex object creation
- **Modules**: Group cohesive domain concepts

## Conditional Workflow: Entity vs. Value Object

Use the decision tree in `TacticalBuildingBlocks.md` to determine whether a concept should be modeled as an Entity or a Value Object.

## Detailed Reference

For comprehensive guidance on all tactical patterns, implementation examples, and design principles, see `TacticalBuildingBlocks.md` in this directory.

## Related Skills

- **[Structuring Domain Layers](../structuring-domain-layers/SKILL.md)**: Isolate domain model before implementing tactical patterns
- **[Refining Supple Design](../refining-supple-design/SKILL.md)**: Improve design quality of Entities, Value Objects, and Services
- **[Mapping Strategic Contexts](../mapping-strategic-contexts/SKILL.md)**: Ensure Aggregates don't cross Bounded Context boundaries

## Related Concepts

- Object-Oriented Design
- Aggregate Pattern
- Repository Pattern
- Domain Model Pattern
