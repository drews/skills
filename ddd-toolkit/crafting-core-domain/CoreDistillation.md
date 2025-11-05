# Core Domain Distillation: Strategic Focus and Ubiquitous Language

## Overview

Not all parts of a domain model are equally important. Some areas provide competitive advantage and business differentiation (the **Core Domain**), while others are necessary but generic. Strategic domain distillation is the process of identifying the Core Domain, focusing effort on it, and ensuring the entire team shares a common language.

## The Problem: Diffused Effort and Miscommunication

In complex systems without strategic focus:
- Development effort spreads evenly across valuable and generic areas
- The most important business capabilities get no special treatment
- Technical and domain experts use different terminology
- Misunderstandings arise from ambiguous language
- Core concepts get buried in supporting complexity
- Competitive advantage is not reflected in code quality

## The Solution: Strategic Distillation

### Ubiquitous Language

**A language structured around the domain model and used by all team members to connect all activities of the team with the software.**

#### What is Ubiquitous Language?

A shared vocabulary that:
- Includes names of domain concepts, relationships, and rules
- Is used consistently in code (class names, method names, properties)
- Is used in conversation between developers and domain experts
- Appears in documentation, diagrams, and discussions
- Evolves as understanding deepens

#### Why It Matters

**With Ubiquitous Language:**
- Domain experts can read and validate code
- Developers understand business requirements clearly
- Misunderstandings surface quickly
- Refactoring reveals deeper domain insights
- The model becomes a communication tool

**Without Ubiquitous Language:**
- Developers invent technical names disconnected from business
- Domain experts can't recognize their concepts in code
- Translation errors occur between business and technical discussions
- Knowledge is lost in translation

#### Establishing Ubiquitous Language

**1. Collaborate with Domain Experts**
- Participate in domain modeling sessions
- Ask questions about terminology
- Challenge vague or overloaded terms
- Propose precise terms for important concepts

**2. Use the Language in Code**
```java
// ❌ BAD: Technical jargon, unclear domain meaning
public class OrderProcessor {
    public void process(OrderDTO dto) {
        dao.update(dto);
    }
}

// ✅ GOOD: Ubiquitous Language reflected in code
public class Order {
    public void placeOrder() {
        validateInventoryAvailability();
        reserveStock();
        recordOrderPlaced();
    }
}
```

**3. Use the Language in Conversations**
- Developers: "When a Customer places an Order, do we immediately reserve Inventory?"
- Domain Expert: "Yes, we place a Reservation that holds Stock for 15 minutes."
- Code reflects this: `Reservation.holdStock(duration: 15.minutes)`

**4. Refine and Evolve**
- When new terms emerge, incorporate them immediately
- When existing terms prove inadequate, refactor
- Document key terms in a glossary

#### Common Pitfalls

**1. Technical terms dominate:**
- "Manager," "Processor," "Handler," "Service" (generic, meaningless names)
- Solution: Use domain-specific names like "PolicyUnderwriter," "ShipmentRouter," "RefundAuthorizer"

**2. Inconsistent usage:**
- "Customer" in code, "Client" in conversation, "User" in UI
- Solution: Choose one term and use it everywhere

**3. Overloaded terms:**
- "Order" means different things in Sales vs. Fulfillment contexts
- Solution: Use Bounded Contexts to maintain distinct languages

#### Hands-On Modelers

**The technical people building the software must participate in modeling.**

Domain-Driven Design requires that those who design the abstractions also write the code. This practice, called **Hands-On Modelers**, ensures:
- Models are practical and implementable
- Feedback from code informs model design
- Deep domain insights emerge from implementation challenges
- Technical people develop domain expertise
- Domain experts see their concepts reflected immediately

**Anti-pattern:** Separate modeling and implementation teams lead to:
- Unrealistic models
- Lost knowledge in handoffs
- Lack of feedback loop
- Divorced code from domain

---

## Core Domain

**The part of the model that makes the system worth building and gives the organization competitive advantage.**

### Identifying the Core Domain

Ask these questions:
1. **What makes our business unique?** What do we do differently from competitors?
2. **Where is the complexity?** Where do the hardest business problems lie?
3. **What provides the most value?** What would customers pay a premium for?
4. **What requires the most domain expertise?** What can't be outsourced easily?

**Example (E-Commerce):**
- **Core Domain**: Dynamic pricing algorithm, personalized recommendation engine, fraud detection
- **Not Core**: Shopping cart, user authentication, payment processing (these are solved problems)

### Supporting Subdomains

**Necessary for the business but not the primary source of competitive advantage.**

Supporting Subdomains:
- Are essential to operations
- Don't differentiate the business
- Can be developed with less rigor than Core
- May use simpler models or CRUD approaches
- Should receive less investment

