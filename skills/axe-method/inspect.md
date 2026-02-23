# Inspect

The Inspection phase evaluates the product against intent, audits all six directions between Spec, Code, and Test, writes a structured report, and produces findings that feed the next Specify iteration.

## When to Use

- Construction is complete (or at a checkpoint)
- User says `/axe-method:inspect`
- Time to evaluate what was built against what was intended

## Available Agents

- **axe-method:inspector** — Six-direction auditor. Read-only on production code; writes structured inspection reports to `Documents/Inspections/`
- For comprehensive audits, spawn an inspection agent team (see below)

## The Process

### Step 1: Choose Inspection Scope

**Use AskUserQuestion:**
```
"What scope for this inspection?"
  Options:
  - Full audit (all specs, all code, all tests)
  - Single domain ({Domain})
  - Single spec type (Experience / Architecture / Engineering)
  - Targeted (specific behaviors or files)
```

### Step 2: Run the Audit

The audit covers all six directions between the three artifacts:

```
        Spec
       ↗↙  ↖↘
     Code ←→ Test
```

#### Single Agent (focused inspection)

Delegate to the `axe-method:inspector` agent:

```
Use the axe-method:inspector agent to audit {Domain} against its specifications.
Check all six gap directions:
  Spec↔Code: Spec->Code, Code->Spec
  Spec↔Test: Spec->Test, Test->Spec
  Code↔Test: Code->Test, Test->Code
```

#### Agent Team (comprehensive inspection)

For full audits, spawn an inspection team with parallel gap analysis, organized by artifact pair:

```
Create an agent team to audit the {Domain} domain:

Spec↔Code pair:
- Spec->Code teammate: for each spec requirement, verify matching code exists
- Code->Spec teammate: for each code behavior, verify it's described in a spec

Spec↔Test pair:
- Spec->Test teammate: for each spec behavior, verify a test asserts it
- Test->Spec teammate: for each test assertion, verify a spec describes the behavior

Code↔Test pair:
- Code->Test teammate: for each code behavior, verify a test covers it
- Test->Code teammate: for each test assertion, verify code implements the behavior

Each teammate should quote what they verify and cite file:line locations.
Communicate any conflicts or overlapping findings to each other.
```

### Step 3: Review Findings

The inspection produces a gap analysis across six directions:

| Gap Type | Risk | Resolution |
|----------|------|------------|
| **Spec → Code** | Feature is missing | Implement in next Construction |
| **Code → Spec** | Behavior is undocumented | Add to spec in next Specify |
| **Spec → Test** | Conformance unenforced | Add test in next Construction |
| **Test → Spec** | Hidden contract + spec coverage gap | Add behavior to spec or remove stale test |
| **Code → Test** | Untested behavior | Add test coverage in next Construction |
| **Test → Code** | Stale or broken test | Fix test or restore missing code |

**Use AskUserQuestion to present findings:**
```
"Inspection findings for {Domain}:

  Spec ↔ Code:
    Spec → Code gaps: [N]
    Code → Spec gaps: [N]

  Spec ↔ Test:
    Spec → Test gaps: [N]
    Test → Spec gaps: [N]

  Code ↔ Test:
    Code → Test gaps: [N]
    Test → Code gaps: [N]

  Total compliant: [N]

How to proceed?"
  Options:
  - Review all gaps in detail
  - Start next Specify iteration with these findings
  - Fix gaps now (quick fixes only)
  - Write report and review later
```

### Step 4: Write Report

Write the inspection report to `Documents/Inspections/{timestamp}/` using ISO 8601 compact format (e.g., `2026-02-21T143022`).

#### Summary File

Write `summary.md` containing:

1. **Timestamp** — When the inspection was run
2. **Scope** — What was inspected (full audit, single domain, targeted)
3. **Spec versions** — Which spec versions were audited against
4. **Gap totals** — Count per direction, grouped by artifact pair
5. **Overall status** — Compliant / Gaps found / Critical gaps
6. **Areas of concern** — List of area files in this inspection with one-line summaries

#### Area Files

Write one file per inspection area, domain, or area of concern. File names are descriptive based on what was inspected (e.g., `playback-behaviors.md`, `network-protocols.md`, `auth-flow.md`).

Each area file contains:

1. **Scope** — What this file covers
2. **Findings per gap direction** — For each gap found:
   - The gap direction (e.g., Spec → Code)
   - The spec requirement or code behavior being audited (quoted)
   - The source location (`file:line`)
   - The gap classification and risk
3. **Compliant items** — Confirmed matches with citations
4. **Recommendations** — Specific actions for the next cycle

**Use AskUserQuestion before writing:**
```
"Ready to write inspection report to Documents/Inspections/{timestamp}/:
  - summary.md
  - {area-1}.md: covers [description]
  - {area-2}.md: covers [description]

Write the report?"
  Options: Write it / Let me review findings first / Change structure
```

### Step 5: Evaluate the Experience

Beyond the mechanical audit, the Inspection phase asks qualitative questions:

**Use AskUserQuestion:**
```
"Beyond the gap analysis, how does the product feel?"
  Options:
  - It matches my intent — proceed to next cycle
  - The behavior is right but the specs need clarification
  - Something is wrong — the specs need to change
  - I need to see it / use it before deciding
```

These qualitative findings are the most important input to the next Specify phase. The specs were instructions — if the output wasn't what you wanted, the instructions need refinement.

### Step 6: Prepare Next Iteration

Compile all findings into input for the next Specify phase:

1. **Spec gaps** — Behaviors in code or tests that need to be added to specs (Code → Spec + Test → Spec)
2. **Implementation gaps** — Spec behaviors that need to be built (Spec → Code)
3. **Test gaps** — Behaviors that need test coverage (Spec → Test + Code → Test)
4. **Stale tests** — Tests asserting behaviors that no longer exist in code (Test → Code)
5. **Refinements** — Spec language that was too vague or misleading
6. **New requirements** — Things discovered during inspection that weren't in any spec

**Present the iteration summary:**
```
"Ready for next iteration of {Domain}:

  Specs to update: [list]
  Code to implement: [list]
  Tests to add: [list]
  Stale tests to fix: [list]
  New requirements: [list]

  Report written to: Documents/Inspections/{timestamp}/

Start next Specify phase?"
  Options: Start Specify / I need to think about this / Review the report first
```

## After Inspect

Inspection findings feed the next **Specify** phase. The cycle continues: **Specify → Construct → Inspect → Specify → ...**

The goal is zero gaps in all six directions. Each iteration brings the specs, code, and tests closer to alignment.
