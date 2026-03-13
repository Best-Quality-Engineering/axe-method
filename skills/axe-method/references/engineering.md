# Engineering Spec Guide

Interactive guide for defining and refining Engineering specifications. The Engineering spec describes how individual components work internally — protocols, APIs, data structures, and UI implementation.

## When to Use

- Starting a new Engineering spec from scratch
- Experience and/or Architecture specs exist and need implementation detail
- Defining protocols, APIs, or UI mechanics
- User says `/axe-method:engineering`

## Phase 1: Discover

Before asking questions, use agent tools to find what already exists.

**Use Task (Explore) agents to scan for:**
- Network clients, HTTP calls, WebSocket connections — protocol implementations
- API endpoint definitions, URL constants, request builders
- UI components — custom views, cells, controls, layout code
- Data models, codable structs, parsing logic
- Test files — existing test coverage, mocks, fixtures

**Use AskUserQuestion to present findings:**
```
"I found these implementation areas in the codebase:
  - Networking: [HTTP clients, endpoints, protocols]
  - UI: [custom views, layouts, controls]
  - Models: [data structures, parsers]
  - Tests: [test files, mocks]

Which areas are in scope for this Engineering spec?"
  Options: All of them / Select specific areas / These are wrong, let me explain
```

### Domain Context

If `/axe-method:specify` has already established the domain, skip this. Otherwise:

- What component or system are we specifying?
- What do the Experience and Architecture specs require? (Read them first using the Read tool.)
- What is the ubiquitous language? (If already established, confirm.)

## Phase 2: Structured Exploration

### Constitutional Compliance Check

Before exploring implementation areas, check whether the project has a Constitution (typically `Documents/Constitution/` or a top-level `CONSTITUTION.md`). If one exists:

1. Read it using the Read tool
2. Identify any constitutional constraints that apply to engineering decisions (e.g., dependency restrictions, technology mandates, platform requirements, prohibited patterns)
3. Keep these constraints active throughout exploration — flag violations immediately rather than discovering them during review

If no Constitution exists, proceed.

### Exploration Approach

Work through each area using discovery, structured questions, and active analysis. For each area, apply these techniques:

- **Assumption Surfacing** — For each area explored, explicitly state what you are assuming. Each assumption either becomes a confirmed requirement or gets marked `[NEEDS CLARIFICATION]`.
- **Negative Requirements** — For each implementation area, ask what constraints exist: dependency restrictions, things that should NOT be used, patterns that are prohibited.

State your conclusions with reasoning and let the user correct, rather than asking "are these correct?"

### 1. Protocols and Wire Formats

**Discover first:** Use Grep to find URL constants, HTTP methods, WebSocket frames, UDP packet definitions, binary format parsing.

**Analyze and present conclusions:**
```
"Based on the codebase, this component uses these protocols:
  - [protocol]: [wire format], [communication pattern] — [evidence from code]

I'm assuming [stated assumptions about protocol behavior].
Constraints I see: [e.g., no third-party networking libraries, must use platform HTTP stack]."
  Options: Correct / Some corrections needed / There are more / No protocols involved
```

For each protocol, use AskUserQuestion:
- **"What is the wire format?"** — Options: JSON / XML / Protobuf / Raw bytes / Other
- **"What is the communication pattern?"** — Options: Request-response / Long-poll / Push/subscribe / Broadcast / Event stream
- **"What are the timing constraints?"** — Options: Standard timeouts / Custom intervals / Real-time / Let me specify

**Example Mapping:** For each protocol, provide concrete request/response examples showing the wire format. Read existing parser/encoder code to extract field names, types, and formats. Present actual byte sequences, JSON bodies, or XML structures — not abstractions. This surfaces edge cases in encoding, optional fields, and format variations that abstract descriptions miss.

**Negative Requirements:** Ask what dependencies or approaches are off-limits for protocol implementation (e.g., "no Alamofire", "no raw sockets on watchOS").

### 2. APIs and Endpoints

**Discover first:** Use Grep/Glob to find route definitions, URL builders, API client methods, response models.

**Analyze and present conclusions:**
```
"I found these API endpoints and reconstructed their contracts:
  - [method] [path] → [response type]
    Example: [concrete request/response from code or docs]
  - [method] [path] → [response type]
    Example: [concrete request/response from code or docs]

I'm assuming [stated assumptions about API behavior, auth, versioning]."
  Options: Correct / Add missing endpoints / Some need correction / Let me describe the API
```

**Example Mapping:** For each endpoint, include a concrete request/response pair extracted from code, test fixtures, or documentation. Show actual payloads, not just schemas. This catches field naming mismatches, optional vs required confusion, and undocumented behaviors.

For each endpoint, confirm:
- **Request schema** — Present discovered request types with example, ask for corrections
- **Response schema** — Present discovered response types with example, ask for corrections
- **Error handling** — Options: HTTP status codes / Error body / Retry logic / All of the above

