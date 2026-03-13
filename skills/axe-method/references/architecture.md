# Architecture Spec Guide

Interactive guide for defining and refining Architecture specifications. The Architecture spec defines how the system is structured and how its parts connect — bounded contexts, system decomposition, aggregates, boundaries, seams, composition rules, context maps, and the rules for crossing them.

## When to Use

- Starting a new Architecture spec from scratch
- An Experience spec exists but the system structure hasn't been defined
- Unclear where the seams should be
- User says `/axe-method:architecture`

## Phase 1: Discover

Before asking questions, use agent tools to find what already exists.

**Use Task (Explore) agents to scan for:**
- Bounded context indicators — distinct subdomains, modules with their own models, areas where language diverges
- Aggregate patterns — entity clusters, repository roots, transactional boundaries
- Module/package/framework boundaries — separate targets, packages, modules
- Protocol/interface definitions — the contracts between components
- Dependency injection patterns — composition roots, factories, containers
- Layer structure — how imports flow, what depends on what
- Threading patterns — main thread annotations, async/await, dispatch queues, actors

**Use AskUserQuestion to present findings (assertion-based — state your conclusion, let them correct):**
```
"I found these structural boundaries in the codebase:
  - [list of modules/packages/targets]
  - [potential bounded contexts]
  - [dependency direction observed]

Based on this, the architecture appears to be [your conclusion with reasoning].

Is this assessment correct?"
  Options: Yes, that's the structure / It needs changes / Let me describe the architecture
```

### Domain Context

If `/axe-method:specify` has already established the domain, skip this. Otherwise:

- What system are we structuring?
- What does the Experience spec require? (If an Experience spec exists, read it first using the Read tool.)
- What is the ubiquitous language? (If already established, confirm.)

## Phase 2: Structured Exploration

Work through each area using discovery and structured questions.

### 0. Constitutional Compliance Check

Before exploring structure, check whether a Constitution exists (e.g., `Documents/Specifications/constitution/` or a project-level constitution file). If it does, read it. The Constitution defines invariants that the architecture must satisfy — non-negotiable constraints that override exploratory conclusions. Reference specific constitutional rules (e.g., "per Constitution rule C-003") when they constrain an architectural decision.

If no Constitution exists, note this and proceed.

### 1. System Structure and Bounded Contexts

**Discover first:** Use Glob/Grep to find module definitions, package manifests, target configurations, top-level directories that imply subsystems. Look for natural domain boundaries — areas where the ubiquitous language shifts or where models diverge.

**Event Storming — discover contexts through events:** Trace the key domain events through the system. For each event (e.g., "player discovered", "track changed", "volume adjusted"), identify which context produces it and which contexts consume it. Events that flow between modules reveal natural bounded context boundaries. Events that stay within a module confirm cohesion.

**Use AskUserQuestion (assertion-based):**
```
"Based on the code structure and event flow, I see these bounded contexts:
  - [context A]: owns [domain concepts], produces [events], consumes [events]
  - [context B]: owns [domain concepts], produces [events], consumes [events]

I'm assuming [stated assumption about why these are separate contexts].

Does this decomposition match your intent?"
  Options: Yes / Some should be merged / Some need splitting / The assumption is wrong — let me explain
```

For each bounded context, confirm:
- **What subdomain does it serve?** — What part of the domain has its own language and rules?
- **What does it own exclusively?** — What concepts, state, and behavior belong to it and nothing else?
- **What is its relationship to the Experience?** — What product capabilities does this context support?
- **What are we assuming?** — State each assumption explicitly. Unconfirmed assumptions become `[NEEDS CLARIFICATION]` markers in the draft.

**Context Mapping — Use AskUserQuestion:**
```
"Based on imports and event flow, I believe these contexts relate as:
  - [A] → [B]: customer-supplier (A serves B because [reasoning])
  - [A] ↔ [C]: shared kernel ([shared concepts])

Is this correct, or are the relationships different?"
  Options: Correct / Some relationships are wrong / Let me describe each relationship
```

### 2. Aggregates and Consistency Boundaries

**Discover first:** Use Grep to find entity clusters, transactional boundaries, repository patterns, factory methods that create groups of related objects.

**Use AskUserQuestion (assertion-based):**
```
"I found these entity/model clusters and believe these are the consistency boundaries:
  - [aggregate root A] with [child entities] — I'm assuming [A] owns [invariant] because [reasoning]
  - [aggregate root B] with [child entities] — I'm assuming [B] controls [invariant] because [reasoning]

Are these the right consistency boundaries?"
  Options: Yes / Some need different boundaries / An assumption is wrong / Let me describe the aggregates
```

