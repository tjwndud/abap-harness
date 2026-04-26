# Claude Gateway CRUD Rules

## Purpose

This document defines Gateway-specific rules for DPC_EXT CRUD implementation.
Gateway is managed separately from the main ABAP harness because service implementation has its own method structure and runtime flow.

## Separation Rule

- Gateway rules are maintained in a separate Markdown file.
- Claude should use this document when the task is OData Gateway CRUD or entityset handling.

## Repository-First Rule

- Claude must search the repository for the closest Gateway example first.
- Reuse entity naming, method naming, filter handling, and CRUD flow from the nearest example before inventing a new service pattern.

## DPC_EXT Rule

- Gateway CRUD logic should be implemented in `DPC_EXT`.
- Claude should follow repository-style redefinition methods for entity or entityset operations.

Common repository pattern:

- `..._CREATE_ENTITY`
- `..._GET_ENTITY`
- `..._GET_ENTITYSET`
- `..._UPDATE_ENTITY`
- `..._DELETE_ENTITY`

## Method Naming Rule

- Use the generated entityset-based method names from the service model.
- Do not rename generated CRUD method identities into unrelated custom names.

## Filter Handling Rule

- Internal filter objects may use `TYPE RANGE OF`.
- Range names should use `lr_` prefix.
- Keep filter processing explicit and easy to trace.

Examples:

- `lr_belnr`
- `lr_connid`
- `lr_carrid`

## SQL Rule

- `SELECT *` is forbidden here as well.
- Select only required fields.
- Keep selected fields aligned with the entity structure or processed response structure.

## CRUD Flow Rule

Follow business-safe ordering.

Typical flow:

1. read request/filter/input values
2. validate business conditions
3. execute DB read or change
4. build response entity or entityset
5. handle message or error flow

## Validation Rule

- If required request data is missing, Claude should not continue blindly.
- Validation, DB processing, and response shaping should remain separated logically.

## Message and Error Rule

- Prefer repository-style message handling.
- If a failure should stop processing, the implementation must stop further flow after the message or error handoff.
- If the situation is informational only, Claude may continue when the business flow allows it.

## Technology Priority Rule

Gateway service implementation does not automatically mean AMDP.
Normal priority still applies:

1. `Open SQL`
2. `CDS`
3. `AMDP`
4. `ADBC`

If Gateway is backed by CDS or AMDP, Claude should explain why.

## Question Rule

If the required Gateway implementation could be done in several ways, Claude should explain the priority first:

`For this Gateway feature, the first option is a straightforward Open SQL-based DPC_EXT implementation. If you want reusable semantic modeling, CDS can also be considered. Do you want to keep it in the repository's normal DPC_EXT style?`

## Scope Rule

- This document governs Gateway CRUD structure and service-side data handling.
- General ALV, selection-screen, screen flow, and report include rules remain in the main ABAP harness document.
