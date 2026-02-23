# The AXE Method

**Product-Driven Development — Start with what you want.**

The AXE Method is a spec-driven loop. You don't start by writing code — you start by writing *instructions to the agent* that describe what you want. Then you iterate until those instructions reliably produce the right product.

## The Core Insight

**A specification is an instruction that elicits the desired product outcome from an agent.**

Specs aren't documentation. They aren't reference material you write after the fact. They're the input. If you hand them to an agent and it builds the wrong thing, the specs were wrong — not the agent. That reframe is the entire method.

## The Cycle: Specify → Construct → Inspect

### 1. Specify

State what you want across three peer spec types:

- **Experience** — Why someone would use this product and what it makes possible — capabilities, features, novel solutions — and how those are experienced (behaviors, flows, feedback, lifecycle, accessibility, localization)
- **Architecture** — How the system is structured and how its parts connect — bounded contexts, aggregates, seams, composition, state flow, dependency policy
- **Engineering** — How things work inside (protocols, APIs, data structures, UI controls, toolchain)

These aren't layers — they're **aspects**. Three views of the same thing at any scale, from a single function up to an entire system. Domain language (DDD's ubiquitous language) sits at the center, keeping all three coherent.

```
      Experience
       /       \
      /  Domain  \
     /  (shared   \
    /   language)   \
   /                 \
Architecture ——— Engineering
```

### 2. Construct

The agent builds from the specs using TDD:

- Derive tests directly from spec behaviors (Red)
- Implement code to make them pass (Green)
- If a spec is too vague to produce a test, that's a signal — it needs refinement

### 3. Inspect

Audit the result across six directions between Spec, Code, and Test:

```
        Spec
       ↗↙  ↖↘
     Code ←→ Test
```

- Does every spec behavior have code? Tests? Tests *at every applicable level*?
- Does any code exist that no spec describes?
- Are there tests asserting things no spec mentions?

Gaps found here feed the next Specify phase.

## What Makes The AXE Method Different

### Specs are refined, not code

When the output is wrong, the instinct in most methods is to fix the code. In The AXE Method, the instinct is to fix the instruction. Each cycle brings the specs closer to eliciting what you actually want.

### Experience Defines The Value

The Experience spec answers the question no other spec can: *"Why would someone use this?"* Features, capabilities, novel solutions — the things that make someone choose this product. Architecture structures the system to deliver that value. Engineering builds the internals. But Experience is where value is defined.

### The triangle is fractal

Experience, Architecture, and Engineering aren't just system-level concerns. They repeat at every scale — function, class, module, component, system. A function's parameter list is an architectural decision. Its caller's reason to use it is an experience concern. Its implementation is engineering.

| Scale | Experience (value + capability) | Architecture (structure + seams) | Engineering (construction) |
|-------|-----------|---------------------|---------------------------|
| **Function** | What capability it provides, why a caller would use it | What it owns, what crosses its boundary | How it's implemented inside |
| **Class** | What problem it solves, what value collaborators get | Domain concept, public contract vs. internal state | Internal mechanics |
| **Module** | What capability it offers importers, why it exists | Bounded context, what's exported, what's hidden | How exports are fulfilled |
| **Component** | What features users get, what novel value it delivers | Subdomain ownership, how it connects to siblings | Controls, layouts, protocols |
| **System** | Why someone would use this product, what it makes possible | Context map, bounded contexts, data flow | Infrastructure, deployment |

### Construction is construction, not design

The construction agent is a general contractor — it reads specs, sequences work, and delivers. It doesn't make design decisions. That separation keeps the feedback loop clean: if the product is wrong, you know to look at the specs.

### Tests are the enforcement mechanism

A spec behavior without a test is aspirational. A test without a spec is a hidden contract. The method demands zero gaps in all six directions.

## The Building Metaphor

| AXE Role | Building Equivalent |
|----------|-------------------|
| Specifier agents | Architect + Engineers (design team) |
| Construction agent | General Contractor |
| Inspector agent | Building Inspector |

The contractor doesn't redesign the building on site. The inspector doesn't fix the plumbing. Roles are separated so feedback flows cleanly.

## In Practice

First cycle: state what you know at whatever resolution you have — broad strokes are fine. The point is to get intent *stated* so it becomes something buildable, testable, and refinable. Each subsequent cycle sharpens the specs based on what inspection reveals. You converge on the product you want by iterating on the instructions, not by debugging code.

## Installation

The AXE Method is a [Claude Code](https://docs.anthropic.com/en/docs/claude-code) plugin. Install it from the marketplace:

```
/plugin marketplace add Best-Quality-Engineering/axe-method
/plugin install axe-method
```

Or test locally during development:

```
claude --plugin-dir ./path-to-axe-method
```

## Sub-commands

| Command | Purpose |
|---------|---------|
| `/axe-method:specify` | Orchestrate the Specify phase |
| `/axe-method:experience` | Experience specs |
| `/axe-method:architecture` | Architecture specs |
| `/axe-method:engineering` | Engineering specs |
| `/axe-method:construct` | Build from specs |
| `/axe-method:inspect` | Audit spec-code-test alignment |
| `/axe-method` | Interactive AXE method guidance |

## Sub-agents

Five sub-agents handle the work in each phase:

| Agent | Phase | Role |
|-------|-------|------|
| `axe-method:experience-specifier` | Specify | Explores and drafts Experience specs |
| `axe-method:architecture-specifier` | Specify | Explores and drafts Architecture specs |
| `axe-method:engineering-specifier` | Specify | Explores and drafts Engineering specs |
| `axe-method:constructor` | Construct | Reads specs, derives tests (Red), implements code (Green) |
| `axe-method:inspector` | Inspect | Six-direction audit across Spec, Code, and Test |

## Built On

The AXE Method is built on and employs principles from two foundational disciplines:

- **Test-Driven Development** — drives the Construction phase through the Red/Green/Refactor cycle. Tests are derived from specs, not from code.
- **Domain-Driven Design** — permeates all phases as a cross-cutting concern. The ubiquitous language, captured in the Product Vocabulary, keeps specs, code, and conversation coherent.
