# Mapping Strategic Contexts

## Description

Identifies Bounded Contexts and defines their relationships using a Context Map. Enables large-scale project organization by making integration patterns and team boundaries explicit.

## When to Use

- Integrating multiple subsystems or microservices
- Model terms have different meanings across team boundaries
- Multiple teams are working on interdependent systems
- Confusion exists about integration responsibilities
- Planning how new contexts will interact with existing systems
- Need to document and communicate system landscape

## Key Goal

Make explicit boundaries for each model in play and define the relationships between contexts using well-understood patterns. This prevents models from bleeding into each other and clarifies integration strategies.

## Quick Start

1. Identify distinct Bounded Contexts (usually aligned with team/subdomain boundaries)
2. Name each context clearly
3. Map relationships between contexts
4. Choose appropriate integration patterns for each relationship
5. Document the Context Map visually
6. Use the map to guide architectural and organizational decisions

## Context Relationship Patterns

- **Partnership** - Mutually dependent teams coordinating closely
- **Shared Kernel** - Small shared subset of model code
- **Customer/Supplier** - Downstream (customer) influences upstream (supplier) planning
- **Conformist** - Downstream strictly adheres to upstream model
- **Anticorruption Layer** - Translation layer protecting downstream from upstream changes
- **Open-host Service** - Published protocol/API for access
- **Separate Ways** - No integration; teams work independently

## Detailed Reference

For comprehensive guidance on Context Mapping strategies, integration patterns, and team dynamics, see `ContextMap.md` in this directory.

## Related Concepts

- Strategic Design
- System Integration
- Conway's Law
- Microservices Architecture