**Negative Requirements:** Ask what constraints apply — rate limits, auth requirements, endpoints that must NOT be called in certain states.

### 3. Data Structures and Algorithms

**Discover first:** Use Task (Explore) to find model definitions, collection types, cache implementations, algorithm implementations.

**Analyze and present conclusions:**
```
"I found these key data structures:
  - [models, collections, caches]

Based on their usage, I believe [stated assumptions about ownership, mutability, threading].
Constraints: [e.g., must be Codable, no Core Data, etc.]"
  Options: Correct / Add more / Some need changes / Let me describe them
```

For lifecycle management:
- **"How are these created?"** — Options: Parsed from network / User-created / System-provided / Computed
- **"What caching strategy applies?"** — Options: TTL / LRU / Manual invalidation / No caching / Let me explain

**Assumption Surfacing:** Explicitly state assumptions about thread safety, identity vs equality semantics, and ownership rules. Each becomes a documented requirement or a `[NEEDS CLARIFICATION]` marker.

**Negative Requirements:** Ask what storage or persistence approaches are off-limits (e.g., "no Core Data", "no singletons", "no mutable shared state").

### 4. UI Implementation

The rendering mechanics that implement the Experience spec's requirements.

**Discover first:** Use Task (Explore) to find custom views, view builders, style constants, color/font definitions, SF Symbol usage, layout code.

**Analyze and present conclusions:**
```
"The Experience spec says the user can [behavior].
I found this implementation: [discovered view/control].

This appears to use [platform framework/API] for rendering. I'm assuming the platform provides [specific capability — e.g., 'SwiftUI List with lazy loading', 'UICollectionView compositional layout'].

Platform assumptions to validate:
  - [assumption 1 — e.g., 'NavigationSplitView available on all target OS versions']
  - [assumption 2 — e.g., 'SF Symbols 5 available across all platforms']"
  Options: Correct / It needs changes / Not implemented yet / Let me describe
```

**Platform Assumption Testing:** For each UI component, explicitly state what you are assuming the platform provides. Unvalidated platform assumptions become bugs. Check: minimum OS version support, API availability, framework behavior differences across platforms.

For each Experience behavior, use AskUserQuestion to specify the UI:
- **"What control implements this?"** — Options: [discovered controls] / Different control / Custom implementation
- **"What are the visual specs?"** — Present discovered sizes/colors/fonts, ask for corrections or additions
- **"How do we implement the accessibility requirements from the Experience spec?"** — Options: VoiceOver labels / Dynamic Type / Reduced Motion / All / Let me specify

Probe the relationship to Experience using AskUserQuestion:
```
"The Experience spec says the user gets [feedback].
How should this be rendered?"
  Options: Animation / State change in existing view / Toast/alert / Sound/haptic / Let me describe
```

**Negative Requirements:** Ask what UI patterns or frameworks are off-limits (e.g., "no UIKit on macOS", "no third-party UI libraries", "no custom gesture recognizers where system gestures suffice").

### 5. Testing Infrastructure

**Discover first:** Use Glob to find test files, mock implementations, test fixtures, snapshot references.

**Analyze and present conclusions:**
```
"I found this test coverage:
  - Unit tests: [count/areas]
  - UI tests: [count/areas]
  - Snapshot tests: [count/areas]
  - Mocks: [list]

I'm assuming the mock strategy is [stated assumption — e.g., 'mock at protocol boundaries only, never mock the manager'].
Constraints: [e.g., 'no mocking frameworks as dependencies', 'snapshot precision relaxed for CI']."
  Options: Correct / Expand coverage / Change strategy / Let me describe
```

For each area:
- **"What test types apply?"** — Options: Unit / Integration / Snapshot / UI / End-to-end (multiSelect)
- **"What mocks are needed?"** — Present discovered mocks, ask what's missing

**Negative Requirements:** Ask what testing approaches are off-limits (e.g., "no mocking the main manager", "no static test helpers in production code", "no test-only compile flags in production targets").

### 6. Platform-Specific Implementation

**Discover first:** Use Grep to find #if os(), platform-specific targets, conditional compilation, platform availability checks.

**Analyze and present conclusions:**
```
"I found these platform-specific implementations:
  - [platform]: [specific code/controls/APIs]

Platform assumptions to validate:
  - [platform]: assumes [capability] is available — [verified/unverified]
  - [platform]: assumes [API] behaves the same as [other platform] — [verified/unverified]

Constraints: [e.g., 'watchOS cannot use POSIX sockets', 'tvOS has no touch input']"
  Options: Correct / Needs changes / More platforms needed / Let me describe
```

