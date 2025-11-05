# Supple Design: Crafting Flexible, Expressive Domain Models

## Overview

A "supple" design is one that:
- **Reveals intention** clearly
- **Invites change** rather than resisting it
- **Makes complex interactions** easy to anticipate
- **Enables deep insights** about the domain to emerge naturally

Supple design is achieved through continuous refinement and the application of rigorous design principles. It's not a one-time achievement but an ongoing process of improvement.

---

## The Problem: Rigid, Obscure Models

Without attention to design quality:
- Code is hard to understand (what does this method do?)
- Changes are risky (what side effects will this have?)
- Domain concepts are buried in implementation details
- Complexity multiplies as the system grows
- Developers fear touching code because they don't understand the implications

---

## The Solution: Supple Design Patterns

---

## 1. Intention-Revealing Interfaces

**Name classes and operations to describe their purpose, not their implementation mechanism.**

### The Problem

```java
// ❌ BAD: What does this do? How does it do it?
public class DataProcessor {
    public void process(Data data) {
        // ???
    }
}

// ❌ BAD: Implementation details in the name
public class CustomerDatabaseManager {
    public void updateCustomerRecordInDatabase(Customer customer) {
        // ...
    }
}
```

**Problems:**
- Names don't reveal business purpose
- Developers must read implementation to understand behavior
- Names are coupled to implementation (what if we change from database to cache?)

### The Solution

**Choose names that:**
1. Describe **what** the operation does (not **how**)
2. Use **Ubiquitous Language** from the domain
3. Are meaningful to **domain experts**
4. Are **precise** (avoid vague terms like "process," "handle," "manage")

```java
// ✅ GOOD: Clear domain intent
public class Order {
    public void confirm() {
        // Domain experts immediately understand: this transitions order to confirmed state
    }

    public void cancel(CancellationReason reason) {
        // Clear: this cancels the order with a reason
    }

    public Money calculateTotalWithTax(TaxRate rate) {
        // Clear: calculates and returns total including tax
    }
}

// ✅ GOOD: Clear service intent
public class CreditScoreEvaluator {
    public CreditScore evaluate(LoanApplication application) {
        // Domain purpose is clear
    }
}
```

### Guidelines

#### For Classes

Use domain concepts:
- ✅ `ShipmentRouter`, `InventoryAllocator`, `PricingCalculator`
- ❌ `DataManager`, `OrderProcessor`, `SystemHandler`

#### For Methods

Use action verbs that reveal domain operations:
- ✅ `order.confirm()`, `payment.authorize()`, `shipment.dispatch()`
- ❌ `order.process()`, `payment.handle()`, `shipment.update()`

#### For Value Objects

Use domain-meaningful names:
- ✅ `Money`, `DateRange`, `Temperature`, `Coordinates`
- ❌ `AmountVO`, `PairOfDates`, `NumberWithUnit`

### Before and After Examples

**Example 1: Payment Processing**

```java
// ❌ BEFORE
public class PaymentService {
    public boolean doPayment(PaymentInfo info) {
        // Unclear: What does "do" mean? What does boolean indicate?
    }
}

// ✅ AFTER
public class PaymentAuthorizer {
    public AuthorizationResult authorize(PaymentRequest request) {
        // Clear: Attempts to authorize payment and returns detailed result
    }
}
```

**Example 2: Inventory**

```java
// ❌ BEFORE
public class InventoryManager {
    public void updateStock(String productId, int amount) {
        // Unclear: Is this adding, subtracting, or setting stock?
    }
}

// ✅ AFTER
public class Inventory {
    public void receiveShipment(ProductId productId, Quantity quantity) {
        // Clear: Stock is increasing due to shipment arrival
    }

    public void reserveForOrder(ProductId productId, Quantity quantity) {
        // Clear: Stock is being held for an order
    }

    public void releaseReservation(ReservationId reservationId) {
        // Clear: Previously reserved stock is freed
    }
}
```

### Benefits