**Examples:**
- Inventory management (unless you're Walmart)
- Basic reporting
- User profile management
- Notification systems

### Generic Subdomains

**Subdomains for which off-the-shelf solutions exist and should be used.**

Generic Subdomains:
- Solve universal, well-understood problems
- Have commodity solutions available
- Provide no competitive advantage
- Should be bought, not built

**Examples:**
- Authentication and authorization (use Auth0, Keycloak, Okta)
- Payment processing (use Stripe, PayPal)
- Email delivery (use SendGrid, Mailchimp)
- Logging and monitoring (use Datadog, New Relic)

**Strategic Decision:** Invest minimal effort. Use third-party libraries, SaaS products, or simple implementations.

---

## Distillation Techniques

### 1. Domain Vision Statement

**A short description (one page) of the Core Domain and the value proposition it provides.**

The Domain Vision Statement:
- Focuses on the business problem being solved
- Explains what makes this system valuable
- Highlights what's unique or differentiating
- Guides architectural and design decisions
- Serves as a North Star for the team

**Example Domain Vision Statement:**

> **ShipFast Logistics Routing System**
>
> ShipFast's competitive edge is our ability to optimize delivery routes in real-time, accounting for traffic, weather, vehicle capacity, and customer priorities. Our Core Domain—the Intelligent Routing Engine—uses proprietary algorithms to reduce delivery time by 30% and fuel costs by 20% compared to traditional routing.
>
> Unlike competitors who rely on static routes or third-party routing APIs, we continuously adapt routes as conditions change, leveraging our deep domain model of delivery constraints, driver behavior, and customer SLA requirements. This capability directly translates to customer satisfaction and operational margin.

**When to write it:** Early in the project and revisit periodically.

**Who writes it:** Collaboration between domain experts, architects, and product leadership.

---

### 2. Highlighted Core

**Flagging the essential Core elements in the model and implementation.**

Techniques:
- **Documentation**: Create a short document (3-5 pages) listing Core classes, aggregates, and relationships
- **Code Annotations**: Use comments or doc-comments to flag Core elements
- **Visual Diagrams**: Highlight Core elements in architecture diagrams
- **README Files**: Explain what's Core in each module

**Example:**
```java
/**
 * CORE DOMAIN: PricingEngine
 *
 * This class implements our proprietary dynamic pricing algorithm,
 * which adjusts prices based on demand, inventory, and competitor data.
 * This is a key differentiator for our business.
 */
public class PricingEngine {
    public Money calculateOptimalPrice(Product product, MarketConditions conditions) {
        // Core business logic here
    }
}
```

**Purpose:** Make it obvious to developers where the most important logic lives and where to invest design effort.

---

### 3. Segregated Core

**Refactoring to physically isolate the Core Domain from supporting and generic elements.**

As systems grow, Core elements become intertwined with supporting code. The Segregated Core pattern involves:

**Structural isolation:**
- Separate modules, packages, or microservices for Core
- Clear boundaries between Core and non-Core
- Minimize dependencies from Core to Supporting

**Example Package Structure:**
```
com.shipfast.routing/           # Core Domain
├── RouteOptimizer
├── DeliveryConstraint
├── VehicleCapacityPlanner
└── TrafficPredictor

com.shipfast.dispatching/       # Supporting Subdomain
├── DriverAssignment
├── NotificationService

com.shipfast.infrastructure/    # Generic/Infrastructure
├── Database
├── MessageQueue
```

**Benefits:**
- Core code is easier to find and understand
- Easier to apply higher design standards to Core
- Reduces risk of accidental coupling
- Facilitates team specialization (Core team vs. Supporting team)

**When to apply:** As the system matures and boundaries become clear.

---

### 4. Abstract Core

**Identifying the most fundamental concepts and defining them in abstract form (interfaces or abstract classes).**

The Abstract Core pattern:
- Extracts the most essential interfaces and interactions
- Keeps the Core conceptually pure
- Allows variations in implementation
- Supports extension without modifying core abstractions

**Example:**
```java
// Abstract Core: Essential concepts
public interface PricingStrategy {
    Money calculatePrice(Product product, Customer customer, MarketConditions conditions);
}

// Concrete implementations
public class DynamicPricingStrategy implements PricingStrategy {
    // Our competitive advantage implementation
}

public class StandardPricingStrategy implements PricingStrategy {
    // Simple cost-plus pricing for commodity products
}
```

**When useful:**
- Core domain has multiple variations or algorithms
- Need to experiment with different approaches
- Want to isolate stable concepts from changing implementations

---

## Model-Driven Design

**The design and implementation directly reflect and express the domain model.**

Model-Driven Design is the practice of:
- Binding model and implementation tightly together
- Refining the model through implementation
- Letting code changes inform model changes, and vice versa
- Treating code as a representation of the model, not just mechanism

**Key principle:** The model is not a separate artifact; it *is* the code.

**Example:**

Domain Expert says: "A Loan Application goes through an Underwriting process, which produces a Risk Assessment. Based on the Risk Assessment, we either Approve or Deny the application."

**Model-Driven Design:**
```java
public class LoanApplication {
    private ApplicationStatus status;

    public RiskAssessment underwrite(UnderwritingCriteria criteria) {
        RiskAssessment assessment = criteria.assess(this);
        return assessment;
    }

    public void approve(RiskAssessment assessment) {
        if (assessment.isAcceptable()) {
            this.status = ApplicationStatus.APPROVED;
        } else {
            throw new UnacceptableRiskException();
        }
    }

    public void deny(String reason) {
        this.status = ApplicationStatus.DENIED;
    }
}
```

The code directly reflects the language and process described by the domain expert.

---

## Continuous Integration (of the Model)

**Frequently merging and harmonizing domain models as the team learns.**

Continuous Integration in DDD means:
- Frequent synchronization of code and model understanding
- Regular team discussions about domain concepts
- Rapid incorporation of new insights
- Resolving model conflicts quickly

**Practices:**
- **Daily standups with domain language:** Discuss domain concepts, not just tasks
- **Pair programming on domain logic:** Two minds refining the model together
- **Code reviews focused on domain clarity:** "Does this class name make sense to domain experts?"
- **Regular model retrospectives:** "What did we learn this week about the domain?"

**Automated integration (CI/CD) supports this:**
- Frequent commits and merges prevent model drift
- Automated tests validate model integrity
- Continuous deployment allows rapid model evolution

---

## Strategic Investment Based on Subdomain Type

| Subdomain Type | Investment Level | Approach | Team Composition |
|----------------|------------------|----------|------------------|
| **Core Domain** | Highest | Custom, refined design; rich domain model; iterative improvement | Best developers + domain experts |
| **Supporting** | Moderate | Adequate design; may use simpler models or CRUD | Competent developers |
| **Generic** | Minimal | Off-the-shelf solutions; simple integration | Junior developers, outsourcing |

**Example allocation:**
- 60% effort on Core Domain
- 30% effort on Supporting Subdomains
- 10% effort on Generic Subdomains (mostly integration)

---

## Applying Distillation in Practice

### Step 1: Assess the Current State
- Map all subdomains in your system
- Classify each as Core, Supporting, or Generic
- Identify which areas receive the most development effort

### Step 2: Identify Core Domain
- Collaborate with business stakeholders
- Use the Core Domain identification questions
- Write a Domain Vision Statement

### Step 3: Align Effort with Value
- Allocate top developers to Core Domain
- Simplify or outsource Generic Subdomains
- Accept simpler designs in Supporting Subdomains

### Step 4: Highlight and Segregate
- Flag Core elements in code and documentation
- Refactor to physically separate Core from Supporting
- Make Core architecture visible to all stakeholders

### Step 5: Establish Ubiquitous Language
- Document key terms
- Use domain language in code
- Conduct collaborative modeling sessions

### Step 6: Iterate and Refine
- Continuously refine Core Domain design
- Revisit Domain Vision Statement as business evolves
- Refactor when deeper insights emerge

---

## Common Pitfalls

### 1. Everything is Core
Treating all areas equally dilutes focus and quality.

**Solution:** Be ruthless in identifying what truly differentiates the business.

### 2. Neglecting Ubiquitous Language
Using technical jargon instead of domain terms.

**Solution:** Establish a glossary and enforce usage in code reviews.

### 3. Static Vision
Writing a Domain Vision Statement once and never revisiting it.

**Solution:** Review and update as the business evolves.

### 4. Isolating Domain Experts
Modelers don't engage with domain experts regularly.

**Solution:** Schedule regular collaborative modeling sessions. Embrace Hands-On Modelers.

### 5. Over-Engineering Supporting Subdomains
Applying sophisticated design patterns to non-Core areas.

**Solution:** Use simpler approaches (CRUD, anemic models) where appropriate.

---

## Success Indicators

You've successfully distilled the Core Domain when:

- ✅ Everyone on the team can name the Core Domain and explain its value
- ✅ The Ubiquitous Language is used consistently in code and conversation
- ✅ Core Domain code receives the most design attention and best developers
- ✅ Supporting and Generic Subdomains are treated pragmatically
- ✅ Domain Vision Statement guides architectural decisions
- ✅ Core elements are easy to locate in the codebase
- ✅ Domain experts actively participate in modeling and can read domain code
- ✅ Competitive advantage is reflected in code quality and design sophistication

---

## Further Reading

- Eric Evans, *Domain-Driven Design*, Part I: "Putting the Domain Model to Work" and Part V: "Strategic Design"
- Vaughn Vernon, *Implementing Domain-Driven Design*, Chapter 2: "Domains, Subdomains, and Bounded Contexts"
- Vaughn Vernon, *Domain-Driven Design Distilled*, Chapter 1: "DDD for Me"