**Platform Assumption Testing:** For each platform, explicitly state what platform capabilities you are assuming. Check: API availability per platform, framework behavior differences, hardware constraints (no haptics on macOS, no keyboard on tvOS, etc.). Unvalidated assumptions here become platform-specific bugs.

- **"What is shared across platforms?"** — Options: All views / Models + logic only / Nothing / Let me list
- **"What conditional compilation is needed?"** — Present discovered #if blocks, confirm or correct

**Negative Requirements:** Ask what is explicitly excluded per platform (e.g., "no LSDP on watchOS", "no sidebar on phone", "no focus engine code on non-tvOS").

## Cross-References

Engineering specs don't exist in isolation. These are the key intersection points with Experience and Architecture:

| When you specify... | Coordinate with Experience on... | Coordinate with Architecture on... |
|--------------------|----------------------------------|-----------------------------------|
| Protocols and wire formats | Whether protocol choices support the Experience's real-time, offline, or sync requirements | Whether protocols align with the seams defined in Architecture; do they respect boundary crossing rules? |
| APIs and endpoints | Whether the API surface supports all Experience behaviors; are there capabilities with no API backing? | Whether endpoints respect bounded context ownership; does one context's API leak another context's data? |
| UI implementation | Whether the rendering mechanics deliver the Experience spec's behavioral intent (feedback, transitions, affordances) | Whether UI components respect Architecture's component boundaries and layer structure |
| Data structures | Whether data models support Experience's state and transition requirements | Whether models align with Architecture's aggregate boundaries and consistency rules |
| Testing infrastructure | Whether test types cover all Experience behaviors at every applicable level | Whether test boundaries align with Architecture's seam and component structure |
| Platform-specific implementation | Whether platform-specific code delivers the Experience's platform adaptation requirements | Whether platform splits respect Architecture's decomposition; do platform-specific components violate layer rules? |

Flag these intersections explicitly when you encounter them. Don't assume the Experience or Architecture specifier will discover them independently.

## Classification Guard

Watch for concerns that belong in sibling spec types:

| User says... | Guide them to... |
|-------------|-----------------|
| "The user should be able to browse content" | That's Experience — here we specify the list/grid control, cell layout, pagination mechanism |
| "The browsing system should be independent of the player system" | That's Architecture (boundary/seam) — here we specify the internal implementation of each |
| "It should feel smooth and responsive" | That's Experience — here we specify the prefetch strategy, frame budget, async loading that achieves it |

Say: *"That's the 'what' — let's capture it in the Experience spec. Here we'll specify the 'how' that makes it happen."*

### Ambiguous Cases

When you're unsure whether something is Engineering or belongs elsewhere, use this decision framework:

1. **"Is this only one way to implement a behavior?"** → Engineering. *"Use WebSocket for real-time updates"* is a protocol choice — Engineering. Another team might use SSE and deliver the same Experience.
2. **"Does this describe what the product can do, not how?"** → Experience. *"Content updates appear in real time"* is the capability — Experience. The WebSocket implementation is Engineering.
3. **"Does this change which parts exist or how they connect?"** → Architecture. *"Real-time updates flow through a dedicated event bus"* is a structural decision — Architecture. How the event bus is implemented is Engineering.
4. **"Does this span the boundary?"** → Split it. The implementation detail stays in Engineering, the behavior it delivers goes in Experience, and any structural implication goes in Architecture. Example: *"The app should work offline"* — the capability is Experience, the sync architecture (what caches where, conflict resolution strategy) is Architecture, and the specific caching library and sync protocol are Engineering.

## Phase 3: Draft Output

After exploration, produce a draft Engineering spec:

1. Document protocols and wire formats with exact structures
2. Specify APIs with request/response schemas
3. Detail UI implementation — controls, layouts, visual specs, accessibility
4. Include data structures, algorithms, and lifecycle management
5. Place in `Documents/Specifications/{domain}/v0.1.0/engineering.md` — each feature subdirectory contains its own engineering spec alongside its experience and architecture specs
6. Use ES-NNN numbering for implementation specs (e.g., ES-001, ES-002) to enable cross-referencing from other specs and tracking
7. Flag any cross-cutting concerns that need Experience or Architecture specs

**Uncertainty markers:** Any unresolved gaps from Phase 2 — unanswered questions, unvalidated platform assumptions, or ambiguous requirements — must be marked in the draft with `[NEEDS CLARIFICATION: description of what's unresolved]`. These markers are not optional polish; they are the mechanism that prevents assumptions from silently becoming implementation decisions.

**Use AskUserQuestion before writing:**
```
"Here's the draft Engineering spec structure:
  - [file 1]: covers [protocols/APIs]
  - [file 2]: covers [UI implementation]
  - [file 3]: covers [data structures]
  - [N] items marked [NEEDS CLARIFICATION]

Ready to write these drafts?"
  Options: Write them / Let me review the content first / Make changes
```
