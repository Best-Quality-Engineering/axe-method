# Specify

Interactive guide for the full Specify phase. Leverages sub-agents and agent teams to explore all three spec types, then produces draft specifications.

## When to Use

- Starting a new project or feature from scratch
- First cycle of the AXE method — getting initial intent stated as specs
- User says `/axe-method:specify`

## Available Agents

The AXE method provides four sub-agents (bundled in the plugin's `agents/` directory):

| Agent | Role |
|-------|------|
| `axe-method:experience-specifier` | Explores and drafts Experience specs — product capabilities, features, novel solutions, behaviors, flows, product lifecycle, system events, accessibility, localization, design language, platform HIG |
| `axe-method:architecture-specifier` | Explores and drafts Architecture specs — structure, bounded contexts, seams, composition, system decomposition |
| `axe-method:engineering-specifier` | Explores and drafts Engineering specs (internals + UI) |
| `axe-method:inspector` | Audits code against specs (Inspection phase) |

Each preloads the `axe-method:axe-method` skill and uses project-level persistent memory to accumulate domain knowledge across sessions.

## The Process

### Step 1: Establish the Domain

Before delegating to spec agents, establish the shared foundation in the main conversation:

1. **"What does this product do, in one sentence?"**
2. **"Who are the users?"**
3. **"What are the key domain terms?"** — Use Task (Explore) to scan the codebase for class names, model names, and variable names. Present discovered terms via AskUserQuestion and ask which are part of the ubiquitous language.

Build a glossary. This glossary must be consistent across all three spec types.

### Step 2: Specify — Sequential or Parallel

Choose the approach based on project complexity:

#### Sequential (single agent at a time)

Delegate to each specifier agent in order. Each builds on the previous:

1. **Experience first** — Use the `axe-method:experience-specifier` agent to define the product's value — capabilities, features, novel solutions — and then explore how those are experienced: behaviors, lifecycle, system events, accessibility, localization, design language, and platform HIG. Experience comes first because it defines the value that the other specs exist to deliver.
2. **Architecture second** — Use the `axe-method:architecture-specifier` agent with the Experience draft in hand. It asks: "How do we structure the system to deliver this value?" — bounded contexts, aggregates, seams, composition.
3. **Engineering third** — Use the `axe-method:engineering-specifier` agent with both drafts in hand. It asks: "How do we implement each component?"

#### Parallel (agent team)

For larger domains, spawn an agent team with three teammates working simultaneously:

```
Create an agent team to specify the {Domain} domain:
- Experience teammate (experience-specifier): define product value, capabilities, features, then explore behaviors, flows, states, lifecycle, system events, accessibility, localization, design language, platform HIG
- Architecture teammate (architecture-specifier): discover bounded contexts, aggregates, boundaries, seams, composition, state distribution
- Engineering teammate (engineering-specifier): map protocols, APIs, UI implementation

Have them communicate cross-cutting concerns to each other.
When one teammate identifies something that belongs in another spec type,
they should message that teammate directly.
```

The agent team approach is faster but uses more tokens. It works best when the domain is large enough that the three spec types can be explored independently.

### Step 3: Cross-Cutting Review

After all three spec types are explored (sequentially or in parallel):

1. **Ubiquitous language consistency** — Grep all draft specs for domain terms. Flag any mismatches (same concept, different names across specs).
2. **Spec triangle alignment** — Do Experience behaviors have Architecture coverage? Does Architecture have Engineering implementation?
3. **Cross-cutting gaps** — Use AskUserQuestion to present any flagged concerns: "These were flagged during exploration but not addressed — add them now?"

### Step 4: Produce Draft Specs

Set up the file organization and write draft specs:

```
Documents/
├── References/
└── Specifications/
    ├── Experience/{Domain}/v0.1.0/
    ├── Architecture/{Domain}/v0.1.0/
    └── Engineering/{Domain}/v0.1.0/
```

Use AskUserQuestion before writing — present the proposed structure and content summary. Write files only after user approval.

## After Specify

With draft specs written, the next step is **Construction** — deriving tests from the specs and implementing code to pass them.

For the **Inspection** phase, use the `axe-method:inspector` agent or spawn an inspection team covering all six gap directions:

```
Create an agent team to audit the {Domain} domain:

Spec↔Code pair:
- Spec->Code teammate: verify every spec requirement has matching code
- Code->Spec teammate: verify every code behavior is described in spec

Spec↔Test pair:
- Spec->Test teammate: verify every spec behavior has a test
- Test->Spec teammate: verify every test assertion is described in a spec

Code↔Test pair:
- Code->Test teammate: verify every code behavior has test coverage
- Test->Code teammate: verify every test assertion has matching code
```

Specs don't need to be perfect. They need to be stated. The cycle will refine them.