- Domain experts can read and validate code
- New developers understand purpose without reading implementation
- Refactoring is safer (intent is preserved even if mechanism changes)
- Code serves as documentation

---

## 2. Side-Effect-Free Functions

**Design operations to return results without causing observable side effects.**

### The Problem

```java
// ❌ BAD: Hidden side effects
public class Order {
    private Money totalAmount;

    public Money calculateTotal() {
        Money total = /* complex calculation */;
        this.totalAmount = total;  // SIDE EFFECT: Modifies state!
        return total;
    }
}
```

**Problems:**
- Calling `calculateTotal()` changes object state
- Not obvious from signature
- Calling multiple times may produce different results
- Testing is harder (must verify state changes)
- Reasoning about behavior is difficult

### The Solution

**Make functions truly functional:**
- Return a value
- Do not modify any observable state
- Same inputs always produce same outputs (deterministic)
- Safe to call multiple times

```java
// ✅ GOOD: Pure function
public class Order {
    public Money calculateTotal() {
        return lines.stream()
            .map(OrderLine::getSubtotal)
            .reduce(Money.ZERO, Money::add);
        // No side effects, safe to call repeatedly
    }
}
```

### When to Avoid Side Effects

**Queries and calculations should be side-effect-free:**

```java
// ✅ Queries (no side effects)
public Money getBalance();
public boolean isOverdrawn();
public List<Transaction> getRecentTransactions();
public boolean canBeShipped();
public Money calculateTax(TaxRate rate);
```

**Commands naturally have side effects:**

```java
// Commands (expected side effects)
public void confirm();
public void ship();
public void addItem(OrderLine line);
```

### Command-Query Separation (CQS)

**Principle:** Methods should either:
1. **Command**: Change state (side effect) and return void
2. **Query**: Return a value and change no state (side-effect-free)

**Not both.**

```java
// ❌ BAD: Violates CQS
public Money applyDiscount(Discount discount) {
    this.totalAmount = this.totalAmount.subtract(discount.getAmount());  // Command
    return this.totalAmount;  // Query
}

// ✅ GOOD: Separate command and query
public void applyDiscount(Discount discount) {
    this.totalAmount = this.totalAmount.subtract(discount.getAmount());
}

public Money getTotal() {
    return this.totalAmount;
}
```

### Benefits of Side-Effect-Free Functions

1. **Easy to test**: No need to verify state; just check return value
2. **Composable**: Can be chained or combined safely
3. **Parallelizable**: No shared state concerns
4. **Predictable**: Same input → same output
5. **Safe to call**: No unintended consequences

### Value Objects as Side-Effect-Free

Value Objects naturally support side-effect-free operations:

```java
public class Money {
    private final BigDecimal amount;
    private final Currency currency;

    // ✅ All operations return new instances, no mutations
    public Money add(Money other) {
        return new Money(this.amount.add(other.amount), this.currency);
    }

    public Money multiply(BigDecimal multiplier) {
        return new Money(this.amount.multiply(multiplier), this.currency);
    }

    public Money negate() {
        return new Money(this.amount.negate(), this.currency);
    }
}
```

---

## 3. Assertions

**State pre-conditions, post-conditions, and invariants explicitly.**

### The Problem

Without explicit contracts:
- Assumptions are hidden
- Errors surface far from their source
- Debugging is harder
- Intent is unclear

### The Solution

**Make expectations explicit through:**
1. **Pre-conditions**: What must be true before operation executes
2. **Post-conditions**: What will be true after operation executes
3. **Invariants**: What must always be true for the object

### Implementation Approaches

#### 1. Guard Clauses (Runtime Checks)

```java
public class Order {
    public void ship(Address destination) {
        // Pre-conditions
        if (this.status != OrderStatus.CONFIRMED) {
            throw new IllegalStateException("Only confirmed orders can be shipped");
        }
        if (destination == null) {
            throw new IllegalArgumentException("Destination address is required");
        }

        this.destination = destination;
        this.status = OrderStatus.SHIPPED;
        this.shippedAt = Instant.now();

        // Post-condition check (optional, often implicit)
        assert this.status == OrderStatus.SHIPPED;
    }
}
```

