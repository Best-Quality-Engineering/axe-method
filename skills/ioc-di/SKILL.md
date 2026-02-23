---
name: ioc-di
description: Inversion of Control and Dependency Injection discipline — constructor injection, composition roots, testable design. Principles from Robert C. Martin, Martin Fowler, Mark Seemann. Use when designing new components, adding dependencies, writing constructors/initializers, structuring application entry points, or when tests become hard to write.
---

# Inversion of Control & Dependency Injection

Dependencies flow inward through constructors. Composition happens once, at the entry point. Tests prove the design is right.

## Core Principles

### The Dependency Inversion Principle

High-level modules must not depend on low-level modules. Both depend on abstractions. Abstractions must not depend on details. Details depend on abstractions.

This inverts the natural dependency direction. Instead of business logic depending on infrastructure, both depend on a shared abstraction (protocol/interface) that the business logic owns.

### Constructor Injection Is the Default

Every dependency a type needs must be provided through its initializer. This guarantees:
- Objects are valid at creation (no partially-initialized state)
- Dependencies are visible in the API (not hidden inside methods)
- Immutable fields are possible (assigned once, never mutated)

Use setter injection only when the dependency genuinely changes at runtime. This is rare.

### The Composition Root

A single location in the application where the entire object graph is assembled. It lives as close to the application entry point as possible.

The Composition Root is the ONLY place that knows how to wire concrete types together. All other modules depend on abstractions, never on the container or on concrete types they don't own.

For detailed patterns and examples, see [references/composition-root.md](references/composition-root.md).

### Pure DI Over Containers

Compose object graphs by hand in the Composition Root — no framework, no container, just constructors calling constructors. This makes the wiring explicit, type-checked, and debuggable.

Use a DI container only when the graph becomes genuinely complex (50+ registrations). Start with Pure DI. You can always add a container later because composition is isolated in the Composition Root.

## Hard Rules (The Six Rules)

### Rule 1: One Designated Constructor

Every type has one designated constructor that accepts all stored state. Its body only assigns — no logic, no object creation, no function calls.

All other constructors are convenience constructors that supply defaults and must call the designated constructor. This guarantees every construction path goes through the same complete initialization.

```
class Player:
    // Designated — accepts ALL state
    constructor(device: Device, client: HttpClient, status: Status?)
        this.device = device      // assignment only
        this.client = client      // assignment only
        this.status = status      // assignment only

    // Convenience — supplies defaults, funnels through designated
    constructor(device: Device)
        this(device, HttpClient.standard(), null)
```

Named static factory methods are the third layer — they contain domain knowledge and call a constructor with the result.

| Allowed in designated constructor | Not allowed in designated constructor |
|-----------------------------------|---------------------------------------|
| Field assignment (`this.x = x`) | Object creation (`new Session(...)`) |
| Null coalescing (`name ?? id`) | Function/method calls (`factory()`) |
| Nothing else | Errors / exceptions |
| | Mutation or transformation |
| | Conditional logic |

### Rule 2: No Work in Constructors

The constructor assigns. It does not do anything — no network calls, no file I/O, no validation that throws, no calls to injected dependencies. Work that must happen before the object is "ready" belongs in an explicit lifecycle method (`start()`, `configure()`, `initialize()`).

```
// WRONG — work in constructor
class Poller:
    constructor(service: DataService)
        this.service = service
        this.data = service.fetch()    // calls mock before test is ready

// RIGHT — assign in constructor, work in lifecycle
class Poller:
    constructor(service: DataService)
        this.service = service         // assignment only

    start()
        this.data = service.fetch()    // called explicitly after construction
```

### Rule 3: No Mock Methods on Production Types

Never add methods whose sole purpose is testing or previewing (`applyMockState()`, `setTestData()`, `resetForTesting()`). If you can't test it without a special method, the design is wrong. Fix the design: add a constructor parameter, extract a protocol, or inject the dependency.

### Rule 4: Configure Dependencies, Not Objects

To set up test or preview state, configure the injected dependencies — never reach into an object and mutate its internals.

| Approach | How |
|----------|-----|
| Mock network | Interface-conforming mock or stub |
| Pre-configured state | Pass state through constructor parameters |
| Mock I/O | Intercept at the protocol/interface boundary |

### Rule 5: Access Control Enforces Boundaries

Properties managed by internal lifecycle use restricted setters. If external code needs to set a value, it flows through the constructor or an interface method — never a public setter.

### Rule 6: Protocol-Based Abstractions at Boundaries

Network, persistence, and external service boundaries use protocols so implementations can be swapped:

```
protocol DiscoveryProtocol {
    func start()
    func stop()
}

// Concrete implementations
class NetworkTransport: DiscoveryProtocol { ... }
class LocalTransport: DiscoveryProtocol { ... }
class MockTransport: DiscoveryProtocol { ... }  // test double

// Coordinator depends on the protocol, never the concrete type
class DiscoveryService {
    init(transport: any DiscoveryProtocol)
}
```

## Protocol: TDD Synergy

DI and TDD form a feedback loop:

```
Tests are hard to write
        |
Design has a DI problem
        |
Fix the injection
        |
Tests become trivial
        |
Design is validated
```

### Tests as DI Diagnostics

| Test Signal | DI Diagnosis | Fix |
|-------------|-------------|-----|
| Long setup code | Too many dependencies | Extract a coordinator, split responsibilities |
| Need to mock internals | Hidden dependencies | Promote to constructor parameter |
| Can't substitute a collaborator | Concrete type dependency | Extract protocol at the boundary |
| Test requires special production method | Mock method on production type | Add constructor parameter instead |

### The Dependency Chain Pattern

When component A depends on B, and B depends on C, A must know nothing about C. Not as a property, not as a parameter, not even as a word in its documentation.

```
App (composition root)
 +-- creates Transport (C)
 +-- creates Discovery(transport: C)  (B depends on C)
 +-- creates Manager(discovery: B)    (A depends on B, knows nothing about C)
```

The composition root is the only place that sees the full chain. Each component sees only its immediate collaborators.

Enforcement: If component A mentions C in any form — type annotation, parameter, doc comment, factory method — the boundary is broken. Move the wiring to the composition root.

## Anti-Patterns

| Anti-Pattern | What It Looks Like | Why It's Wrong |
|-------------|-------------------|----------------|
| **Service Locator** | `ServiceLocator.resolve(Foo.self)` | Hides dependencies; every type depends on the locator |
| **Control Freak** | `let db = Database()` inside a method, or a constructor with `= ConcreteType()` default referencing a type that should be abstracted | Untestable; dependency is invisible from the outside |
| **Constrained Construction** | Requiring all implementations to have a specific constructor signature | Leaks infrastructure requirements into domain types |
| **Ambient Context** | `Environment.current.logger` | Global mutable state disguised as DI |

## Execution Checklist

When designing or reviewing a component:

1. **List the dependencies.** What does this type need to do its job?
2. **Define abstractions.** Should any dependency be behind a protocol?
3. **Write the constructor.** One constructor, parameters = stored state, body = assignment only.
4. **Write the test.** Can you instantiate it with a mock? If not, go back to step 2.
5. **Check the chain.** Does this type mention any transitive dependency it shouldn't know about?
6. **Check access control.** Are mutable properties using the right setter restriction?
7. **Write the factory.** Convenient creation paths as static methods, not extra constructors.

## Verification

- Can you instantiate the type with a mock for every dependency? If not, the design prevents injection.
- Does the type mention any transitive dependency? If so, the boundary is broken.
- Is any dependency created inside the type instead of injected? If so, it's a Control Freak.
- Does any test require a special method on the production type? If so, the design is wrong.
