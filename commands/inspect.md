Delegate to the `axe-method:inspector` agent to run a six-direction audit across Spec, Code, and Test.

The agent audits all six gap directions (Spec↔Code, Spec↔Test, Code↔Test), writes a structured inspection report to `Documents/Inspections/{timestamp}/`, and produces findings for the next Specify iteration. It is read-only on production code. It preloads the `axe-method:axe-method` skill and uses project-level persistent memory.

Pass the following as scope for what to inspect:

$ARGUMENTS
