---
name: axe-method
description: "AXE: Product-Driven Development — Start with what you want. Spec-driven method for agent-assisted software development. Use when implementing features against specs, creating or updating specs, auditing code against specifications, or when specs and code need to stay in sync."
---

# AXE

**Product-Driven Development — Start with what you want.**

**AXE** is an iterative, product-driven development method for agent-assisted software development. It is built on and employs principles and practices from two foundational disciplines:

- **Test-Driven Development** (`axe-method:tdd`) — drives the Construction phase through the Red/Green/Refactor cycle. Tests are derived from specs, not from code.
- **Domain-Driven Design** (`axe-method:ddd`) — permeates all phases as a cross-cutting concern. The ubiquitous language, captured in the Product Vocabulary, keeps specs, code, and conversation coherent.

Each cycle of **Specify → Construction → Inspection** refines the specifications until they reliably elicit the desired product outcome from the agent.

## Sub-agents

Five sub-agents are bundled in the plugin's `agents/` directory:

| Agent | Phase | Role |
|-------|-------|------|
| `axe-method:experience-specifier` | Specify | Explores and drafts Experience specs — product capabilities, features, novel solutions, behaviors, flows, product lifecycle, system events, accessibility, localization, design language, platform HIG |
| `axe-method:architecture-specifier` | Specify | Explores and drafts Architecture specs — structure, bounded contexts, seams, composition, system decomposition |
| `axe-method:engineering-specifier` | Specify | Explores and drafts Engineering specs (internals + UI + toolchain) |
| `axe-method:constructor` | Construct | Reads specs, sequences work, derives tests (Red), implements code (Green), delivers the finished product |
| `axe-method:inspector` | Inspect | Six-direction audit — read-only on production code, writes inspection reports |

Each preloads `axe-method:axe-method` and uses project-level persistent memory.

## Core Definition

**A specification is an instruction to the agent that elicits the desired product outcome.**

Specs are not documentation. They are not reference material. They are instructions — written with enough precision that an agent, following them, produces the correct product. If a spec cannot drive an agent to the desired outcome, it is not yet a spec.

## Philosophy

### The Specification Triangle Is Fractal

Most people treat "architecture" in software as the high-level stuff — system diagrams, service boundaries, the boxes-and-arrows view. This misses the point entirely.

Architecture is about **structure and seams**. Structure is how you decompose a system into parts — what exists, why it exists as a distinct thing, and what it owns. Seams are where those parts meet — every point where a decision about connection, separation, or communication has been made. Structure and seams exist at every level of granularity:

| Scale | Structure | Seams |
|-------|-----------|-------|
| **Function** | What it does, what responsibility it owns | Parameters, return types, what it delegates to vs. does itself |
| **Class** | What concept it represents, what state and behavior it encapsulates | Public interface vs. internal state, constructor dependencies |
| **Module** | What bounded context or subdomain it serves | Exports, imports, what crosses the boundary |
| **Component** | What capability it provides, what it owns exclusively | Protocols, APIs, data contracts between components |
| **System** | What bounded contexts exist, how they decompose the domain | Service boundaries, context maps, network protocols, deployment units |

The word "architecture" doesn't change meaning as you zoom in or out. A function's parameter list is an architectural decision — it defines what crosses the seam between caller and callee. A class's encapsulation of a domain concept is an architectural decision — it defines what the outside world can depend on. A module's alignment with a bounded context is an architectural decision — it determines where the ubiquitous language has authority. These are the same concern at different scales.

And if architecture is fractal, then so is engineering — for the same reason. At every level where structure and seams exist, there are construction techniques, tools, and practices for building what lives inside those structures. For any given architecture, there is a corresponding engineering: the way you actually build it.

| Scale | Architecture (structure + seams) | Engineering (construction) |
|-------|---------------------|---------------------------|
| **Function** | Responsibility ownership, parameter types, delegation boundaries | Algorithm choice, data transformation, error handling |
| **Class** | Domain concept, aggregate boundary, public interface, dependency contracts | Internal data structures, method implementations, state management |
| **Module** | Bounded context, exports, import boundaries | Wire formats, serialization, internal protocols |
| **Component** | Subdomain ownership, API contracts, event channels | Specific frameworks, libraries, rendering mechanics |
| **System** | Context map, service boundaries, network protocols | Infrastructure, deployment, platform-specific implementation |

And Experience completes the triangle at every level. At every scale where structure exists and construction happens, there is a reason the thing exists — a value it provides to its consumer. Whether that consumer is an end user, a calling function, or a dependent module, Experience answers "why would you use this?"

