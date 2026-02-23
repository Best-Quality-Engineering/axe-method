# Composition Root — Detailed Reference

Sources: Mark Seemann, "Dependency Injection: Principles, Practices, and Patterns" (Seemann & van Deursen, 2019); Mark Seemann, "Composition Root" (blog.ploeh.dk, 2011); Martin Fowler, "Inversion of Control Containers and the Dependency Injection pattern" (2004)

## The Principle

The Composition Root is a single location in an application where the entire object graph is assembled. Only applications should have Composition Roots — libraries and frameworks should not. A DI container should only be referenced from the Composition Root. All other modules should have no reference to the container.

## Where the Composition Root Lives

| Framework | Composition Root Location |
|-----------|--------------------------|
| SwiftUI | `@main App.init` or `WindowGroup` body |
| UIKit | `AppDelegate` / `SceneDelegate` |
| Console app | `main()` |
| ASP.NET | `Program.cs` / `Startup` |
| Spring | Application context configuration |
| Express/Koa | Server setup / app entry |

## Pure DI Example

```
@main
struct MyApp: App {
    // Composition Root — all wiring happens here
    private let transport = NetworkTransport()
    private let discovery: DiscoveryService
    private let manager: DeviceManager

    init() {
        discovery = DiscoveryService(transport: transport)
        manager = DeviceManager(discovery: discovery)
    }

    var body: some Scene {
        WindowGroup {
            ContentView()
                .environment(manager)
        }
    }
}
```

Key observations:
- `transport -> discovery -> manager` — the dependency chain is assembled top-down
- `DeviceManager` receives `DiscoveryService`, never `NetworkTransport`
- No type outside this file knows how the graph is wired

## The Dependency Chain

```
Composition Root (knows everything)
 |
 +-- creates Transport (concrete)
 |
 +-- creates DiscoveryService(transport:)
 |     +-- knows Transport protocol, not concrete type
 |
 +-- creates DeviceManager(discovery:)
       +-- knows DiscoveryService, nothing about Transport
```

Each layer sees only its immediate dependencies. The composition root is the only place with full visibility.

## Platform-Specific Composition Roots

When multiple platforms share components but wire them differently:

```
// Platform A: full transport in environment
@main struct PlatformAApp: App {
    private let transport = NetworkTransport()
    // ... full wiring
}

// Platform B: simplified wiring, subset of capabilities
@main struct PlatformBApp: App {
    private let transport = LocalTransport()
    // ... different wiring, same protocols
}
```

The protocols don't change. Only the composition root changes — this is the power of DI.

## Common Mistakes

### Mistake 1: Composing in Library Code

```
// WRONG — library type wires its own dependency
class DiscoveryService {
    private let transport = NetworkTransport()  // Control Freak
}

// RIGHT — dependency is injected
class DiscoveryService {
    private let transport: any DiscoveryProtocol
    init(transport: any DiscoveryProtocol) { ... }
}
```

Libraries provide defaults via factory methods, but never hide dependencies.

### Mistake 2: Passing the Container

```
// WRONG — passing the container is Service Locator
class DeviceManager {
    init(container: Container) {
        self.discovery = container.resolve(DiscoveryService.self)
    }
}

// RIGHT — pass the dependency directly
class DeviceManager {
    init(discovery: DiscoveryService) { ... }
}
```

### Mistake 3: Leaking Transitive Dependencies

```
// WRONG — DeviceManager knows about Transport
class DeviceManager {
    init(discovery: DiscoveryService, transport: NetworkTransport) { ... }
}

// RIGHT — DeviceManager only knows its direct dependency
class DeviceManager {
    init(discovery: DiscoveryService) { ... }
}
```

If `DeviceManager` needs something from transport, the design needs restructuring — perhaps `DiscoveryService` should expose the needed information, or a new abstraction is needed.

### Mistake 4: Multiple Composition Points

```
// WRONG — wiring happens in multiple places
class ViewControllerA {
    let service = DiscoveryService(transport: NetworkTransport())  // wiring here
}
class ViewControllerB {
    let service = DiscoveryService(transport: LocalTransport())  // and here
}

// RIGHT — one composition root, shared instances
@main struct App {
    let discovery = DiscoveryService(transport: NetworkTransport())
    // Both views receive the same instance via environment/injection
}
```

## Testing the Composition Root

The composition root itself is not unit-tested — it's infrastructure. Instead:

1. **Unit tests** — Each component is tested in isolation with mock dependencies
2. **Integration tests** — Verify that real components work together
3. **UI tests** — Verify the full graph works end-to-end

The composition root's correctness is proven by the integration and UI tests passing.

## Sources

- Mark Seemann, [Composition Root](https://blog.ploeh.dk/2011/07/28/CompositionRoot/) (2011)
- Mark Seemann & Steven van Deursen, [Dependency Injection: Principles, Practices, and Patterns](https://www.manning.com/books/dependency-injection-principles-practices-patterns) (2019)
- Martin Fowler, [Inversion of Control Containers and the Dependency Injection pattern](https://martinfowler.com/articles/injection.html) (2004)
- Robert C. Martin, [The Dependency Inversion Principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle) (1994)
