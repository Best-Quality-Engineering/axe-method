# Specify

Interactive guide for the full Specify phase. Leverages sub-agents and agent teams to explore all three spec types, then produces draft specifications.

## When to Use

- Starting a new project or feature from scratch
- First cycle of the AXE method — getting initial intent stated as specs
- User says `/axe-method:specify`

## Brownfield Projects — Code Exists Without Specs

If code already exists without specifications, the first Specify cycle uses the specifier agents in **extraction mode** rather than exploration mode. Each specifier reads the existing code and reverse-engineers what the specs *should* say — then validates with the user.

### Why Specifiers, Not the Inspector

The inspector audits compliance — it checks code against existing specs. When no specs exist, it has nothing to audit against. Extracting specifications from code is a specifier function: reading implementation and deriving the behavioral, structural, and engineering intent behind it.

### The Extraction Process

1. **Establish the domain** (same as greenfield — Step 1 below). Even with existing code, you need the user's one-sentence product description, user identification, and domain terms.

2. **Run specifier agents in extraction mode.** Each specifier reads the codebase and extracts specs in its domain:
   - **Experience specifier** — Scans views, screens, navigation, user actions, lifecycle handlers. Extracts product capabilities and behaviors. Presents to user: "The code does X — is this what you want?"
   - **Architecture specifier** — Scans modules, imports, protocols, dependency patterns. Extracts bounded contexts, seams, layers. Presents to user: "The code is structured like X — is this intentional?"
   - **Engineering specifier** — Scans network clients, APIs, UI components, data models, toolchain. Extracts protocols, wire formats, UI mechanics. Presents to user: "The code implements X — should this be specified?"

3. **User validates extractions.** For each extracted behavior/structure/implementation, the user confirms: intentional (becomes a spec), accidental (flagged for removal), or wrong (flagged for correction in the next Construction cycle).

4. **Draft specs from validated extractions.** Proceed to the Cross-Cutting Review (Step 3 below) and then Produce Draft Specs (Step 4 below) as normal.

This is the normal entry point for brownfield codebases. The result is a set of specs that describe the intended product — which may differ from the code as it exists today. Those differences become the work for the first Construction cycle.

## Available Agents

The AXE method provides four sub-agents (bundled in the plugin's `agents/` directory):

| Agent | Role |
|-------|------|
| `axe-method:experience-specifier` | Explores and drafts Experience specs — product capabilities, features, novel solutions, behaviors, flows, product lifecycle, system events, accessibility, localization, design language, platform HIG |
| `axe-method:architecture-specifier` | Explores and drafts Architecture specs — structure, bounded contexts, seams, composition, system decomposition |
| `axe-method:engineering-specifier` | Explores and drafts Engineering specs (internals + UI) |
| `axe-method:inspector` | Audits code against specs (Inspection phase) |

Each preloads the `axe-method:axe-method` skill and uses project-level persistent memory to accumulate domain knowledge across sessions.

## Re-entry from Construction — Closing Spec Gaps

When the constructor routes back to Specify because specs are too incomplete to build from, this becomes a **targeted refinement cycle** rather than a full exploration. The constructor's gap report tells you exactly what needs attention.

### Step 1: Triage the Gap Report

Read the constructor's gap report. It classifies each gap as Blocking, Underspecified, or Missing, and identifies which spec type (Experience, Architecture, or Engineering) needs work.

**Use AskUserQuestion to prioritize:**
```
"The constructor reported these spec gaps:

  Blocking (can't build at all):
  - [gap 1: spec type + what's missing]
  - [gap 2: spec type + what's missing]

  Underspecified (built with assumptions):
  - [gap 3: assumption made]

Which gaps should we close first?"
  Options: All blocking gaps / Most impactful gap first / Let me choose / Review the full report
```

### Step 2: Route to the Right Specifier

Delegate each gap to the specifier that owns that domain:

- **Experience gaps** → `axe-method:experience-specifier` with the specific behaviors that need clarification
- **Architecture gaps** → `axe-method:architecture-specifier` with the structural decisions that need resolution
- **Engineering gaps** → `axe-method:engineering-specifier` with the implementation details that need specification

Provide each specifier with the constructor's gap entry: the quote (or absence), what's needed, and the impact. The specifier doesn't re-explore the full domain — it focuses on closing the gap.

### Step 3: Validate and Return to Construction

After the specifier agents have updated their specs:

1. **Verify the gaps are closed** — Re-read the updated specs against the constructor's gap list
2. **Check for new cross-cutting concerns** — Did closing one gap create a need in another spec type?
3. **Return to Construction** — The constructor re-reads the updated specs and resumes where it left off

---

## First-Time Specify — The Process

### Step 1: Establish the Domain and Seed the Vocabulary

Before delegating to spec agents, establish the shared foundation in the main conversation. **The Specify orchestrator is the vocabulary steward** — responsible for eliciting domain knowledge, establishing authoritative terms, and maintaining the Product Vocabulary across every cycle.

#### Elicit Domain Knowledge

1. **"What does this product do, in one sentence?"**
2. **"Who are the users?"**
3. **"What are the key domain terms?"** — Use Task (Explore) to scan the codebase for class names, model names, and variable names. Present discovered terms via AskUserQuestion and ask which are part of the ubiquitous language.

#### Establish the Product Vocabulary

The vocabulary is not a byproduct of specification — it is a prerequisite. Specifier agents must have agreed-upon terms *before* they start writing specs. Otherwise three agents independently name the same concept three different ways.

1. **Check for existing vocabulary.** Read `Documents/Vocabulary/` — if it exists from a prior cycle, present it to the user for confirmation or update.
2. **Build or update the glossary.** For each domain term the user confirms:
   - Record the **authoritative name** (what the domain calls it)
   - Record a **one-line definition** (what it means in this domain)
   - Note any **aliases to reject** (names that exist in code but aren't the authoritative term)
3. **Write the vocabulary before delegating to specifiers.** Create or update `Documents/Vocabulary/v{SemVer}/index.md`. This is the anchor document all three specifiers will read before they begin.

**Use AskUserQuestion to confirm the vocabulary:**
```
"Here is the Product Vocabulary for {Domain}:

  - [Term 1]: [definition]
  - [Term 2]: [definition]
  - [Term 3]: [definition]

  Rejected aliases: [code name] → use [authoritative name] instead

Ready to proceed with this vocabulary?"
  Options: Approve / Add terms / Change definitions / Let me review
```

Specifier agents do not proceed until the vocabulary is written.

#### Vocabulary Stewardship Across Cycles

The orchestrator's vocabulary responsibility doesn't end at the first cycle:

- **After specifier exploration:** Check if specifiers introduced new terms during their work. If so, add them to the vocabulary and confirm with the user.
- **After Construction re-entry:** Check if the constructor's gap report reveals naming confusion. Resolve it in the vocabulary before routing to specifiers.
- **After Inspection:** Check if the inspector flagged terminology inconsistencies between specs, code, and tests. Update the vocabulary, then cascade the correction to the relevant specifier.

When a naming conflict arises between specifiers, the orchestrator resolves it — not the specifiers themselves. The vocabulary is the single source of truth; the orchestrator maintains it.

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

1. **Vocabulary reconciliation** — Grep all draft specs for domain terms. Compare against the Product Vocabulary. Flag any mismatches: same concept with different names across specs, new terms introduced by specifiers that aren't in the vocabulary, or vocabulary terms the specs didn't use. Resolve conflicts, update the vocabulary, and cascade corrections to the affected specs. The orchestrator makes the naming decision — specifiers don't negotiate terms with each other.
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
