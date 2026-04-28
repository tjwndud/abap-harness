# Claude ABAP Harness Handoff

## Purpose

This file is a handoff note for continuing the ABAP harness work on another laptop or in another session.

For normal Claude Code use, load `CLAUDE.md` first.
Open detailed rule files only when the task needs that topic.

## Files Already Created

- `CLAUDE.md`
- `docs/rules/AMDP.md`
- `docs/rules/BDC.md`
- `docs/rules/CDS.md`
- `docs/rules/GATEWAY.md`

## What Has Already Been Decided

The following directions are already fixed and should be treated as repository rules unless the user changes them later.

- repository-first implementation
- include split by `TOP`, `C01`, `O01`, `I01`, `F01`
- global declarations in repository-style grouped `DATA :`
- local temporary declarations may use inline syntax
- DB shape as-is uses `TYPE`
- processed ALV structures use local structure plus `LIKE TABLE OF`
- main ALV field catalog pattern stays centered on `set_field_catalog`
- `gs_fcat` is one global top declaration, `gt_fcat` tables are global and may be separated per ALV
- main ALV layout uses `gs_layout`
- popup or helper ALV may use `gs_playout` or `gs_pop_layout`
- event handler methods stay thin and delegate to `PERFORM`
- editable ALV uses `celltab`, `stylefname`, and edit-event registration
- ALV color handling uses processed structures and layout link fields such as `ctab_fname`
- popup trigger is flexible, but popup resources are separated from main screen resources
- splitter and multi-ALV naming should be role-based
- tab strip uses `gv_dynnr` and repository screen flow
- selection-screen style should follow repository examples
- mandatory single-value input should prefer `PARAMETERS`
- `SELECT-OPTIONS` should be used only when ranges or multi-value behavior are actually needed
- declaration-level input restriction should be used before `AT SELECTION-SCREEN`
- screen-driven conditions use `SELECT-OPTIONS`
- internal dynamic conditions use `RANGES` or `TYPE RANGE OF`
- `SELECT *` is absolutely forbidden
- Open SQL is the default first choice unless another technology is justified
- `JOIN` is preferred before `FOR ALL ENTRIES`, and `FOR ALL ENTRIES` must check the driver table first
- DB retrieval uses `get_` naming
- ALV shaping/setting uses `set_` naming
- `gv_okcode` plus `CASE gv_okcode` stays the standard screen command flow
- message class usage is preferred over hardcoded text
- standalone comment lines use `*--`
- inline comments must stay after the code using `"`
- macros are allowed only for short repeated patterns and should stay in `TOP`
- Claude must search for similar repository examples before coding

## Current Document Structure

### Default Core Harness

Use `CLAUDE.md` by default for any ABAP program.

Do not load all reference files at once. Load topic files only when needed.

### Core ABAP Rules

Use `CLAUDE.md` for report structure, declarations, ALV, module pool, popup ALV, editable ALV, splitter, tab strip, selection screen, ranges, SQL, messaging, flow control, and macro rules.

### BDC Rules

Use `docs/rules/BDC.md` only for BDC or batch-input work.

### AMDP Rules

Use `docs/rules/AMDP.md` for:

- Eclipse-context AMDP work
- class structure
- `if_amdp_marker_hdb`
- `TYPES`
- `CLASS-METHODS`
- SQLScript rules
- AMDP selection priority

### CDS Rules

Use `docs/rules/CDS.md` for:

- Eclipse-context CDS work
- annotation order
- `define view`
- OData publish annotations
- field selection and key handling
- CDS selection priority

### Gateway Rules

Use `docs/rules/GATEWAY.md` for:

- `DPC_EXT`
- CRUD redefinition methods
- entityset filters
- `TYPE RANGE OF`
- response flow
- validation and error handling

## Next Work To Continue

The most useful next step is to keep extending the harness with still-missing repository topics.

Recommended order:

1. `Top-of-page / document header ALV helper patterns`
2. `File upload / download / frontend service patterns`
3. `Search help / F4 handling` only when requested

## Recommended Immediate Next Prompts

When resuming with Codex or Claude, continue in this style:

- `Read CLAUDE.md first. Open detailed references only if the task needs them.`
- `Continue the harness in repository-first style and ask rule-decision questions in the same style as before.`

These are now done:

- `BDC` rules split into `docs/rules/BDC.md`
- `Macro` rules absorbed into `CLAUDE.md`
- `Module Pool` rules added to the main ABAP harness
- `CDS` rules split into `docs/rules/CDS.md`
- `Screen number / dynpro naming` rules added to the main ABAP harness
- `Subroutine and FORM parameter style` rules added to the main ABAP harness
- `ALV variant / refresh stable` rules added
- `Icon handling` rules added
- Root `CLAUDE.md` core harness created for default Claude Code loading
- Oversized full reference and thin macro reference removed after key details were absorbed into `CLAUDE.md`

## How Rule Questions Should Continue

When a topic is not fixed yet, continue using this interaction style:

1. show what the repository currently does
2. explain the practical alternative if there is one
3. explain real-use preference or priority
4. ask the user which rule to lock

Example style:

`In your GitHub examples, this is mainly written this way. Do you want to keep this style? If needed, I can also suggest an alternative. In practice, this repository style is often used when consistency matters more than introducing a new pattern.`

## Important Repository Study Areas

When continuing the work, these folders are especially useful because they contain repeated practical patterns:

- `강사님 실무용 코드 모음`
- `GATEWAY SERVICE`
- `ADMP IN ECLIPSE`
- editable ALV examples
- popup ALV examples
- splitter examples
- tab strip examples

## Things To Be Careful About

- do not load every rule file by default
- do not rewrite repository style into a cleaner but different style unless the user explicitly asks
- do not use `SELECT *`
- do not treat toolbar as one concept without first distinguishing ALV toolbar vs application toolbar
- do not use `RANGES` as a replacement for mandatory screen input
- do not assume AMDP just because a DB-heavy feature exists
- do not skip `FOR ALL ENTRIES` emptiness checks

## Best Resume Point

If a later session needs one exact resume point, start here:

`Start with CLAUDE.md. If the task is BDC, CDS, AMDP, or Gateway, explicitly attach only the matching detailed file.`
