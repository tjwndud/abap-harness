# ABAP Harness Framework

Reusable Claude Code framework for ABAP report, ALV, module pool, BDC, CDS, AMDP, and Gateway work.

## Structure

- `CLAUDE.md`: default core harness loaded first.
- `docs/INDEX.md`: map of the framework and load strategy.
- `docs/ARCHITECTURE.md`: directory structure and extension pattern.
- `docs/rules/`: focused topic rules.
- `docs/operations/`: handoff notes and feedback log.
- `.claude/commands/`: reusable Claude command prompts.

## Load Strategy

1. Start with `CLAUDE.md`.
2. Use `docs/INDEX.md` to choose the smallest extra reference.
3. Before implementation, inspect the closest existing ABAP source example.
4. For BDC, CDS, AMDP, or Gateway work, attach only the matching file under `docs/rules/`.
5. After generation, review the result against the loaded harness rules.

## Topic Files

- `docs/rules/BDC.md`: BDC and batch-input reference.
- `docs/rules/CDS.md`: CDS reference.
- `docs/rules/AMDP.md`: AMDP reference.
- `docs/rules/GATEWAY.md`: Gateway CRUD reference.

## Operations

- `docs/operations/HANDOFF_NEXT_STEPS.md`: decisions already locked and future expansion areas.
- `docs/operations/FEEDBACK.md`: operating log for issues to consider in a future v2 update.

## Usage

Copy this repository into an ABAP project as a reusable rules source, or copy only `CLAUDE.md` plus the relevant `docs/rules/` file for a smaller task.

Claude Code command prompts:

- `/harness`: start an implementation task with the ABAP harness.
- `/review`: review changes against the ABAP harness.
