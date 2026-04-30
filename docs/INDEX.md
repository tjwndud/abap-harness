# ABAP Harness Framework Index

Use this index to load only the smallest useful set of instructions.

## Default Entry

- `../CLAUDE.md`: core ABAP harness for Claude Code.
- `../AGENTS.md`: core ABAP harness for Codex.

## Topic Rules

- `rules/BDC.md`: BDC and batch-input flow.
- `rules/CDS.md`: Eclipse DDL source and CDS modeling rules.
- `rules/AMDP.md`: AMDP class and SQLScript rules.
- `rules/GATEWAY.md`: Gateway DPC_EXT CRUD and entityset rules.

## Operations

- `operations/HANDOFF_NEXT_STEPS.md`: decisions already locked and next expansion areas.
- `operations/FEEDBACK.md`: log repeated harness failures before changing rules.

## Load Strategy

1. Load the assistant-specific root file first: `../CLAUDE.md` or `../AGENTS.md`.
2. Inspect the target ABAP repository for the closest existing example.
3. Load exactly one topic rule when the task is BDC, CDS, AMDP, or Gateway.
4. Use operations documents only when maintaining this harness.