For each aggregate, confirm:
- **What is the root?** — What entity controls access to the cluster?
- **What invariants must it enforce?** — What rules must always hold within this boundary?
- **What can change independently?** — What is outside this consistency boundary?
- **What are we assuming?** — Each unconfirmed assumption about ownership or invariants becomes a `[NEEDS CLARIFICATION]` marker.

### 3. Component Boundaries

**Discover first:** Use Glob/Grep to find module definitions, package manifests, target configurations, top-level directories that imply components.

**Boundary Stress Testing:** For each component boundary you discover, mentally replace the component. Ask: "If we swapped out [component A] for a completely different implementation, what would break?" If the answer is "nothing outside A" — the boundary is clean. If other components would break, the boundary is leaking.

**Use AskUserQuestion (assertion-based):**
```
"I found these component boundaries:
  - [component A]: contains [files/types]
  - [component B]: contains [files/types]

Stress test: If [component A] were replaced, [component B] would [break/not break] because [reasoning].
I'm assuming [stated assumption about coupling].

Are these the right boundaries?"
  Options: Yes / Merge some of these / Split some of these / The assumption is wrong / Let me describe the components
```

For each component, confirm:
- **What does it own?** — What data, state, and behavior belongs to it exclusively?
- **What bounded context does it belong to?** — Or does it span contexts?
- **What doesn't belong to any component?** — Shared concerns, cross-cutting functionality
- **What breaks if you replace it?** — If the answer is "too much," the boundary needs work.

### 4. Seams

The most important architectural decisions.

**Discover first:** Use Grep to find protocol definitions, public API surfaces, import statements that cross module boundaries.

**Use AskUserQuestion for each pair of connected components (assertion-based):**
```
"Between [Component A] and [Component B], I found these crossing points:
  - [imports/calls/protocols discovered]

Based on this, I believe [specific things] should cross this seam and [specific things] should NOT.
I'm assuming [stated assumption about the boundary contract].

Is this correct?"
  Options: Correct / Some things shouldn't cross / More needs to cross / The assumption is wrong / Let me explain
```

**Negative Requirements:** For each seam, explicitly identify what must NOT cross it. These become AS-NNN rules in the draft (e.g., "AS-012: Implementation details of discovery protocol must not leak into the Player context"). Negative requirements are often more valuable than positive ones — they prevent architectural erosion.