| Scale | Experience (value + capability) | Architecture (structure + seams) | Engineering (construction) |
|-------|---------------------|---------------------|---------------------------|
| **Function** | What capability it provides, why a caller would use it | What it owns, what crosses its boundary | How it's implemented inside |
| **Class** | What problem it solves, what value collaborators get | Domain concept, public contract vs. internal state | Internal mechanics |
| **Module** | What capability it offers importers, why it exists | Bounded context, what's exported, what's hidden | How exports are fulfilled |
| **Component** | What features users get, what novel value it delivers | Subdomain ownership, how it connects to siblings | Controls, layouts, protocols |
| **System** | Why someone would use this product, what it makes possible | Context map, bounded contexts, data flow | Infrastructure, deployment |

The three spec types are not layers — they are **aspects**. They describe different facets of the same thing at any scale. This is why AXE treats them as peers in a triangle rather than a hierarchy. If architecture were only "the high-level stuff," it would sit above engineering. But the triangle repeats at every level of granularity, which means there is no level where one aspect dominates the others.

### The Construction Metaphor

The AXE cycle maps directly to building construction — and the metaphor is not decorative. It clarifies roles and responsibilities:

| AXE | Building Construction | Responsibility |
|-------|----------------------|----------------|
| **Specifier agents** | Architect + Engineers (design team) | Produce specifications precise enough to build from |
| **Construction agent** | General Contractor | Reads specs, sequences work, delivers the finished product according to spec |
| **Inspector agent** | Building Inspector | Verifies construction matches specifications |

The construction agent doesn't design — it constructs. It reads the specifications, breaks the work into tasks, sequences them correctly, and delivers. When the output doesn't match the specs, the question is whether the specs were unclear (refine in next Specify) or the construction was wrong (fix in Construction).

In building construction, engineering specs don't just describe *what* to build — they specify *with what*. Materials, methods, tools: "14-gauge copper wire, run in EMT conduit" not just "install electrical." The software equivalent is the **toolchain** — build systems, test runners, package managers, linters, formatters, CI/CD pipelines. These are the materials and tools the construction agent needs to execute the specs. A construction agent that doesn't know the toolchain is a crew that shows up without tools.

### Experience Defines The Value

The Experience spec is where the product's reason to exist is stated. Features, capabilities, novel solutions — the things that make someone choose this product over alternatives or over doing nothing. Architecture structures the system to deliver that value. Engineering builds the internals that make it real. But Experience is where value is defined.

This means the Experience spec answers the question no other spec can: **"Why would someone use this?"** If the Experience spec doesn't make the product's value clear, the Architecture and Engineering specs are structuring and building something with no stated purpose. The triangle has three equal peers, but Experience is the one that justifies the other two.

### Specs Are Instructions, Not Documentation

A specification is not a reference document. It is not written for humans to read after the fact. It is an instruction — written with enough precision that an agent, following it, produces the correct product.

This distinction matters because it changes how you evaluate a spec. The question is not "Is this accurate?" but **"If I handed this to an agent with no other context, would it produce what I want?"** If the answer is no, the spec has gaps — regardless of how accurate or well-written it appears.

When the output isn't what you wanted, the instinct is to blame the construction. In AXE, the instinct is to refine the instruction. The specs were what you asked for; if the result is wrong, you asked wrong. This is not a failure — it's the method working. Each cycle of Specify → Construct → Inspect brings the instructions closer to eliciting the desired outcome.

## The Specification Triangle

Specs are organized into three types that form a triangle — not layers. There is no hierarchy; each is a peer that informs the other two:

```
      Experience
       /       \
      /  Domain  \
     /  (shared   \
    /   language)   \
   /                 \
Architecture ——— Engineering
```

| Spec | Instructs the agent on... |
|------|--------------------------|
| **Experience** | Why the product exists and what it makes possible — the capabilities, features, and novel solutions that give someone a reason to use it. Also: the behaviors, flows, feedback, states, transitions, product lifecycle, system events, accessibility requirements, localization requirements, design language principles, and platform HIG compliance that shape how those capabilities are experienced. The Experience spec describes intent, value, and observable outcomes — not visual implementation. "Users can discover and join listening sessions with friends" is Experience. "The product recommends content based on listening history" is Experience. "User can adjust volume smoothly with haptic feedback at meaningful thresholds" is Experience. "Data syncs in the background when connectivity is available" is Experience. |
| **Architecture** | How the system is structured and how its parts connect — bounded contexts, aggregates, system decomposition, boundaries, layers, seams, composition rules, context maps, state distribution, threading model, dependency policy. The architecture spec defines what the parts are, why each exists as a distinct thing, where the seams between them fall, what crosses those seams, and what doesn't. Structure determines what the system is made of; seams determine what can change independently. |
| **Engineering** | How individual components work internally — protocols, APIs, packet formats, endpoints, algorithms, data structures, polling strategies, and UI. The engineering spec describes the internals and the rendering: wire formats, request/response schemas, lifecycle management, and the specific controls, layouts, and visual mechanics that implement the Experience. "Vertical slider, 44pt touch target, speaker.wave.3 SF Symbol" is Engineering. "WCAG AA contrast ratio of 4.5:1" is Engineering. |