#### 2. Constructor Invariants

```java
public class DateRange {
    private final LocalDate start;
    private final LocalDate end;

    public DateRange(LocalDate start, LocalDate end) {
        // Invariant: start must be before or equal to end
        if (start == null || end == null) {
            throw new IllegalArgumentException("Dates cannot be null");
        }
        if (start.isAfter(end)) {
            throw new IllegalArgumentException("Start date must be before or equal to end date");
        }

        this.start = start;
        this.end = end;
    }
}
```

#### 3. Java Assertions (for Development/Testing)

```java
public void transferFunds(Account from, Account to, Money amount) {
    assert from != null && to != null && amount != null : "Arguments cannot be null";
    assert amount.isPositive() : "Amount must be positive";

    Money fromBalanceBefore = from.getBalance();
    Money toBalanceBefore = to.getBalance();

    from.debit(amount);
    to.credit(amount);

    // Post-condition: Total money is conserved
    assert from.getBalance().add(to.getBalance())
        .equals(fromBalanceBefore.add(toBalanceBefore))
        : "Money was lost or created during transfer";
}
```

#### 4. Documentation (Design by Contract)

```java
/**
 * Ships the order to the specified destination.
 *
 * Pre-conditions:
 * - Order status must be CONFIRMED
 * - Destination must not be null
 *
 * Post-conditions:
 * - Order status is SHIPPED
 * - shippedAt timestamp is set
 * - destination is recorded
 *
 * @throws IllegalStateException if order is not confirmed
 * @throws IllegalArgumentException if destination is null
 */
public void ship(Address destination) {
    // implementation
}
```

### Invariants

**Class invariants** are conditions that must always hold:

```java
public class BankAccount {
    private Money balance;
    private AccountStatus status;

    // Invariant: Closed accounts cannot have transactions
    public void debit(Money amount) {
        if (this.status == AccountStatus.CLOSED) {
            throw new IllegalStateException("Cannot debit from closed account");
        }
        this.balance = this.balance.subtract(amount);
    }

    // Invariant: Balance cannot go below zero (for regular accounts)
    public void debit(Money amount) {
        Money newBalance = this.balance.subtract(amount);
        if (newBalance.isNegative()) {
            throw new InsufficientFundsException();
        }
        this.balance = newBalance;
    }
}
```

### Benefits

- **Self-documenting**: Code clearly states its expectations
- **Early failure**: Errors caught close to their source
- **Confidence**: Callers know what to expect
- **Maintenance**: Invariants guide changes
- **Testing**: Assertions provide test oracles

---

## 4. Closure of Operations

**Where appropriate, define operations whose return type matches the argument type(s).**

### The Concept

An operation has **closure** if:
- Input and output types are the same
- You can chain operations naturally
- Result can be used as input to another operation

### Examples

#### Value Objects with Closure

```java
public class Money {
    public Money add(Money other) {
        // Money + Money → Money
        return new Money(this.amount.add(other.amount), this.currency);
    }

    public Money multiply(BigDecimal factor) {
        // Money × number → Money
        return new Money(this.amount.multiply(factor), this.currency);
    }

    public Money negate() {
        // Money → Money
        return new Money(this.amount.negate(), this.currency);
    }
}

// Usage: Natural chaining
Money finalAmount = baseAmount
    .add(tax)
    .add(shipping)
    .multiply(quantity);
```

#### Date/Time Operations

```java
public class DateRange {
    public DateRange extendBy(Duration duration) {
        // DateRange + Duration → DateRange
        return new DateRange(this.start, this.end.plus(duration));
    }

    public DateRange intersect(DateRange other) {
        // DateRange ∩ DateRange → DateRange
        LocalDate newStart = this.start.isAfter(other.start) ? this.start : other.start;
        LocalDate newEnd = this.end.isBefore(other.end) ? this.end : other.end;
        return new DateRange(newStart, newEnd);
    }
}
```

