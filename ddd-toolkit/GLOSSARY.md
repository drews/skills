# Domain-Driven Design Glossary

A comprehensive reference of all Domain-Driven Design terms, patterns, and concepts covered in this toolkit.

---

## Core Concepts

### Bounded Context
An explicit boundary within which a particular domain model is defined and applicable. Each Bounded Context has its own Ubiquitous Language and maintains internal model consistency. Often aligns with team boundaries and may be implemented as a microservice or module.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Core Domain
The part of the model that makes the system worth building and provides competitive advantage. The area of the business that is most valuable and differentiating, deserving the highest investment in design quality and the best developers.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

### Domain
The subject area to which the application is applied. The sphere of knowledge and activity around which the application logic revolves.

### Model-Driven Design
The practice of binding model and implementation tightly together, where the design and code directly reflect and express the domain model. Code is treated as a representation of the model, not just mechanism.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

### Ubiquitous Language
A language structured around the domain model and used by all team members (developers, domain experts, stakeholders) to connect all activities with the software. Class names, method names, and conversations all use the same terminology.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

---

## Strategic Design

### Abstract Core
Identifying the most fundamental concepts in the Core Domain and defining them in abstract form (interfaces or abstract classes), keeping the core conceptually pure while allowing variations in implementation.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

### Anticorruption Layer (ACL)
A translation layer that isolates a downstream context from an upstream model, preventing upstream concepts from "corrupting" the downstream model. Allows the downstream model to evolve independently.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Conformist
A context relationship where the downstream context strictly conforms to the upstream context's model without any translation layer. Simplifies integration but creates tight coupling.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Context Map
A document showing all Bounded Contexts in a system and the relationships between them. Serves as a communication tool, integration guide, and organizational alignment mechanism.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Customer/Supplier Development
A context relationship where the downstream team (customer) depends on the upstream team (supplier), but the downstream has influence over the upstream's priorities through negotiation.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Domain Vision Statement
A short description (typically one page) of the Core Domain and the value proposition it provides. Focuses on the business problem being solved and what makes the system unique.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

### Generic Subdomain
Subdomains for which off-the-shelf solutions exist and should be used. Solve universal, well-understood problems and provide no competitive advantage (e.g., authentication, email delivery, payment processing).

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

### Highlighted Core
Flagging the essential Core Domain elements in documentation and code to make it obvious where the most important logic lives and where to invest design effort.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

### Open-Host Service
A context relationship where the upstream defines a public protocol or API designed to serve multiple downstream consumers. Often paired with Published Language.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Partnership
A context relationship where two teams coordinate closely in a cooperative relationship for mutual success. Neither can succeed without the other.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Published Language
A well-documented, standardized data format used for communication between contexts (e.g., JSON schema, Protocol Buffers, XML schema). Often used with Open-Host Service.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Segregated Core
Refactoring to physically isolate the Core Domain from supporting and generic elements through separate modules, packages, or services.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

### Separate Ways
A context relationship where two contexts have no integration at all and operate completely independently.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Shared Kernel
A subset of the domain model that is shared explicitly between two or more contexts. Changes to the Shared Kernel require consensus from all teams. Should be kept as small as possible.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Supporting Subdomain
Subdomains that are necessary for the business but don't provide the primary competitive advantage. Essential to operations but can be developed with less rigor than the Core Domain.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

---

## Architectural Patterns

### Layered Architecture
Partitioning a complex program into cohesive layers (typically: User Interface, Application, Domain, Infrastructure) that depend only on layers below. Concentrates domain logic in the Domain layer.

**Related Skills**: [Structuring Domain Layers](./structuring-domain-layers/SKILL.md)

### Hexagonal Architecture (Ports and Adapters)
An architectural pattern emphasizing the domain/application core at the center, with "ports" (interfaces) for external interactions and "adapters" implementing ports for specific technologies.

**Related Skills**: [Structuring Domain Layers](./structuring-domain-layers/SKILL.md)

