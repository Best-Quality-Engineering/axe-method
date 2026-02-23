---
name: ddd
description: Domain-Driven Design discipline — modeling business domains with Ubiquitous Language, Aggregates, Bounded Contexts. Principles from Eric Evans, Vaughn Vernon. Use when modeling business domains, designing aggregates/entities/value objects, or when code doesn't reflect how domain experts talk.
---

# Domain-Driven Design

The model and the implementation must be intimately connected. The code IS an expression of the model. The Ubiquitous Language is spoken everywhere — developers, domain experts, documentation, code, and conversations.

## Hard Rules

### Domain Layer Isolation
- Domain code MUST NOT import infrastructure (no database drivers, HTTP clients, frameworks).
- Domain code MUST NOT import application layer. The domain is the innermost layer.
- Repository interfaces live in the domain layer. Implementations live in infrastructure.

### Aggregate Rules
- Only Aggregate roots have repositories. Never query for non-root entities.
- References across aggregates use IDs, not object references.
- Aggregates are consistency boundaries. One transaction = one aggregate.
- A delete operation must remove everything within the aggregate boundary.
- When any change is committed, all invariants of the whole aggregate must be satisfied.

### Entity vs Value Object
- If identity matters, it is an Entity. Track it by ID.
- If only attributes matter, it is a Value Object. Make it immutable.
- No hybrid objects. Pick one.

### Naming Discipline
- Domain concepts get domain names, not technical names.
- No generic CRUD verbs in the domain layer: `approve()`, not `update()`.
- Methods express domain operations: `order.ship()`, not `order.setStatus("shipped")`.
- Changes to the language ARE changes to the model.

## Protocol

When working with domain code, follow this sequence:

```
1. IDENTIFY the Bounded Context
   |
2. CLASSIFY each concept: Entity, Value Object, Service, Aggregate, or Domain Event?
   |
3. CHECK aggregate boundaries — who owns what, what are the invariants?
   |
4. VERIFY domain layer isolation — no infrastructure imports in domain code
   |
5. APPLY naming discipline — Ubiquitous Language everywhere
   |
6. ASK: "Would a domain expert recognize these concepts?"
```

## Pattern Application Guide

| Situation | Pattern |
|-----------|---------|
| Object has lifecycle, needs tracking by identity | Entity |
| Object defined by attributes, no identity needed | Value Object |
| Operation spans multiple entities | Domain Service |
| Group of objects with consistency rules across them | Aggregate |
| Need to retrieve aggregates from persistence | Repository |
| Complex creation logic for an aggregate | Factory |
| Something significant happened in the domain | Domain Event |

## Questions That Reveal Missing Concepts

When code feels awkward, ask:

- "What is the name for this in the business?" Unnamed concepts create confusion.
- "What rules must always be true?" These are invariants; they define aggregate boundaries.
- "Who can change this?" The answer defines aggregate roots and access paths.
- "What events happen when this changes?" These are Domain Events.
- "Would a domain expert recognize this concept?" If not, the model drifts from the domain.

## Verification

### Signals On Track
- Domain experts recognize the concepts in the code.
- New requirements have obvious homes in the model.
- The code reads like a description of the business.
- Changes to business rules stay in the domain layer.

### Signals Something Is Wrong
- Adding more and more Service classes with no domain logic in entities.
- The domain layer is just data bags with getters and setters.
- Business logic is scattered across layers.
- Domain experts don't understand the code.
- Every change touches multiple bounded contexts.

## Calibration

### Anemic vs Rich Domain Model

WRONG — Anemic Domain Model:
```java
// Data bag with getters/setters
public class Order {
    private String status;
    public String getStatus() { return status; }
    public void setStatus(String status) { this.status = status; }
}

// Logic in Service
public class OrderService {
    public void shipOrder(Order order) {
        if (order.getStatus().equals("paid")) {
            order.setStatus("shipped");
        }
    }
}
```

RIGHT — Rich Domain Model:
```java
public class Order {  // Entity
    private OrderId id;  // Identity
    private OrderStatus status;  // Value Object

    public void ship() {  // Domain operation
        if (!canBeShipped()) {
            throw new OrderCannotBeShippedException();
        }
        this.status = OrderStatus.SHIPPED;
    }

    private boolean canBeShipped() {
        return status.equals(OrderStatus.PAID);
    }
}
```

### Infrastructure Leak vs Domain Isolation

WRONG — Infrastructure Leak:
```java
// Domain object knows about persistence
public class Customer {
    private JdbcTemplate jdbc;  // infrastructure in domain

    public void save() {  // persistence in domain
        jdbc.update("INSERT INTO...");
    }
}
```

RIGHT — Domain Isolation:
```java
// Domain defines interface
public interface CustomerRepository {
    Customer findById(CustomerId id);
    void save(Customer customer);
}

// Infrastructure implements it
public class JdbcCustomerRepository implements CustomerRepository {
    private final JdbcTemplate jdbc;
    // Implementation details hidden here
}
```
