Run the axe-method Specify phase. You are the orchestrator.

**Step 1: Establish the Domain** — Do this yourself in the main conversation. Ask the user:
1. "What does this product do, in one sentence?"
2. "Who are the users?"
3. Use Task (Explore) to scan the codebase for class/model names. Present discovered terms via AskUserQuestion and ask which are part of the ubiquitous language.

Build a glossary. This glossary must be consistent across all three spec types (Experience, Architecture, Engineering).

**Step 2: Delegate to specifier agents** — Based on complexity, choose sequential or parallel:

- **Sequential:** Delegate to `axe-method:experience-specifier` first, then `axe-method:architecture-specifier` with the Experience draft, then `axe-method:engineering-specifier` with both drafts.
- **Parallel:** Spawn all three specifier agents simultaneously for larger domains.

**Step 3: Cross-cutting review** — After all three agents complete, review their outputs for ubiquitous language consistency and spec triangle alignment. Flag mismatches to the user.

**Step 4: Produce draft specs** — Present the proposed structure to the user via AskUserQuestion. Write files to `Documents/Specifications/{Experience,Architecture,Engineering}/{Domain}/v0.1.0/` after approval.

$ARGUMENTS