Each spec type informs the other two:

- **Experience** informs Architecture (what needs to be structured) and Engineering (what needs to be built)
- **Architecture** informs Experience (what's feasible to structure) and Engineering (how to implement it)
- **Engineering** informs Experience (what the technology supports) and Architecture (what constraints exist)

### Domain as Cross-Cutting Concern

The domain sits at the center of the triangle. Following Domain-Driven Design principles, the ubiquitous language — the shared vocabulary of the domain — must be consistent across all three spec types. If the Experience spec calls something a "playlist" and the Engineering spec calls it a "queue," the agent receives conflicting instructions and produces an incoherent product.

Domain modeling (entities, value objects, aggregates, bounded contexts) is not owned by any single spec type. It permeates all three — the domain is the connective tissue that keeps the specs coherent. However, structural domain decisions — which bounded contexts exist, how they relate, where aggregate boundaries fall — are primarily Architecture concerns. The Experience spec names the capabilities those domain structures support. The Engineering spec implements the internals.

### Product Vocabulary

The ubiquitous language must be captured as a concrete artifact — the **Product Vocabulary**. This is a versioned glossary of authoritative domain terms, stored in `Documents/Vocabulary/v{SemVer}/index.md`. It is not documentation. It is the single source of truth for naming decisions across all specs, all phases, and all code.

**Why this artifact exists:** Domain terminology drifts at phase transitions. An architecture spec establishes clear domain names, but when engineering work begins, the agent encounters existing code with different names and starts reasoning from code terminology instead of spec terminology. The vocabulary prevents this by providing an anchoring document that is re-read at every phase boundary.

**Rules:**
- When a domain concept is named during any spec phase, it is recorded in the vocabulary immediately
- When a naming collision or ambiguity arises, check the vocabulary first — it may already be resolved
- Specs reference vocabulary terms; code implements them
- The vocabulary is updated when domain understanding evolves, not when code happens to use a different word

## The Cycle

### Phase Transition Rule

**Before starting any phase, re-read the Product Vocabulary and the prior phase's specs.** Phase transitions are where domain language drifts. The architecture spec establishes terms; the engineering phase encounters existing code with different names; the agent starts reasoning from code instead of spec. Anchoring on the vocabulary at every boundary prevents this.

This is not optional. Skipping the anchor step produces naming inconsistencies, phantom collisions, and wasted cycles resolving terminology that was already decided.

### Phase 1: Specify

State what you know as specifications across all three types, at whatever resolution you have. Broad or specific — the goal is to get intent *stated* so it becomes something that can be built against, tested, and refined.

On the first cycle, this is your initial definition. On subsequent cycles, this incorporates findings from Inspection.

When new domain terms are introduced during specification, record them in the Product Vocabulary immediately.

### Phase 2: Construction

The agent builds from the specifications following TDD discipline:

1. Derive exhaustive test suites from the specs — every stated behavior becomes a test assertion across all applicable test types (unit, UI, integration, e2e, etc.)
2. Run the tests. They fail. (Red)
3. Implement code to make the tests pass. (Green)

If a spec is too vague to produce a test, that is a signal — the spec needs refinement in the next cycle.

### Phase 3: Inspection

Evaluate the product against your intent:

- **How does it look?** — Examine the actual product the agent built.
- **How does it behave?** — Exercise it against your expectations.
- **Where did it not do what we asked?** — Identify gaps between intent and outcome.
- **How could we ask differently?** — Determine how to refine the specs to close those gaps.

Inspection findings feed the next Specify phase. The specs were instructions; if the output wasn't what you wanted, the instructions need refinement — not the agent.

## Behavioral Specifications

Specs describe **behaviors**, not implementation details. A spec should be precise enough that:

1. **Tests can be generated directly from the spec.** Every stated behavior maps to one or more test assertions. If a spec section can't produce a test, it's either too vague or describing implementation rather than behavior.

2. **The spec is 100% capable of causing you to generate the same code.** If you handed the spec to a competent developer with no access to the existing codebase, they would produce functionally identical code. If they wouldn't, the spec has gaps.

3. **Tests guarantee the implementation conforms to stated spec.** The test suite is the enforcement mechanism. Specs without corresponding tests are aspirational, not enforced. Code without corresponding specs is undocumented behavior that may silently break.

## Test Completeness

**A behavior is not tested until it has coverage at every level where it is observable.**

Test types form a hierarchy from most user-facing to most isolated. **User-facing tests are the highest priority** — they verify what actually matters: the experience the user has. Lower levels support and reinforce, but never substitute.

| Priority | Level | Catches | Example |
|----------|-------|---------|---------|
| **Highest** | **UI / E2E** | Wiring, navigation, actual user experience | Tapping a tab shows the correct screen |
| High | **Snapshot** | Visual regressions, layout, rendering | Empty state shows the right icon and text |
| Foundation | **Unit / Integration** | Logic, contracts, state transitions | ViewModel calls correct API endpoint |

The natural temptation is to write unit tests first because they're fast, easy, and satisfying. This is backwards. A unit test that verifies a ViewModel method does not prove the button that calls it is wired up. A snapshot that verifies rendering does not prove the view responds to user interaction. Only a UI test proves the user gets the right experience. Unit and snapshot tests are supporting infrastructure — important, but not the point.

Coverage at one level does not satisfy coverage at another. When forced to choose (time pressure, infrastructure gaps), **always prioritize the higher level**. A behavior with a UI test but no unit test is more valuable than a behavior with a unit test but no UI test.

**The completeness rule:** For each spec behavior, determine ALL applicable test levels and write tests at each. A behavior with only unit tests when UI tests also apply is incomplete — the same as code without tests.

**Test-first is mandatory.** Tests are derived from specs before production code is written. This is not a suggestion — it is the mechanism by which specs drive construction. Writing production code before tests inverts the dependency: the test becomes a verification of code rather than an instruction that drives code. This produces tests that verify what was built rather than what was specified.

## Gap Analysis

The Inspection phase audits all six directions between the three artifacts — Spec, Code, and Test:

```
        Spec
       ↗↙  ↖↘
     Code ←→ Test
```

| Gap Type | Description | Risk | Resolution |
|----------|-------------|------|------------|
| **Spec → Code** | Spec states a behavior, code doesn't implement it | Feature is missing | Implement the missing code |
| **Code → Spec** | Code implements a behavior, spec doesn't describe it | Undocumented behavior — untestable from spec | Extract the key behavior and add it to the spec |
| **Spec → Test** | Spec states a behavior, no test asserts it | Conformance is unenforced — regressions will be silent | Derive the test from the spec, not from the code |
| **Spec → Test (level)** | Spec behavior has tests at some levels but not all applicable levels | Partial coverage — specific bug classes go undetected (e.g., wiring bugs when only unit-tested) | Add tests at the missing levels per the Test Completeness principle |
| **Test → Spec** | Test asserts a behavior, no spec describes it | Hidden contract constraining future changes + spec coverage gap | Add the behavior to the spec, or remove the stale test |
| **Code → Test** | Code implements a behavior, no test covers it | Untested behavior — regressions will be silent | Add test coverage for the behavior |
| **Test → Code** | Test asserts a behavior, code doesn't implement it | Stale or broken test — false signal in the test suite | Fix the test to match current behavior, or restore the missing code |

The goal is zero gaps in all six directions. Updating specs to match code is NOT a substitute for implementing code to match specs. Every direction matters equally.

## Extracting Key Behaviors

When auditing code that has no spec coverage, extract behaviors at the right level of abstraction:

| Too specific (implementation detail) | Right level (behavior) |
|--------------------------------------|----------------------|
| "Uses a `Timer` with 1.0s interval" | "Client increments elapsed time locally every second while playing" |
| "Checks `name != nil && name != defaultName`" | "Display name is nil when using the default name" |
| "Spawns a Task that polls at 50ms intervals" | "Entity is not added to the list until initialization completes" |

The test for a correctly-specified behavior: **would the test fail if the behavior were reverted, but pass for any correct implementation?**

## What Belongs in Each Spec Type

| If you're describing... | It goes in... |
|------------------------|---------------|
| Why someone would use the product, what it makes possible | Experience spec |
| Product capabilities, features, novel solutions | Experience spec |
| User behaviors, flows, states, navigation, feedback | Experience spec |
| Transitions, affordances | Experience spec |
| Platform-specific experience adaptations | Experience spec |
| Accessibility requirements (what experience assistive technology users have) | Experience spec |
| Localization requirements (supported languages, cultural adaptations) | Experience spec |
| Design language principles (color philosophy, typography hierarchy, spacing system, motion philosophy) | Experience spec |
| Platform HIG compliance (which guidelines govern, system integration requirements) | Experience spec |
| Product lifecycle behaviors (launch, resume, first-run, upgrade) | Experience spec |
| Background operations and system event responses | Experience spec |
| Bounded contexts, subdomains, context maps | Architecture spec |
| Aggregates, consistency boundaries, entity ownership | Architecture spec |
| System decomposition, component structure | Architecture spec |
| State management, data flow | Architecture spec |
| Threading model, concurrency | Architecture spec |
| Dependency policy, layer boundaries, seams | Architecture spec |
| Composition rules, module composition | Architecture spec |
| UI layout, fonts, colors, icons, controls | Engineering spec |
| Protocol formats, packet structures | Engineering spec |
| API endpoints, request/response schemas | Engineering spec |
| Implementation algorithms, data structures | Engineering spec |
| Toolchain — build systems, test runners, linters, formatters | Engineering spec |
| CI/CD pipelines, deployment scripts | Engineering spec |
| Package managers, dependency versions, lock files | Engineering spec |

## Cross-Cutting Changes

Changes often span multiple spec types. When you add or modify a feature:

1. Check if the Experience spec needs updating (new capability, changed value proposition, new behavior, changed flow, lifecycle change, accessibility or localization impact)
2. Check if the Architecture spec needs updating (new bounded context, new aggregate, changed seam, new state flow, new component, composition change)
3. Check if the Engineering spec needs updating (new endpoint, changed data format, new UI implementation)

Missing any of these creates silent inconsistency between specs.

## File Organization

Specs and inspection reports live in a `Documents` directory at the project root.

```
Documents/
├── Inspections/
│   └── {ISO-8601-compact-timestamp}/
│       ├── summary.md
│       └── {area-of-concern}.md
├── Vocabulary/
│   └── v{SemVer}/
│       └── index.md
├── References/
└── Specifications/
    ├── Experience/
    │   └── {Domain}/
    │       └── v{SemVer}/
    ├── Architecture/
    │   └── {Domain}/
    │       └── v{SemVer}/
    └── Engineering/
        └── {Domain}/
            └── v{SemVer}/
```

### Domain Directories

Domain directories are named using the **ubiquitous language** — the shared vocabulary of the domain. If domain experts call it "Playback," the directory is `Playback`, not `playback-controller` or `audio-mgmt`.

### Version Directories

Each domain is versioned using **SemVer**:

| Increment | When |
|-----------|------|
| **Major** | Breaking change to the spec contract — behaviors removed or fundamentally altered |
| **Minor** | Additive change — new behaviors specified without breaking existing ones |
| **Patch** | Clarification — wording fixes, examples added, no behavioral change |

### Files Within a Version

A version directory contains **multiple files**, split by feature or capability. File naming is not prescribed — use whatever names make the content clear. The split should follow natural capability boundaries within the domain.

### Inspections Directory

Each inspection cycle produces a timestamped directory using **ISO 8601 compact** format (e.g., `2026-02-21T143022`). Timestamps sort chronologically and contain no special characters that conflict with file systems.

Each inspection directory contains:

- **`summary.md`** — Totals per gap direction, overall compliance status, scope, and timestamp. The executive view.
- **`{area-of-concern}.md`** — One file per inspection area, domain, or concern. Detailed findings with code citations (`file:line`), quoted spec requirements, and gap classifications. File names are descriptive based on what was inspected (e.g., `playback-behaviors.md`, `network-protocols.md`, `auth-flow.md`).

### Vocabulary Directory

The Product Vocabulary is a first-class versioned artifact — the ubiquitous language made concrete. It contains authoritative domain terms, naming decisions, and their definitions. It is versioned using the same SemVer rules as specs.

This is the anchoring document re-read at every phase transition. Terms defined here are binding across all specs and all code. See **Product Vocabulary** under Domain as Cross-Cutting Concern for rules.

### References Directory

The `References` directory holds material that informs specs but is not itself a spec:

- **Domain reference material** — research, external documentation, API references, vendor specs
- **Shared cross-spec content** — data models or other content referenced by multiple spec types

References are not instructions to the agent. They are source material that specs draw from.

## Inspection Rules

- **Read every line.** Do not skim. The audit's value comes from thoroughness.
- **Quote what you verify.** When checking a spec requirement, cite the code location that satisfies it.
- **No false positives.** Only mark compliant when you have confirmed the code matches. If uncertain, mark uncertain and note why.
- **No fixes during audit.** The report is read-only. Fixes happen after the user reviews.
