---
name: crafting-core-domain
description: Focuses development effort on the Core Domain (the heart of business value) and ensures the entire team speaks the Ubiquitous Language, aligning code with business priorities through strategic distillation. Use when prioritizing features or refactoring work, when team members use inconsistent terminology, when unclear which parts provide competitive advantage, when communicating domain vision to stakeholders, when the domain model has accumulated complexity, or when starting new projects.
---

# Crafting Core Domain

## Description

Focuses development effort on the Core Domain—the heart of business value—and ensures the entire team speaks the Ubiquitous Language. Aligns code with business priorities through strategic distillation.

## When to Use

- Prioritizing features or refactoring work
- Team members use inconsistent terminology
- Unclear which parts of the system provide competitive advantage
- Need to communicate domain vision to stakeholders
- Domain model has accumulated complexity that obscures key concepts
- Starting a new project and defining the most valuable areas

## Key Goal

Identify and invest in the Core Domain—the area that provides the most business value and competitive differentiation. Establish a shared Ubiquitous Language that allows technical and domain experts to collaborate effectively.

## Quick Start

1. Identify the Core Domain (what makes your business unique?)
2. Distinguish Supporting Subdomains (necessary but not differentiating)
3. Identify Generic Subdomains (commodity capabilities)
4. Establish Ubiquitous Language with domain experts
5. Create a Domain Vision Statement
6. Highlight Core elements in your codebase
7. Consider segregating Core from Supporting elements

## Core Concepts

- **Core Domain**: The central area of focus that provides business advantage
- **Supporting Subdomain**: Necessary for the business but not the primary value driver
- **Generic Subdomain**: Solved problems with off-the-shelf solutions
- **Ubiquitous Language**: Shared vocabulary used by everyone (developers and domain experts)
- **Domain Vision Statement**: Short description of the Core Domain's value
- **Highlighted Core**: Visual/structural emphasis on core elements
- **Segregated Core**: Refactoring to physically isolate core elements

## Detailed Reference

For comprehensive guidance on domain distillation, Ubiquitous Language, and strategic focus, see `CoreDistillation.md` in this directory.

## Related Skills

- **[Mapping Strategic Contexts](../mapping-strategic-contexts/SKILL.md)**: Map Core, Supporting, and Generic subdomains to Bounded Contexts
- **[Modeling Building Blocks](../modeling-building-blocks/SKILL.md)**: Implement the Core Domain using tactical patterns
- **[Refining Supple Design](../refining-supple-design/SKILL.md)**: Apply highest design standards to Core Domain code

## Related Concepts

- Strategic Design
- Value Stream Mapping
- Business Capability Modeling
- Model-Driven Design
