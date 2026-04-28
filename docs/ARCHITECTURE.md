# Architecture

## Directory Structure

```text
.
|-- CLAUDE.md
|-- README.md
|-- .claude/
|   `-- commands/
|       |-- harness.md
|       `-- review.md
`-- docs/
    |-- INDEX.md
    |-- ARCHITECTURE.md
    |-- rules/
    |   |-- AMDP.md
    |   |-- BDC.md
    |   |-- CDS.md
    |   `-- GATEWAY.md
    `-- operations/
        |-- FEEDBACK.md
        `-- HANDOFF_NEXT_STEPS.md
```

## Design Rules

- Root `CLAUDE.md` stays small enough to be the default loaded file.
- Topic-specific rules live under `docs/rules/` and are loaded only when needed.
- Maintenance and continuation notes live under `docs/operations/`.
- Claude command prompts live under `.claude/commands/`.
- Do not duplicate detailed topic rules in the root file.

## Extension Pattern

When adding a new ABAP topic, create one focused file under `docs/rules/` and add it to:

- `CLAUDE.md` framework navigation
- `docs/INDEX.md`
- `README.md`

When logging repeated failures or future work, update `docs/operations/FEEDBACK.md` or `docs/operations/HANDOFF_NEXT_STEPS.md` instead of changing rules immediately.