### Application Layer
A thin layer that orchestrates domain objects and manages transactions, security, and coordination. Does not contain business logic or maintain domain state.

**Related Skills**: [Structuring Domain Layers](./structuring-domain-layers/SKILL.md)

### Domain Layer
The layer representing business concepts, rules, and state. Contains Entities, Value Objects, Aggregates, and Domain Services. Isolated from technical concerns.

**Related Skills**: [Structuring Domain Layers](./structuring-domain-layers/SKILL.md)

### Infrastructure Layer
The layer providing technical capabilities (persistence, messaging, external services) that support the layers above.

**Related Skills**: [Structuring Domain Layers](./structuring-domain-layers/SKILL.md)

---

## Tactical Patterns (Building Blocks)

### Aggregate
A cluster of associated Entities and Value Objects treated as a unit for data consistency. Has one Entity as the Aggregate Root and enforces consistency rules within its boundary.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Aggregate Root
The single Entity within an Aggregate that serves as the entry point. External objects can only reference the Aggregate through its Root, which enforces all consistency rules.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Domain Event
An immutable record of something significant that happened in the domain. Named in past tense (e.g., OrderConfirmed, PaymentProcessed). Enables decoupling between Aggregates and supports eventual consistency.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Domain Service
A stateless operation that implements domain logic when the behavior doesn't naturally fit into an Entity or Value Object. Represents significant processes or transformations.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Entity
An object defined primarily by its unique identity rather than attributes. Identity persists throughout the object's lifecycle even as attributes change. Two Entities with the same attributes but different identities are distinct.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Factory
An object or method that encapsulates complex creation of Aggregates or Value Objects. Used when object creation involves business rules, validation, or consultation of other objects.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Module (Package)
A grouping of cohesive domain concepts that reflects the domain language. Used to organize code into manageable units with high cohesion within modules and low coupling between them.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Repository
Provides the illusion of an in-memory collection of Aggregate Roots. Encapsulates data access logic and abstracts away database/storage details. One Repository per Aggregate Root, not for internal Entities.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Value Object
An immutable object that describes characteristics or attributes without conceptual identity. Defined entirely by its attributes; two Value Objects with identical attributes are interchangeable. Can be freely shared and copied.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

---

## Supple Design Patterns

### Assertion
Explicitly stating pre-conditions, post-conditions, and invariants through guard clauses, constructor validation, or documentation. Makes expectations and contracts explicit.

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

### Closure of Operations
Defining operations where the return type matches the argument type(s), enabling natural chaining and composition (e.g., Money + Money → Money).

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

### Command-Query Separation (CQS)
The principle that methods should either change state (command, return void) or return a value (query, no side effects), but not both.

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

### Conceptual Contours
Decomposing design elements along axes that align with the domain's natural concepts and stable areas of change. Classes and methods reflect how domain experts think.

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

### Continuous Integration (of the Model)
Frequently merging and harmonizing domain models as the team learns, through regular synchronization of code and model understanding.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

### Declarative Design
Expressing logic in a style that describes *what* rather than *how*, emphasizing intent over implementation mechanism.

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

### Hands-On Modelers
The practice of having technical people who build the software also participate in modeling. Ensures models are practical, implementable, and benefit from immediate feedback.

**Related Skills**: [Crafting Core Domain](./crafting-core-domain/SKILL.md)

### Intention-Revealing Interface
Naming classes and operations to describe their purpose rather than their implementation mechanism. Names use Ubiquitous Language and are meaningful to domain experts.

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

### Refactoring Toward Deeper Insight
Continuously refining the model as domain understanding deepens through implementation, conversation with experts, and emergence of new patterns.

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

### Side-Effect-Free Function
Operations that return results without causing observable side effects. Same inputs always produce same outputs. Enables composition, parallelization, and predictability.

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

### Supple Design
A design that is easy to understand, invites change, and makes complex interactions easy to anticipate. Achieved through intention-revealing interfaces, side-effect-free functions, and other quality patterns.

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

---

## Related Patterns and Concepts

