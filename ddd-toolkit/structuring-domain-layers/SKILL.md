# Structuring Domain Layers

## Description

Isolates the domain model and business logic from infrastructural concerns like UI and database access, preventing logic diffusion across technical layers.

## When to Use

- Starting a new bounded context or service
- Refactoring legacy code where business logic is scattered across UI, database, or infrastructure code
- Team members struggle to locate domain logic
- Domain objects are coupled to persistence or presentation frameworks
- Need to prevent domain model from being polluted by technical concerns

## Key Goal

Create a clear architectural boundary that concentrates all domain-related code in one layer, allowing domain objects to focus purely on expressing the business model without responsibilities for storing, displaying, or transmitting themselves.

## Quick Start

1. Identify domain logic currently mixed with infrastructure
2. Define layer boundaries (typically: UI/Presentation, Application, Domain, Infrastructure)
3. Move domain logic into the Domain layer
4. Ensure dependencies flow inward (outer layers depend on inner layers)
5. Review for domain logic leakage into other layers

## Detailed Reference

For comprehensive guidance on layered architecture patterns and isolation strategies, see `LayeredArchitecture.md` in this directory.

## Related Skills

- **[Modeling Building Blocks](../modeling-building-blocks/SKILL.md)**: Implement tactical patterns within the isolated domain layer
- **[Crafting Core Domain](../crafting-core-domain/SKILL.md)**: Focus effort on the Core Domain within your layers
- **[Mapping Strategic Contexts](../mapping-strategic-contexts/SKILL.md)**: Apply layered architecture within each Bounded Context

## Related Concepts

- Hexagonal Architecture (Ports and Adapters)
- Clean Architecture
- Onion Architecture
