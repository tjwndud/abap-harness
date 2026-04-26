# Claude ABAP Harness Handoff

## Purpose

This file is a handoff note for continuing the ABAP harness work on another laptop or in another session.

Open this file first, then open the main rules documents in the same folder.

## Files Already Created

- `CLAUDE_ABAP_HARNESS_RULES.md`
- `CLAUDE_AMDP_RULES.md`
- `CLAUDE_GATEWAY_RULES.md`

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

### Main ABAP Rules

Use `CLAUDE_ABAP_HARNESS_RULES.md` for:

- report structure
- declarations
- ALV
- popup ALV
- editable ALV
- splitter
- tab strip
- selection screen
- ranges
- SQL rules
- messaging
- flow control
- macros

### AMDP Rules

Use `CLAUDE_AMDP_RULES.md` for:

- Eclipse-context AMDP work
- class structure
- `if_amdp_marker_hdb`
- `TYPES`
- `CLASS-METHODS`
- SQLScript rules
- AMDP selection priority

### Gateway Rules

Use `CLAUDE_GATEWAY_RULES.md` for:

- `DPC_EXT`
- CRUD redefinition methods
- entityset filters
- `TYPE RANGE OF`
- response flow
- validation and error handling

## Next Work To Continue

The most useful next step is to keep extending the harness with still-missing repository topics.

Recommended order:

1. `BDC` rules
2. `CDS` rules
3. `Module Pool` rules
4. `Screen number / dynpro naming` rules
5. `Subroutine and FORM parameter style`
6. `CALL SCREEN / LEAVE TO SCREEN / screen navigation patterns`
7. `Search help / F4 handling`
8. `Icon handling`
9. `Top-of-page / document header ALV helper patterns`
10. `File upload / download / frontend service patterns`

## Recommended Immediate Next Prompts

When resuming with Codex or Claude, continue in this style:

- `Read CLAUDE_HANDOFF_NEXT_STEPS.md and CLAUDE_ABAP_HARNESS_RULES.md first, then continue defining missing repository rules.`
- `Next, search the repository for BDC examples and turn the repeated pattern into a new rules section.`
- `Next, search the repository for CDS examples and create a separate CDS rules markdown if needed.`
- `Continue the harness in repository-first style and ask rule-decision questions in the same style as before.`

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

- do not rewrite repository style into a cleaner but different style unless the user explicitly asks
- do not use `SELECT *`
- do not treat toolbar as one concept without first distinguishing ALV toolbar vs application toolbar
- do not use `RANGES` as a replacement for mandatory screen input
- do not assume AMDP just because a DB-heavy feature exists
- do not skip `FOR ALL ENTRIES` emptiness checks

## Best Resume Point

If a later session needs one exact resume point, start here:

`The next step is to define missing BDC, CDS, and Module Pool rules by searching the repository and adding them either into the main ABAP harness or into separate markdown files if the topic deserves its own document.`