### Benefits

1. **Composability**: Operations can be chained
2. **Predictability**: Same type in, same type out
3. **Fluent API**: Natural, readable expressions
4. **Mathematical elegance**: Mirrors domain algebra

### When Closure Applies

**Natural in:**
- Mathematics (numbers, sets, vectors)
- Time and dates
- Money and quantities
- Geometric operations
- Collections

**Not always appropriate:**
- Commands that change state (void return)
- Conversions (Money → String)
- Predicates (Order → boolean)

### Example: Building a Pricing DSL

```java
public class Price {
    public Price applyDiscount(Percentage discount) {
        return new Price(this.amount.multiply(discount.asDecimal()));
    }

    public Price addTax(TaxRate rate) {
        return new Price(this.amount.multiply(rate.multiplier()));
    }

    public Price add(Price other) {
        return new Price(this.amount.add(other.amount));
    }
}

// Fluent usage
Price finalPrice = basePrice
    .applyDiscount(Percentage.of(10))
    .addTax(TaxRate.standardRate())
    .add(deliveryFee);
```

---

## 5. Declarative Design

**Express logic in a declarative style that describes *what* rather than *how*.**

### The Problem

Imperative code buries intent in implementation:

```java
// ❌ IMPERATIVE: How to calculate
public Money calculateTotalPrice() {
    Money total = Money.zero();
    for (OrderLine line : this.lines) {
        Money lineTotal = line.getUnitPrice().multiply(line.getQuantity());
        total = total.add(lineTotal);
    }
    return total;
}
```

### The Solution

Declarative code emphasizes *what* is being calculated:

```java
// ✅ DECLARATIVE: What we're calculating
public Money calculateTotalPrice() {
    return lines.stream()
        .map(OrderLine::getSubtotal)
        .reduce(Money.ZERO, Money::add);
}
```

### Techniques

#### 1. Use Domain-Specific Methods

```java
// ❌ IMPERATIVE
public boolean canShip() {
    if (this.status == OrderStatus.CONFIRMED &&
        this.paymentStatus == PaymentStatus.PAID &&
        this.items.size() > 0) {
        return true;
    }
    return false;
}

// ✅ DECLARATIVE
public boolean canShip() {
    return isConfirmed() && isPaid() && hasItems();
}

private boolean isConfirmed() {
    return this.status == OrderStatus.CONFIRMED;
}

private boolean isPaid() {
    return this.paymentStatus == PaymentStatus.PAID;
}

private boolean hasItems() {
    return !this.items.isEmpty();
}
```

#### 2. Leverage Streams and Functional APIs

```java
// ✅ Declarative filtering
List<Order> overdueOrders = orders.stream()
    .filter(Order::isOverdue)
    .collect(Collectors.toList());

// ✅ Declarative transformation
List<OrderSummary> summaries = orders.stream()
    .map(OrderSummary::from)
    .collect(Collectors.toList());
```

#### 3. Build Domain-Specific Languages (DSLs)

```java
// Specification pattern for declarative queries
Specification<Order> overdueAndHighValue =
    OrderSpecifications.overdue()
        .and(OrderSpecifications.valueGreaterThan(Money.dollars(1000)));

List<Order> results = orderRepository.findAll(overdueAndHighValue);
```

#### 4. Strategy Pattern for Declarative Selection

```java
// ✅ Declarative selection of pricing strategy
PricingStrategy strategy = PricingStrategy.selectFor(customer);
Money price = strategy.calculatePrice(product);

// Instead of:
// if (customer.isVIP()) { ... } else if (customer.isBulkBuyer()) { ... }
```

### Benefits

- **Clarity**: Intent is obvious
- **Maintainability**: Less boilerplate
- **Testability**: Focus on *what*, not *how*
- **Refactorability**: Implementation can change without affecting clarity