### Anemic Domain Model
An anti-pattern where domain objects contain only data (getters/setters) with all business logic in services. Contrary to DDD principles of rich domain models.

### Conway's Law
"Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations." Context boundaries often align with organizational boundaries.

**Related Skills**: [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)

### Design by Contract
A design approach using explicit pre-conditions, post-conditions, and invariants to define contracts between software components.

**Related Skills**: [Refining Supple Design](./refining-supple-design/SKILL.md)

### Eventual Consistency
A consistency model where changes to one Aggregate are eventually propagated to other Aggregates asynchronously, often via Domain Events. Used to avoid multi-Aggregate transactions.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Immutability
The characteristic of objects that cannot be changed after creation. Value Objects are always immutable; creating a "modified" version returns a new instance.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

### Invariant
A condition that must always hold for an object or system. Aggregates enforce invariants within their boundaries.

**Related Skills**: [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)

---

## Quick Lookup: Pattern by Purpose

### "I need to organize multiple systems..."
- **Bounded Context**: Define model boundaries
- **Context Map**: Document relationships
- **Partnership, Shared Kernel, Customer/Supplier, etc.**: Choose integration pattern

### "I need to focus effort..."
- **Core Domain**: Identify what matters most
- **Supporting Subdomain**: Recognize necessary but not differentiating
- **Generic Subdomain**: Use off-the-shelf solutions
- **Domain Vision Statement**: Communicate strategic focus

### "I need to implement a model..."
- **Entity**: For objects with unique identity
- **Value Object**: For descriptive, immutable objects
- **Aggregate**: To enforce consistency boundaries
- **Repository**: To abstract persistence
- **Domain Service**: For operations not belonging to objects

### "I need to improve design quality..."
- **Intention-Revealing Interface**: Clear naming
- **Side-Effect-Free Function**: Predictable operations
- **Assertions**: Explicit contracts
- **Conceptual Contours**: Align with domain structure
- **Refactoring Toward Deeper Insight**: Continuous improvement

### "I need to isolate the domain..."
- **Layered Architecture**: Separate concerns by layer
- **Hexagonal Architecture**: Isolate core from external dependencies
- **Dependency Inversion**: Domain defines interfaces, infrastructure implements

---

## Alphabetical Index

- Abstract Core
- Aggregate
- Aggregate Root
- Anemic Domain Model
- Anticorruption Layer (ACL)
- Application Layer
- Assertion
- Bounded Context
- Closure of Operations
- Command-Query Separation
- Conceptual Contours
- Conformist
- Context Map
- Continuous Integration (of the Model)
- Conway's Law
- Core Domain
- Customer/Supplier Development
- Declarative Design
- Design by Contract
- Domain
- Domain Event
- Domain Layer
- Domain Service
- Domain Vision Statement
- Entity
- Eventual Consistency
- Factory
- Generic Subdomain
- Hands-On Modelers
- Hexagonal Architecture
- Highlighted Core
- Immutability
- Infrastructure Layer
- Intention-Revealing Interface
- Invariant
- Layered Architecture
- Model-Driven Design
- Module (Package)
- Open-Host Service
- Partnership
- Published Language
- Refactoring Toward Deeper Insight
- Repository
- Segregated Core
- Separate Ways
- Shared Kernel
- Side-Effect-Free Function
- Supporting Subdomain
- Supple Design
- Ubiquitous Language
- Value Object

---

## Further Reading

For detailed explanations and examples, navigate to the relevant skill:

- 🏗️ [Structuring Domain Layers](./structuring-domain-layers/SKILL.md)
- 🗺️ [Mapping Strategic Contexts](./mapping-strategic-contexts/SKILL.md)
- 🎯 [Crafting Core Domain](./crafting-core-domain/SKILL.md)
- 🧱 [Modeling Building Blocks](./modeling-building-blocks/SKILL.md)
- ✨ [Refining Supple Design](./refining-supple-design/SKILL.md)

---

**See also**: [README.md](./README.md) for toolkit overview and navigation
