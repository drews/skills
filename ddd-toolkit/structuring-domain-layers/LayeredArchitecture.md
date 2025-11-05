# Layered Architecture: Isolating the Domain Model

## Overview

A fundamental prerequisite for Domain-Driven Design is the isolation of the domain model from technical and infrastructural concerns. Without this isolation, domain logic becomes diffused throughout the codebase, making it difficult to evolve and maintain.

## The Problem: Logic Diffusion

In complex programs without clear architectural boundaries:
- Business rules leak into UI code, database queries, and API handlers
- Domain objects become coupled to persistence frameworks, display widgets, or transport protocols
- The same business logic gets duplicated across multiple technical layers
- Changes to domain concepts require coordinating changes across disparate parts of the codebase
- Domain experts cannot easily recognize their model in the code

## The Solution: Layered Architecture

**Partition a complex program into cohesive layers that depend only on the layers below.**

### Standard Layers

Most layered architectures organize into four conceptual layers:

#### 1. User Interface (Presentation Layer)
**Responsibility:** Display information to the user and interpret user commands.

- Handles HTTP requests/responses, CLI parsing, GUI rendering
- Presents data in formats appropriate for users
- Translates user input into application commands
- **Should NOT contain business logic**

#### 2. Application Layer
**Responsibility:** Coordinate application activity and delegate work to the domain layer.

- Thin layer that orchestrates domain objects
- Defines jobs the software is supposed to do
- Manages transactions and security
- Does not contain business rules or knowledge
- Does not maintain domain object state (delegates to domain layer)

Example responsibilities:
- "When user requests order checkout, validate payment, reserve inventory, and confirm order"
- The *how* of validation and reservation lives in the domain layer

#### 3. Domain Layer (Model Layer)
**Responsibility:** Represent concepts of the business, business rules, and business state.

- **The heart of business software**
- Contains Entities, Value Objects, Aggregates, Domain Services
- Expresses business rules and invariants
- Manages business state and behavior
- **Isolated from technical concerns** (no database queries, no HTTP, no file I/O)

#### 4. Infrastructure Layer
**Responsibility:** Provide technical capabilities that support the layers above.

- Persistence (databases, file systems)
- Messaging (message queues, event buses)
- External service integration (APIs, third-party services)
- Framework plumbing
- Technical services (logging, caching)

### Dependency Rule

**Layers depend only on layers below them.**

```
User Interface
     ↓
  Application
     ↓
    Domain  ← The core (no outward dependencies)
     ↓
Infrastructure
```

Critical insight: The Domain Layer should have **no dependencies** on outer layers. Infrastructure dependencies are managed through abstractions (interfaces) defined in the domain layer.

## Key Benefit: Freedom to Evolve

When domain logic is concentrated in one layer, isolated from infrastructure:

1. **Domain objects are free** from responsibilities like:
   - Storing themselves in a database
   - Displaying themselves on screen
   - Routing themselves over networks
   - Managing transactions

2. **The model can evolve** without being constrained by:
   - Database schemas
   - UI frameworks
   - Communication protocols
   - External service APIs

3. **Business complexity can be tackled directly** because:
   - Domain experts can read and understand domain code
   - Domain concepts are expressed in pure business terms
   - Testing business logic doesn't require databases or web servers
   - Multiple UI or persistence strategies can be supported

## Implementation Guidelines

### Concentrate Domain Logic

All code related to the domain model should be in one layer, free from other responsibilities:

```java
// ✅ GOOD: Pure domain logic
public class Order {
    private OrderStatus status;
    private Money totalAmount;

    public void confirm() {
        if (status != OrderStatus.PENDING) {
            throw new IllegalStateException("Only pending orders can be confirmed");
        }
        status = OrderStatus.CONFIRMED;
    }

    public boolean canBeShipped() {
        return status == OrderStatus.CONFIRMED && totalAmount.isPositive();
    }
}

// ❌ BAD: Domain logic mixed with persistence
public class Order {
    public void confirm() {
        this.status = "CONFIRMED";
        database.executeUpdate("UPDATE orders SET status = ? WHERE id = ?", status, id);
    }
}
```

### Use Dependency Inversion for Infrastructure

The domain layer defines interfaces for what it needs; infrastructure implements them:

```java
// Domain layer defines the interface
public interface OrderRepository {
    Order findById(OrderId id);
    void save(Order order);
}

// Infrastructure layer implements it
public class SqlOrderRepository implements OrderRepository {
    // Database-specific implementation
}
```

### Keep Application Layer Thin

The application layer should orchestrate, not implement business logic:

```java
// ✅ GOOD: Application service orchestrates domain objects
public class CheckoutService {
    public void checkout(OrderId orderId) {
        Order order = orderRepository.findById(orderId);
        order.confirm();  // Business logic in domain
        inventoryService.reserve(order.getItems());  // Business logic in domain
        orderRepository.save(order);
    }
}

// ❌ BAD: Application service contains business logic
public class CheckoutService {
    public void checkout(OrderId orderId) {
        Order order = orderRepository.findById(orderId);
        if (order.getStatus().equals("PENDING")) {  // Business rule here!
            order.setStatus("CONFIRMED");
            // ...
        }
    }
}
```

## Related Architectural Patterns

### Hexagonal Architecture (Ports and Adapters)

Also called "Ports and Adapters," this pattern emphasizes:
- The domain/application core at the center
- "Ports" (interfaces) for all external interactions
- "Adapters" that implement ports for specific technologies

The hexagonal metaphor emphasizes that all external systems (UI, database, APIs) are equivalent from the domain's perspective—they're all just external dependencies accessed through ports.

**When to prefer Hexagonal over traditional layers:**
- When you need multiple UIs (web, CLI, mobile)
- When you anticipate swapping infrastructure (change databases, add message queues)
- When testability and isolation are paramount

### Key Distinction

Traditional layered architecture provides a good mental model and organizational structure. Hexagonal architecture provides additional rigor around dependency management and makes the boundaries more explicit.

Both serve the same fundamental goal: **isolating the domain model from technical concerns.**

## Common Pitfalls

### 1. Anemic Domain Model
When business logic leaks into the application or UI layers, leaving the domain layer with only data structures.

**Solution:** Push behavior into domain objects. Ask "whose responsibility is this business rule?"

### 2. Smart UI Anti-Pattern
When UI components contain business logic for convenience.

**Solution:** Extract business logic into domain services or domain objects. UI should only present and interpret.

### 3. Domain Layer Depending on Infrastructure
When domain objects reference database entities, HTTP clients, or framework types.

**Solution:** Use dependency inversion. Define interfaces in the domain layer, implement in infrastructure.

### 4. Oversized Application Layer
When the application layer grows complex orchestration logic that's actually business logic.

**Solution:** Identify business rules and move them to the domain layer. Application layer should be thin glue code.

## Success Indicators

You've successfully isolated the domain when:

- ✅ Domain objects have no imports from infrastructure packages
- ✅ Business rules can be explained without mentioning databases, web frameworks, or file formats
- ✅ Domain objects can be tested without mocking infrastructure
- ✅ Domain experts can read and validate domain code
- ✅ You can change persistence strategy without changing domain code
- ✅ Multiple UIs can be added without duplicating business logic

## Further Reading

- Eric Evans, *Domain-Driven Design*, Chapter 4: "Isolating the Domain"
- Alistair Cockburn, "Hexagonal Architecture" (Ports and Adapters)
- Robert C. Martin, *Clean Architecture*