---

## 6. Conceptual Contours

**Decompose design elements along axes that align with the domain's natural concepts and stable areas of change.**

### The Problem

Poor decomposition:
- Classes and methods don't align with domain concepts
- Changes to one concept require touching multiple places
- Related logic is scattered
- Unrelated logic is bundled together

### The Solution

**Find the "grain" of the domain—the natural boundaries and stable concepts—and align your design with them.**

### Identifying Conceptual Contours

Ask:
1. **What concepts are fundamental and stable?** (e.g., Customer, Order, Product)
2. **What varies together?** (bundle these)
3. **What varies independently?** (separate these)
4. **Where are the natural boundaries in the domain?**

### Example: Pricing

**Poor contours** (everything in one class):

```java
// ❌ BAD: Muddy contours
public class PricingEngine {
    public Money calculatePrice(Product product, Customer customer, Season season,
                                Inventory inventory, PromotionCode promo) {
        // Mix of:
        // - Base pricing
        // - Volume discounts
        // - Seasonal adjustments
        // - Inventory-based pricing
        // - Promotion logic
        // All tangled together
    }
}
```

**Clear contours** (separated by concept):

```java
// ✅ GOOD: Clear conceptual boundaries
public class BasePrice {
    public Money calculate(Product product) {
        // Stable: Base price from product catalog
    }
}

public class VolumeDiscountPolicy {
    public Money calculateDiscount(Money basePrice, Quantity quantity) {
        // Varies: Volume discount rules
    }
}

public class SeasonalAdjustment {
    public Money adjust(Money price, Season season, ProductCategory category) {
        // Varies: Seasonal pricing rules
    }
}

public class PromotionCalculator {
    public Money applyPromotion(Money price, PromotionCode code) {
        // Varies frequently: Promotion campaigns
    }
}

// Orchestrator
public class PricingService {
    public Money calculateFinalPrice(Product product, Customer customer,
                                     Season season, PromotionCode promo) {
        Money basePrice = basePriceCalculator.calculate(product);
        Money afterDiscount = volumeDiscountPolicy.calculateDiscount(basePrice, customer.getOrderVolume());
        Money afterSeasonal = seasonalAdjustment.adjust(afterDiscount, season, product.getCategory());
        Money finalPrice = promotionCalculator.applyPromotion(afterSeasonal, promo);
        return finalPrice;
    }
}
```

**Benefits:**
- Each class has a single, clear responsibility
- Changes to promotions don't affect seasonal pricing
- Each concept can evolve independently
- Testing is easier (isolated concerns)

### Aligning with Domain Concepts

Classes and modules should reflect how domain experts think:

```java
// ✅ Domain expert says: "We have a base rate, apply zone-based adjustments,
//     and then calculate delivery fees"
public class ShippingCostCalculator {
    public Money calculate(Shipment shipment) {
        Money baseRate = rateCard.getBaseRate(shipment.getWeight());
        Money adjusted = zoneAdjuster.adjust(baseRate, shipment.getDestination());
        Money total = deliveryFeeCalculator.addFees(adjusted, shipment.getServiceLevel());
        return total;
    }
}
```

### Refactoring Toward Conceptual Contours

1. **Identify concepts** that are currently tangled
2. **Extract each concept** into its own class or module
3. **Compose** them in a coordinating layer
4. **Validate** with domain experts: "Does this decomposition make sense to you?"

---

## 7. Refactoring Toward Deeper Insight

**Continuously refine the model as domain understanding deepens.**

### The DDD Refactoring Cycle

```
Explore → Model → Code → Reflect → Deeper Insight → Refactor → Explore...
```

Domain-Driven Design is not a one-time activity. As you implement and use the model:
- **New insights emerge** from conversations with domain experts
- **Hidden concepts surface** through implementation challenges
- **Awkward code signals** a model mismatch
- **Patterns reveal themselves** as the domain becomes clearer

### Triggers for Refactoring

