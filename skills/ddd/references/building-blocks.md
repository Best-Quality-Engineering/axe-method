# DDD Building Blocks Reference

Sources: Eric Evans, "Domain-Driven Design" (2003); Vaughn Vernon, "Implementing Domain-Driven Design" (2013); Eric Evans, "Domain-Driven Design Reference" (2015)

## Layered Architecture

Isolate the domain. When domain-related code is diffused through UI, database, and infrastructure code, it becomes extremely difficult to see and reason about.

The layers:
- **User Interface** — Presents information and interprets commands
- **Application** — Coordinates tasks, no business logic, no domain state
- **Domain** — Business concepts, rules, and information. The heart.
- **Infrastructure** — Technical capabilities that support the layers above

Key rule: Concentrate all domain model code in one layer, isolated from everything else.

## Entities

Some objects are defined by their identity, not their attributes.

An Entity:
- Has continuity through a life cycle
- Has distinction independent of attributes that matter to the user
- Must have a means of distinguishing it regardless of form or history

When modeling Entities:
- Strip the definition down to the most intrinsic characteristics
- Focus on identity and lifecycle continuity
- Move other behavior to Value Objects or associated objects

## Value Objects

Many objects describe characteristics of things but have no conceptual identity.

A Value Object:
- Is defined entirely by its attributes
- Should be immutable
- Can be freely shared and copied

Why this matters:
- Tracking identity is expensive
- When immutable, sharing and reference passing become safe
- Design freedom: copy, share, or pool as needed

Rule: If the value of an attribute changes, use a different Value Object. Don't modify the existing one.

## Services

Sometimes an operation doesn't conceptually belong to any object.

A Domain Service:
- Represents an operation that is not a natural part of an Entity or Value Object
- Has an interface defined in terms of domain model elements
- Is stateless (any client can use any instance)

Beware: Don't let Services strip Entities and Value Objects of all their behavior. Use Services judiciously.

The distinction:
- **Domain Service** — Represents business operations (funds transfer, policy application)
- **Application Service** — Coordinates tasks, delegates to domain (no business logic)
- **Infrastructure Service** — Technical services (email, logging)

## Aggregates

An Aggregate is a cluster of associated objects treated as a unit for data changes.

Structure:
- **Root Entity** — The only member that outside objects can hold references to
- **Boundary** — Defines what is inside the Aggregate
- **Invariants** — Consistency rules that must be maintained across the boundary

Rules (from Evans):
- Nothing outside the boundary can hold a reference to anything inside except the root
- Only Aggregate roots can be obtained directly with database queries
- Objects within can hold references to OTHER Aggregate roots
- A delete operation must remove everything within the boundary
- When any change is committed, all invariants of the whole Aggregate must be satisfied

## Factories

When creation of an Aggregate becomes complicated, encapsulate it.

A Factory:
- Provides an interface that reflects the goals of the client
- Gives an abstract view of the created object
- Each creation method is atomic and enforces all invariants
- Should be abstracted to the type desired, not concrete classes

When a constructor is enough:
- The class is the type (no polymorphism)
- The client cares about the implementation
- All attributes are available to the client
- Construction is not complicated

## Repositories

A Repository represents all objects of a certain type as a conceptual set.

The illusion: Objects are in memory, ready to be found and used.

A Repository:
- Presents a simple model for obtaining persistent objects
- Decouples domain from persistence technology
- Communicates design decisions about object access
- Allows easy substitution of dummy implementation for testing

Rules:
- Provide Repositories only for Aggregate roots that actually need direct access
- Keep the client focused on the model, not storage mechanics

## Domain Events

Something significant happened in the domain that domain experts care about.

A Domain Event:
- Represents something that happened in the past (immutable)
- Is named in the Ubiquitous Language (past tense: `OrderShipped`, `PaymentReceived`)
- Captures the state at the time of the event
- Can trigger side effects in other aggregates or bounded contexts

When to use Domain Events:
- When a state change in one aggregate should trigger behavior in another
- When you need to maintain eventual consistency across aggregate boundaries
- When you need an audit trail of what happened in the domain
- When bounded contexts need to communicate

## Supple Design Principles

From Evans — principles for making the model easy to work with:

### Intention-Revealing Interfaces
Name classes and operations to describe their effect and purpose without revealing HOW they do what they do.

### Side-Effect-Free Functions
Place logic in functions that return results without observable side effects. Separate commands (which result in modifications) from queries (which return results).

### Assertions
State post-conditions of operations and invariants of classes explicitly. If assertions cannot be coded directly, write automated unit tests for them.

### Conceptual Contours
Decompose design elements into cohesive units that make sense together in the domain. Find the natural divisions that fit naturally into human understanding of the domain.

### Standalone Classes
Low coupling is fundamental. When you can, extract a class that is self-contained, depending on nothing.

### Closure of Operations
Where it fits, define an operation whose return type is the same as the type of its argument(s). This creates a closed set that doesn't leak out of the domain.

## Sources

- Eric Evans, "Domain-Driven Design: Tackling Complexity in the Heart of Software" (2003)
- Vaughn Vernon, "Implementing Domain-Driven Design" (2013)
- Eric Evans, "Domain-Driven Design Reference" (2015)
