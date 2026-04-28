# Claude CDS Rules

## Purpose

This document defines CDS-specific rules.
CDS is managed separately from the main ABAP harness because repository examples show it in Eclipse DDL source form, with its own annotations and modeling flow.

## Separation Rule

- CDS rules are handled in a separate Markdown file.
- CDS work assumes Eclipse-based DDL source context.
- If the requirement can be solved with normal `Open SQL`, Claude should not jump to CDS automatically.

## Technology Priority Rule

Before selecting CDS, Claude should explain the normal priority:

1. `Open SQL`
2. `CDS`
3. `AMDP`
4. `ADBC`

CDS should be chosen when a reusable semantic view, OData publication, or model-driven structure is actually needed.

## Repository Style Rule

Claude must first find the closest CDS example in this repository and mirror its structure.

Common repeated repository CDS patterns:

- top-level annotations before the view definition
- `@AbapCatalog.sqlViewName`
- `@AccessControl.authorizationCheck`
- `@EndUserText.label`
- optional `@OData.publish: true`
- `define view ... as`
- explicit field list with `key`
- short table aliases such as `a`, `b`

## Annotation Rule

- Keep annotations grouped above the DDL definition.
- If the repository example uses OData annotations, keep them in the same top annotation block.
- Do not scatter annotations deep inside the field list unless the repository example for that exact shape does so.

## View Definition Rule

- Prefer repository-style `define view ... as` when the closest example uses that pattern.
- Keep joins explicit.
- Keep aliases short and consistent.
- Mark key fields explicitly with `key`.

## Field Selection Rule

- Never use `SELECT *` thinking in CDS either.
- Expose only the fields actually needed.
- Keep the field list aligned with the intended consumer: ALV, OData, or reusable read model.
- If text joins are required, follow the repository example by joining the text table explicitly.

## OData Rule

- If the repository example is a CDS-for-OData pattern, OData publication may be defined directly in the CDS annotations.
- Claude should explain whether the user wants a reusable CDS only, or a CDS that is also meant for OData publication.
- Do not add `@OData.publish: true` unless the user-facing service intent is clear.

## Naming Rule

- Keep repository naming first.
- Reuse the closest repository naming pattern for the DDL source object name and SQL view name.
- Do not invent a completely different naming convention when a close repository example already exists.

## Question Rule

If both `Open SQL` and `CDS` are realistic, Claude should ask in repository-first style:

`This can be built with Open SQL first, and CDS is usually the next option when you want a reusable view or OData-friendly model. In your repository, CDS is written in this annotation-first Eclipse style. Do you want to keep that CDS direction here?`