#### 1. Breakthrough in Understanding
Domain experts reveal a concept you hadn't modeled.

**Example:** You've been modeling "PromotionCode" as a simple string, but experts explain that there are fundamentally different types of promotions (percentage-based, fixed-amount, buy-one-get-one) with different rules.

**Refactoring:** Introduce a `PromotionStrategy` abstraction.

#### 2. Awkward Code
Code feels unnatural or overly complex.

**Example:**
```java
// Awkward: Why is Order calculating inventory?
public void confirmOrder(Order order) {
    if (inventory.getQuantity(order.getProductId()) >= order.getQuantity()) {
        order.confirm();
        inventory.reduce(order.getProductId(), order.getQuantity());
    }
}
```

**Refactoring:** Recognize that this is about "Reservation," not Order or Inventory directly. Introduce a `Reservation` concept.

#### 3. Duplicate Concepts
Similar logic appears in multiple places.

**Refactoring:** Extract a shared concept (Entity, Value Object, or Service).

#### 4. Missing Ubiquitous Language
Domain experts use terms not present in the code.

**Example:** Experts say "We allocate shipments to carriers," but code says `assignShipmentToCarrier()`.

**Refactoring:** Introduce `Allocation` as a first-class concept.

### The Refactoring Process

1. **Listen to the domain**: Engage regularly with domain experts
2. **Notice friction**: When code feels wrong, investigate
3. **Hypothesize**: "What if we modeled this as...?"
4. **Experiment**: Try refactoring in a branch
5. **Validate**: Show refactored model to domain experts
6. **Commit**: If insight is confirmed, merge the refactoring
7. **Iterate**: Continue learning and refining

### Example Refactoring Journey

**Initial Model:**
```java
public class Order {
    private List<Item> items;
    private String shippingAddress;

    public void ship() {
        // ...
    }
}
```

**Insight 1:** "Shipping Address isn't just a string; it has structure and validation."

**Refactoring 1:**
```java
public class Order {
    private List<Item> items;
    private Address shippingAddress;  // Extract Value Object
}
```

**Insight 2:** "Items in an order have price and quantity; that's an OrderLine."

**Refactoring 2:**
```java
public class Order {
    private List<OrderLine> lines;  // More precise concept
    private Address shippingAddress;
}
```

**Insight 3:** "Orders go through Fulfillment, which is a separate concern."

**Refactoring 3:**
```java
// Order focuses on sales
public class Order {
    private List<OrderLine> lines;
    private Address shippingAddress;
}

// Fulfillment is separate Aggregate
public class Fulfillment {
    private OrderId orderId;
    private ShipmentStatus status;

    public void ship(Carrier carrier) {
        // ...
    }
}
```

Each refactoring brings the model closer to how domain experts think and talk.

### Enabling Refactoring

To support continuous refactoring:
- **Comprehensive test suite**: Enables safe changes
- **Collaborative modeling**: Regular sessions with domain experts
- **Code reviews**: Catch model drift early
- **Refactoring tools**: Use IDE refactoring features
- **Small, frequent changes**: Easier to review and validate

---

## Putting It All Together: A Supple Design Example

**Domain:** Online bookstore pricing

### Initial (Rigid) Design

```java
public class PriceCalculator {
    public double calculatePrice(String isbn, int quantity, String customerType, String promoCode) {
        double basePrice = getBasePriceFromDatabase(isbn);
        double price = basePrice * quantity;

        if (customerType.equals("VIP")) {
            price = price * 0.9;
        }

        if (promoCode != null && !promoCode.isEmpty()) {
            if (promoCode.equals("SUMMER10")) {
                price = price * 0.9;
            } else if (promoCode.equals("FALL15")) {
                price = price * 0.85;
            }
        }

        return price;
    }
}
```

**Problems:**
- Primitive types (double, String) don't reveal domain concepts
- Mixed responsibilities (base price lookup, discount calculation)
- Procedural style
- Hard to test, hard to extend

### Refactored (Supple) Design

