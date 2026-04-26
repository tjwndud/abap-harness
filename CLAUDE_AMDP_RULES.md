# Claude AMDP Rules

## Purpose

This document defines AMDP-specific rules.
AMDP is managed separately from the main ABAP harness because development context and tooling differ.

## Separation Rule

- AMDP rules are handled in a separate Markdown file.
- AMDP work assumes Eclipse-based development context.
- If the requested function can be solved by normal Open SQL, Claude should not jump to AMDP automatically.

## Technology Priority Rule

Before selecting AMDP, Claude should explain the normal priority:

1. `Open SQL`
2. `CDS`
3. `AMDP`
4. `ADBC`

AMDP should be chosen when DB pushdown is truly needed.

Typical reasons:

- large volume processing
- complex aggregation
- SQLScript benefit
- database-side reusable logic

## Repository Style Rule

Claude must first find the closest AMDP example in this repository and mirror its structure.

Common repeated repository AMDP patterns:

- `INTERFACES if_amdp_marker_hdb.`
- local `TYPES` for result shape
- `CLASS-METHODS`
- `METHOD ... BY DATABASE PROCEDURE FOR HDB LANGUAGE SQLSCRIPT`
- `USING ...`

## Class Structure Rule

Prefer this repository shape:

1. class definition
2. `PUBLIC SECTION`
3. `INTERFACES if_amdp_marker_hdb.`
4. result structure `TYPES`
5. `CLASS-METHODS`
6. implementation with SQLScript methods

## Types Rule

- Define explicit result `TYPES`.
- Keep type names repository-style:
  `ts_xxx`, `tt_xxx`
- If multiple result sets exist, use separate result type pairs.

## Method Rule

- AMDP methods should normally be `CLASS-METHODS` when repository examples match that pattern.
- The method signature should clearly separate importing and exporting values.
- Use repository-style parameter naming such as `iv_`, `et_`.

## SQLScript Rule

- Never use `SELECT *`.
- Always list only required columns.
- Keep output columns aligned with the declared result type.
- Use `USING` with explicit tables, views, or AMDP helper methods actually required.
- If client or language filtering is needed, apply it explicitly.

## Reuse Rule

- If an AMDP helper method already exists or the repository shows method-to-method AMDP reuse, Claude may follow that pattern.
- If not necessary, do not introduce additional AMDP layering.

## Question Rule

If AMDP is technically possible but not obviously necessary, Claude should ask in repository-first style:

`This can be implemented with Open SQL first, and AMDP is usually the later option when DB pushdown is really needed. In your repository, AMDP is used in these kinds of cases. Do you want to proceed with AMDP here?`

## Non-AMDP Spillover Rule

- Screen flow, ALV flow, popup handling, and normal report rules still belong to the main ABAP harness document.
- This document only governs AMDP-specific structure and selection criteria.