Probe deeper with AskUserQuestion:
- **"If you replaced [A], what would [B] need to know?"** — Options: Nothing (fully decoupled) / Just the protocol / Some internal types / It would break
- **"How stable is this seam?"** — Options: Load-bearing (won't change) / Evolving / Likely to change
- **"What should never cross this seam?"** — Options: Internal state / Implementation types / Threading concerns / Let me specify

### 5. Layers

**Discover first:** Use Task (Explore) to analyze import graphs — which modules import which, and in what direction.

**Use AskUserQuestion (assertion-based):**
```
"I found this dependency direction:
  - [layer A] → [layer B] → [layer C]

I'm assuming [layer A] is the outermost (presentation) layer because [reasoning].

Is this layering correct?"
  Options: Yes / The direction should be different / There are more layers / No layers, it's flat / The assumption is wrong
```

If layered:
- **"What is forbidden?"** — Options: Skip-layer dependencies / Upward dependencies / Circular dependencies / All of the above

### 6. State Distribution

**Discover first:** Use Grep to find state stores, observable objects, published properties, shared state patterns.

**Use AskUserQuestion (assertion-based):**
```
"I found these state patterns:
  - [observable objects, state stores, etc.]

I believe the source of truth is:
  - [state X]: [local/remote/derived] because [reasoning]
  - [state Y]: [local/remote/derived] because [reasoning]

Is this correct?"
  Options: Yes / Some sources of truth are wrong / Let me explain per state
```

For state propagation:
- **"How does state flow to consumers?"** — Options: Observation/binding / Push notifications / Pull on demand / Event bus

### 7. Composition Rules

**Discover first:** Use Grep to find initialization patterns, factory methods, dependency injection containers, composition roots.

**Use AskUserQuestion (assertion-based):**
```
"I found this composition pattern:
  - [discovered DI/factory/init patterns]

I'm assuming this is the intended assembly strategy because [reasoning].

Is this correct?"
  Options: Yes / We should use DI / We should use factories / The assumption is wrong / Let me explain
```

- **"What can be swapped?"** — Options: Everything behind a protocol / Specific components only / Nothing is swappable / Let me list them

### 8. Threading Model

**Discover first:** Use Grep to find @MainActor, DispatchQueue, Task {}, async/await, actor declarations, thread annotations.

**Use AskUserQuestion (assertion-based):**
```
"I found these threading patterns:
  - [main thread annotations, async patterns, actors]

I believe the threading model is [your conclusion] because [reasoning].
I'm assuming [stated assumption about isolation boundaries].

Is this correct?"
  Options: Yes / It needs changes / The assumption is wrong / Let me describe the model
```

- **"What must run on the main thread?"** — Options: UI updates only / UI + state observation / Everything except network / Let me specify
- **"What concurrency rules apply?"** — Options: Strict actor isolation / Sendable everywhere / Pragmatic (some shared mutable state) / Let me explain

## Cross-References

Architecture specs don't exist in isolation. These are the key intersection points with Experience and Engineering:

| When you specify... | Coordinate with Experience on... | Coordinate with Engineering on... |
|--------------------|---------------------------------|----------------------------------|
| Bounded contexts and decomposition | Which product capabilities each context supports; does the decomposition align with how users think about the product? | Which protocols implement communication across context boundaries |
| Seams and crossing points | Whether seam decisions constrain Experience behaviors (e.g., does a seam prevent real-time updates the Experience requires?) | Which wire formats, APIs, and protocols implement each crossing point |
| State distribution | Which states are user-observable; does the state flow support the Experience's feedback requirements? | How state is persisted, propagated, and synchronized in implementation |
| Threading model | Whether threading decisions affect perceived responsiveness or create observable delays | Actor isolation, dispatch queues, async/await implementation |
| Composition rules | Whether swappability requirements align with Experience's platform adaptation needs | Dependency injection containers, factory implementations, composition root code |
| Layer boundaries | Whether layer structure supports the Experience's lifecycle and system event requirements | Import rules, module boundaries, build target organization |

Flag these intersections explicitly when you encounter them. Don't assume the Experience or Engineering specifier will discover them independently.

## Classification Guard

Watch for concerns that belong in sibling spec types:

| User says... | Guide them to... |
|-------------|-----------------|
| "The user taps to select a player" | That's Experience — here we care about how selection state propagates |
| "Users can create collaborative playlists" | That's Experience (product capability) — here we care about which bounded context owns playlists and how collaboration crosses context boundaries |
| "We use WebSocket for real-time updates" | That's Engineering (protocol detail) — here we care about the seam between real-time and polling |
| "A vertical slider with haptic feedback" | That's Engineering (UI implementation) — here we care about how volume state flows between components |

Say: *"Good — let's capture the structural decision here and the implementation detail in the Engineering spec."*

### Ambiguous Cases

When you're unsure whether something is Architecture or belongs elsewhere, use this decision framework:

1. **"Does this change which parts exist or how they communicate?"** → Architecture. *"Recommendations should be independent of playback"* is a boundary decision — Architecture.
2. **"Does this change only how one part works internally?"** → Engineering. *"Use a B-tree index for playlist lookup"* is internal to one component — Engineering.
3. **"Does this describe what the product can do or how users experience it?"** → Experience. *"Users can get personalized recommendations"* is a capability — Experience. How the recommendation system is decomposed is Architecture.
4. **"Does this span the boundary?"** → Split it. The structural decision goes in Architecture, the capability it enables goes in Experience, and the internal implementation goes in Engineering. Example: *"The system should handle 10,000 concurrent listeners"* — the scalability requirement is Experience, the decomposition into stateless services is Architecture, and the load balancer configuration is Engineering.

## Phase 3: Draft Output

After exploration, produce a draft Architecture spec:

1. Document bounded contexts — what subdomains exist, what each owns
2. Map context relationships — shared kernels, anti-corruption layers, customer-supplier
3. Document aggregates and consistency boundaries within each context
4. Document component boundaries and their responsibilities
5. Map every seam — what crosses it, what doesn't, and what must NOT cross it
6. State the dependency direction and composition rules
7. Document threading model and state distribution
7b. Number structural rules as AS-NNN (e.g., AS-001: "PlayerManager is the single source of truth for player state"). These are testable architectural assertions, not prose.
8. Place in `Documents/Specifications/{domain}/v0.1.0/architecture.md` (each feature domain gets its own subdirectory)
9. Flag any cross-cutting concerns that need Experience or Engineering specs
10. Mark unresolved gaps with `[NEEDS CLARIFICATION: ...]` — these are assumptions that surfaced during exploration but were not confirmed. They must be resolved before the spec advances beyond v0.1.0.

**Use AskUserQuestion before writing:**
```
"Here's the draft Architecture spec covering:
  - Bounded Contexts: [list]
  - Context Map: [relationships]
  - Aggregates: [list]
  - Components: [list]
  - Seams: [list]
  - Layers: [description]
  - State: [distribution model]

Ready to write this draft?"
  Options: Write it / Let me review the content first / Make changes
```