```java
// INTENTION-REVEALING: Clear domain types
public class Money {
    private final BigDecimal amount;

    public Money multiply(BigDecimal factor) {  // CLOSURE OF OPERATIONS
        return new Money(this.amount.multiply(factor));
    }

    public Money subtract(Money other) {  // CLOSURE, SIDE-EFFECT-FREE
        return new Money(this.amount.subtract(other.amount));
    }
}

public class Book {
    private ISBN isbn;
    private Money basePrice;

    public Money getBasePrice() {  // SIDE-EFFECT-FREE
        return basePrice;
    }
}

// CONCEPTUAL CONTOURS: Discount logic separated
public interface DiscountPolicy {
    Money applyDiscount(Money price);  // INTENTION-REVEALING, SIDE-EFFECT-FREE
}

public class VIPDiscountPolicy implements DiscountPolicy {
    public Money applyDiscount(Money price) {
        return price.multiply(BigDecimal.valueOf(0.9));
    }
}

public class PromotionDiscountPolicy implements DiscountPolicy {
    private final BigDecimal discountRate;

    public PromotionDiscountPolicy(Promotion promotion) {
        // ASSERTIONS: Validate promotion
        if (promotion.isExpired()) {
            throw new IllegalArgumentException("Promotion has expired");
        }
        this.discountRate = promotion.getDiscountRate();
    }

    public Money applyDiscount(Money price) {
        return price.multiply(discountRate);
    }
}

// DECLARATIVE DESIGN: Compose policies
public class PricingService {
    public Money calculateFinalPrice(Book book, Quantity quantity, Customer customer, Optional<Promotion> promotion) {
        Money basePrice = book.getBasePrice();
        Money subtotal = basePrice.multiply(quantity.asDecimal());  // CLOSURE

        List<DiscountPolicy> policies = new ArrayList<>();
        if (customer.isVIP()) {
            policies.add(new VIPDiscountPolicy());
        }
        promotion.ifPresent(promo -> policies.add(new PromotionDiscountPolicy(promo)));

        // DECLARATIVE: Apply all discounts
        return policies.stream()
            .reduce(subtotal,
                (price, policy) -> policy.applyDiscount(price),
                (p1, p2) -> p1);  // (Not used, but required by reduce signature)
    }
}
```

**Improvements:**
- **Intention-Revealing**: `Money`, `DiscountPolicy`, `Promotion` reveal domain concepts
- **Side-Effect-Free**: All calculation methods return new values
- **Closure**: Money operations return Money
- **Assertions**: Promotion expiration is validated
- **Conceptual Contours**: Discount logic separated by type
- **Declarative**: Discount application is declarative stream operation

---

## Common Pitfalls

### 1. Over-Engineering
Applying every pattern everywhere.

**Solution:** Apply patterns where they clarify, not for their own sake.

### 2. Premature Abstraction
Creating flexible designs before understanding is deep enough.

**Solution:** Refactor toward deeper insight incrementally.

### 3. Ignoring Domain Feedback
Implementing patterns without validating with domain experts.

**Solution:** Collaborate continuously.

### 4. Neglecting Tests
Refactoring without test coverage is dangerous.

**Solution:** Build comprehensive test suites first.

---

## Success Indicators

You've achieved supple design when:

- ✅ New developers understand code intent quickly
- ✅ Domain experts can read and validate domain logic
- ✅ Changes are easy and localized
- ✅ Complex interactions are predictable
- ✅ Tests are clear and concise
- ✅ Code aligns with how domain experts describe the domain
- ✅ Refactoring feels safe and natural
- ✅ The model invites further refinement

---

## Further Reading

- Eric Evans, *Domain-Driven Design*, Part III: "Refactoring Toward Deeper Insight" and Chapter 10: "Supple Design"
- Martin Fowler, *Refactoring: Improving the Design of Existing Code*
- Robert C. Martin, *Clean Code*
- Joshua Bloch, *Effective Java* (for immutability and design principles)
