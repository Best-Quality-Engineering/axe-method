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

Work through each area using discovery and structured questions.

### 1. Protocols and Wire Formats

**Discover first:** Use Grep to find URL constants, HTTP methods, WebSocket frames, UDP packet definitions, binary format parsing.

**Use AskUserQuestion:**
```
"I found these protocol implementations:
  - [HTTP endpoints, WebSocket connections, UDP broadcasts, etc.]

Are these the protocols this component uses?"
  Options: Yes, those are all of them / There are more / Some of these are wrong / No protocols involved
```

For each protocol, use AskUserQuestion:
- **"What is the wire format?"** — Options: JSON / XML / Protobuf / Raw bytes / Other
- **"What is the communication pattern?"** — Options: Request-response / Long-poll / Push/subscribe / Broadcast / Event stream
- **"What are the timing constraints?"** — Options: Standard timeouts / Custom intervals / Real-time / Let me specify

**Discover exact structures:** Read existing parser/encoder code to extract field names, types, and formats. Present to user for confirmation.

### 2. APIs and Endpoints

**Discover first:** Use Grep/Glob to find route definitions, URL builders, API client methods, response models.

**Use AskUserQuestion:**
```
"I found these API endpoints:
  - [method] [path] → [response type]
  - [method] [path] → [response type]

Are these correct and complete?"
  Options: Yes / Add missing endpoints / Some need correction / Let me describe the API
```

For each endpoint, confirm:
- **Request schema** — Present discovered request types, ask for corrections
- **Response schema** — Present discovered response types, ask for corrections
- **Error handling** — Options: HTTP status codes / Error body / Retry logic / All of the above

### 3. Data Structures and Algorithms

**Discover first:** Use Task (Explore) to find model definitions, collection types, cache implementations, algorithm implementations.

**Use AskUserQuestion:**
```
"I found these key data structures:
  - [models, collections, caches]

Are these the core data structures?"
  Options: Yes / Add more / Some need changes / Let me describe them
```

For lifecycle management:
- **"How are these created?"** — Options: Parsed from network / User-created / System-provided / Computed
- **"What caching strategy applies?"** — Options: TTL / LRU / Manual invalidation / No caching / Let me explain

### 4. UI Implementation

The rendering mechanics that implement the Experience spec's requirements.

**Discover first:** Use Task (Explore) to find custom views, view builders, style constants, color/font definitions, SF Symbol usage, layout code.

**Use AskUserQuestion:**
```
"The Experience spec says the user can [behavior].
I found this implementation: [discovered view/control].

Is this the right rendering approach?"
  Options: Yes / It needs changes / Not implemented yet / Let me describe
```

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

### 5. Testing Infrastructure

**Discover first:** Use Glob to find test files, mock implementations, test fixtures, snapshot references.

**Use AskUserQuestion:**
```
"I found this test coverage:
  - Unit tests: [count/areas]
  - UI tests: [count/areas]
  - Snapshot tests: [count/areas]
  - Mocks: [list]

What testing strategy should the spec prescribe?"
  Options: Keep current approach / Expand coverage / Change strategy / Let me describe
```

For each area:
- **"What test types apply?"** — Options: Unit / Integration / Snapshot / UI / End-to-end (multiSelect)
- **"What mocks are needed?"** — Present discovered mocks, ask what's missing

### 6. Platform-Specific Implementation

**Discover first:** Use Grep to find #if os(), platform-specific targets, conditional compilation, platform availability checks.

**Use AskUserQuestion:**
```
"I found these platform-specific implementations:
  - [platform]: [specific code/controls/APIs]

Is this the intended platform split?"
  Options: Yes / Needs changes / More platforms needed / Let me describe
```

- **"What is shared across platforms?"** — Options: All views / Models + logic only / Nothing / Let me list
- **"What conditional compilation is needed?"** — Present discovered #if blocks, confirm or correct

## Classification Guard

Watch for concerns that belong in sibling spec types:

| User says... | Guide them to... |
|-------------|-----------------|
| "The user should be able to browse content" | That's Experience — here we specify the list/grid control, cell layout, pagination mechanism |
| "The browsing system should be independent of the player system" | That's Architecture (boundary/seam) — here we specify the internal implementation of each |
| "It should feel smooth and responsive" | That's Experience — here we specify the prefetch strategy, frame budget, async loading that achieves it |

Say: *"That's the 'what' — let's capture it in the Experience spec. Here we'll specify the 'how' that makes it happen."*

## Phase 3: Draft Output

After exploration, produce a draft Engineering spec:

1. Document protocols and wire formats with exact structures
2. Specify APIs with request/response schemas
3. Detail UI implementation — controls, layouts, visual specs, accessibility
4. Include data structures, algorithms, and lifecycle management
5. Place in `Documents/Specifications/Engineering/{Domain}/v0.1.0/`
6. Flag any cross-cutting concerns that need Experience or Architecture specs

**Use AskUserQuestion before writing:**
```
"Here's the draft Engineering spec structure:
  - [file 1]: covers [protocols/APIs]
  - [file 2]: covers [UI implementation]
  - [file 3]: covers [data structures]

Ready to write these drafts?"
  Options: Write them / Let me review the content first / Make changes
```
