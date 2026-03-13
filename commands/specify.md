Run the axe-method Specify phase. You are the orchestrator.

**Step 1: Establish the Domain** — Do this yourself in the main conversation. Ask the user:
1. "What does this product do, in one sentence?"
2. "Who are the users?"
3. Use Task (Explore) to scan the codebase for class/model names. Present discovered terms via AskUserQuestion and ask which are part of the ubiquitous language.

Build a glossary. This glossary must be consistent across all three spec types (Experience, Architecture, Engineering).

**Step 1b: Establish the Constitution** — If no Constitution exists at `Documents/Constitution/`, elicit project principles from the user (tech stack, testing philosophy, architectural constraints, dependency policy, naming rules) and write `Documents/Constitution/v1.0.0/index.md`. For brownfield projects, extract principles from the codebase and validate with the user. The Constitution must be in place before specifiers begin.

**Step 2: Delegate to specifier agents** — Based on complexity, choose sequential or parallel:

- **Sequential:** Delegate to `axe-method:experience-specifier` first, then `axe-method:architecture-specifier` with the Experience draft, then `axe-method:engineering-specifier` with both drafts.
- **Parallel:** Spawn all three specifier agents simultaneously for larger domains.

**Step 3: Cross-cutting review** — After all three agents complete, review their outputs for ubiquitous language consistency and spec triangle alignment. Flag mismatches to the user. Verify the Specify exit gate: all aspect files are coherent, vocabulary-compliant, no `[NEEDS CLARIFICATION: ...]` markers remain, and every XS/AS/ES requirement can produce a test assertion.

**Step 4: Produce draft specs** — Present the proposed structure to the user via AskUserQuestion. Write files to `Documents/Specifications/{domain}/v0.1.0/` with three aspect files (`experience.md`, `architecture.md`, `engineering.md`) per domain directory, plus feature subdirectories as needed, after approval.

$ARGUMENTS
