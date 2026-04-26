# ABAP Harness

Reusable Claude Code harness for ABAP report, ALV, module pool, BDC, CDS, AMDP, and Gateway work.

## Load Strategy

- Use `CLAUDE.md` as the default instruction file.
- Do not load every rule file by default.
- Open detailed files only when the task needs that topic.

## Files

- `CLAUDE.md`: default core harness for Claude Code.
- `CLAUDE_BDC_RULES.md`: BDC and batch-input reference.
- `CLAUDE_CDS_RULES.md`: CDS reference.
- `CLAUDE_AMDP_RULES.md`: AMDP reference.
- `CLAUDE_GATEWAY_RULES.md`: Gateway CRUD reference.
- `CLAUDE_HANDOFF_NEXT_STEPS.md`: project handoff notes.

## Usage

Copy `CLAUDE.md` into an ABAP project root, or keep this repository as a reusable rules source.

For topic-specific work, explicitly attach the matching file, for example `@CLAUDE_BDC_RULES.md`, `@CLAUDE_CDS_RULES.md`, `@CLAUDE_AMDP_RULES.md`, or `@CLAUDE_GATEWAY_RULES.md`.

For Claude Code subdirectory loading, place focused `CLAUDE.md` files inside topic folders only when the real code repository has matching folders such as `alv/`, `bdc/`, `gateway/`, or `amdp/`.
