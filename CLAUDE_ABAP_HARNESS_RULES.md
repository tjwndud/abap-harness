# Claude ABAP Harness Rules

## Purpose

This document defines the ABAP coding rules Claude must follow for this repository.

The goal is not to invent a new style.
Claude must first follow the style already learned and repeated in this repository.

When there is no fixed rule yet, Claude should:

1. Find the closest existing example in the repository.
2. Explain the repository style first.
3. Present a practical alternative if needed.
4. Ask the user which direction to lock as a rule.

## Core Principle

- New code must be based on the closest existing repository example.
- Claude should copy the closest structure, naming, declaration style, screen flow, and ALV flow first, then modify only what is necessary.
- If an implementation style looks technically cleaner but differs from the repository style, repository consistency comes first unless the user explicitly asks otherwise.

## Rule Decision Prompt Style

When a rule is not fixed yet, Claude should ask in this style:

`In your GitHub examples, this is mainly written in this way. Do you want to keep this style? If needed, I can also suggest an alternative. In practice, this repository style is commonly used in real SAP GUI report work when consistency matters.`

When there are multiple technical choices, Claude should also explain priority and reason before asking.

## Example Search Rule

- Before writing code, Claude must search for similar examples in this repository.
- Claude must prefer the example with the closest functional shape:
  ALV, popup ALV, editable ALV, splitter, tab strip, selection screen, Gateway, AMDP, ADBC, module pool, and so on.
- If several examples are similar, Claude should choose the one whose structure and naming are the most reusable.

## Include Structure Rule

Use the existing include split pattern.

- `TOP`: global declarations, tables, types, macros, common variables, ALV declarations
- `C01`: local class and event handler declarations
- `O01`: PBO logic
- `I01`: PAI logic
- `F01`: FORM routines and business logic

Do not mix unrelated responsibilities into one include unless there is a strong reason.

## TOP Declaration Order Rule

Prefer the repeated repository order below.

1. `Macro`
2. `TABLES`
3. `Class Instance`
4. `Internal Table / Work Area`
5. `ALV related declarations`
6. `Common Variable`
7. `Screen field / Toolbar / Popup / auxiliary declarations` when needed

## Global and Local Declaration Rule

- Global declarations must follow the repository style.
- Global declarations belong in `TOP`.
- Global declarations should use grouped `DATA :` style.
- Local and temporary variables may use inline syntax.

Allowed local inline examples:

```abap
DATA(lv_bukrs) = '1000'.
LOOP AT gt_body INTO DATA(gs_body).
READ TABLE gt_body INTO DATA(gs_row) INDEX 1.
SELECT carrid connid
  FROM spfli
  INTO TABLE @DATA(gt_spfli).
```

Do not convert top-level global declarations into inline style.

## Naming Rule

Use repository naming first.

- object reference: `go_`
- structure/work area: `gs_`
- internal table: `gt_`
- variable: `gv_`
- constant: `gc_`
- range variable: `lr_`
- select-options: `so_`
- parameter: `pa_` or repeated repository parameter naming

Do not invent new naming patterns unless the repository has no reusable equivalent.

## Class Instance Naming Rule

- main container: `go_container`
- main ALV: `go_alv_grid`
- popup ALV: `go_pop_grid`
- multi-ALV objects: `go_top_grid`, `go_bottom_grid`, `go_left_grid`, `go_right_grid`
- event instance: `go_event` when instance-based
- local event class name: `lcl_event_handler` by default

Use more specific names only when multiple objects of the same kind must be separated clearly.

## Internal Table Rule

- If the DB table or DDIC structure can be used as-is, use `TYPE TABLE OF`.
- If display-only or processing-only fields are needed, use a local structure plus `LIKE TABLE OF gs_xxx`.
- If fields such as icon, color, celltab, text, status, or editing helpers are needed, always build a processed structure instead of forcing the DB table shape.

Examples of when to use processed structure:

- editable ALV
- ALV color handling
- icon/status display
- text helper fields
- subset-only ALV display

## Work Area / Structure Rule

- If the DB table or DDIC structure is used as-is, declare it with `TYPE`.
- If processed fields are required, create a local structure with `BEGIN OF gs_xxx ... END OF gs_xxx`.
- Keep the structure and internal table aligned to the same base shape.

## ALV Display Structure Rule

- If the output is not the exact DB structure, use `gs_body` and `gt_body` as the default output structure naming.
- Add display helper fields such as `icon`, `color`, `celltab`, text fields, status fields, and similar fields into the processed structure.
- If the table meaning must be clearer, specific names are allowed.

Allowed exceptions:

- `gt_main`
- `gt_detail`
- `gt_popup`
- `gt_scarr`
- `gt_mara`

Default remains `gs_body` and `gt_body`.

## Field Catalog Rule

- The default pattern is `FORM set_field_catalog`.
- Field catalog construction should be centralized as much as possible.
- Single ALV:
  declare `gs_fcat` and `gt_fcat` globally in `TOP`.
- Multiple ALVs:
  declare `gs_fcat` only once globally in `TOP`.
  declare separate target field catalogs globally in `TOP`, such as `gt_fcat`, `gt_fcat2`, `gt_pfcat`, `gt_tfcat`, `gt_bfcat`.
- Pass the target field catalog to the common field catalog FORM when possible.

Do not declare per-ALV field catalog tables locally when they are part of the screen design structure.

## Layout Rule

- Main ALV layout should use `gs_layout` by default.
- Separate layout structures should be created only when the role differs clearly.

Common exceptions:

- `gs_playout`
- `gs_pop_layout`
- `gs_top_layout`
- `gs_bottom_layout`

## Event Handling Rule

- ALV event methods should be defined in `C01`.
- Event methods should remain thin and delegate to `PERFORM`.
- Actual processing belongs in `F01`.

Preferred pattern:

```abap
METHOD on_double_click.
  PERFORM on_double_click USING e_row e_column.
ENDMETHOD.
```

Do not put long SQL or heavy business logic directly into the event method unless truly necessary.

## Editable ALV Rule

For editable ALV, follow the repeated repository pattern:

- add `celltab TYPE lvc_t_styl` into the processed structure
- set `gs_layout-stylefname = 'CELLTAB'`
- register edit event with `cl_gui_alv_grid=>mc_evt_modified`
- move post-edit handling into FORM logic such as `handle_modify_value`
- use `mc_style_enabled` and `mc_style_disabled` for cell-level control

## Color Rule

- If ALV row or cell color is needed, create a processed structure field for color handling.
- Use processed display structures, not raw DB structures.
- Connect the color field through layout settings such as `ctab_fname`.

## Popup ALV Rule

- Popup trigger is not fixed.
- Popup may be opened by double-click, hotspot, screen button, ALV toolbar button, or other user command depending on the requirement.
- Popup screen management should still be separated.

Use separate popup resources:

- popup screen
- popup data
- popup ALV object
- popup field catalog
- popup layout

Preferred naming:

- `go_pop_grid`
- `gt_pfcat`
- `gs_playout` or `gs_pop_layout`

Preferred flow:

1. prepare popup data
2. call popup screen
3. display popup content

## Multi-ALV and Splitter Rule

- When more than one ALV exists, use role-based names.
- Preferred names:
  `go_top_grid`, `go_bottom_grid`, `go_left_grid`, `go_right_grid`
- Use aligned names for data, field catalog, and layout:
  `gt_top`, `gt_bottom`, `gt_tfcat`, `gt_bfcat`, `gs_top_layout`, `gs_bottom_layout`
- Keep declarations global in `TOP`.

## Tab Strip Rule

- Use `gv_dynnr TYPE sy-dynnr` for subscreen switching.
- Manage tab change with normal screen flow and `OKCODE`.
- Separate tab data, ALV objects, field catalogs, and layouts only when needed.
- Reuse common FORM logic whenever possible.

## Selection Screen Rule

- Selection screen coding should follow the repeated repository style.
- Use repository-style `PARAMETERS`, `SELECT-OPTIONS`, `RADIOBUTTON GROUP`, `MODIF ID`, and `AT SELECTION-SCREEN`.
- Do not modernize selection screens just for syntax preference.

## Input Method Rule

- If a condition is mandatory and naturally single-value, prefer `PARAMETERS`.
- Do not open a simple mandatory single-value condition as `SELECT-OPTIONS` without a real reason.
- Use `SELECT-OPTIONS` when range input, multiple values, include/exclude logic, or business flexibility is actually needed.

## Selection Option Constraint Rule

Claude should proactively suggest screen options based on the real condition shape.

- mandatory value: `OBLIGATORY`
- single value only: `NO INTERVALS`, `NO-EXTENSION`
- dynamic screen control: `MODIF ID`

Claude should explain why the option is appropriate before locking the pattern if the user has not fixed it yet.

## Selection Screen Validation Rule

- Use declaration-level restriction first when possible:
  `OBLIGATORY`, `NO INTERVALS`, `NO-EXTENSION`, `MODIF ID`
- Use `AT SELECTION-SCREEN` for business-rule validation that cannot be expressed by declaration only.

Examples:

- mutually dependent fields
- radio-button dependent mandatory conditions
- invalid date combinations
- business-specific value combinations

## Range Rule

- If the user enters conditions on the screen, prefer `SELECT-OPTIONS`.
- If the program must build conditions internally, use `RANGES` or `TYPE RANGE OF`.
- Prefer `DATA lr_xxx TYPE RANGE OF <field>` for local or method-level range objects.
- Repository-style global `RANGES : lr_xxx FOR ...` is also allowed when the code shape matches existing examples.

## Range Naming Rule

- internal range object: `lr_`
- screen select-option: `so_`

Examples:

- `lr_bukrs`
- `lr_belnr`
- `lr_connid`
- `so_budat`
- `so_matnr`

## Range Build Rule

- When building range lines manually, set `sign`, `option`, `low`, and `high` explicitly.
- Repeated range construction may use repository-style macros such as `_range` or `_ranges`.
- Do not overuse macros if explicit code is clearer.

## SQL Selection Rule

- `SELECT *` is absolutely forbidden.
- Always list only the required fields.
- Keep selected columns compact and purpose-driven.
- For multi-row fetch, prefer `INTO TABLE`.
- For processed structures with corresponding fields, `INTO CORRESPONDING FIELDS OF TABLE` may be used.

Do not use the pattern of fetching everything first and trimming later.

## SQL Technology Priority Rule

Claude should explain the recommended priority before implementation when multiple data access technologies are possible.

Default priority:

1. `Open SQL`
2. `CDS`
3. `AMDP`
4. `ADBC`

General guidance:

- `Open SQL`: normal reports, ALV screens, straightforward reads
- `CDS`: reusable semantic view or model-driven design
- `AMDP`: DB pushdown, large volume, complex aggregation
- `ADBC`: dynamic SQL or special DB-level access patterns

## JOIN / FAE / Subquery Rule

When several query forms are possible, Claude should explain priority and reason first.

Default priority:

1. `JOIN`
2. `FOR ALL ENTRIES`
3. `Subquery`

## FOR ALL ENTRIES Rule

- `FOR ALL ENTRIES` must never run without checking the driver internal table first.
- Always check `IF gt_xxx IS NOT INITIAL.` before the query.
- If a `JOIN` solves the problem naturally, prefer `JOIN` first.

## Data Retrieval and Processing Naming Rule

- DB retrieval logic should use `get_`.
- ALV output shaping and processing should use `set_`.

Examples:

- `get_main_data`
- `get_detail_data`
- `get_pop_data`
- `set_field_catalog`
- `set_layout`
- `set_body_data`
- `set_display_data`

Do not merge heavy data retrieval and output shaping into one FORM when separation is clearer.

## DB Change Rule

- `INSERT`, `MODIFY`, `UPDATE`, and `DELETE` should follow the repository style.
- Prefer this flow:

1. validation
2. DB execution
3. result message handling

- Success and failure should be clearly communicated through message handling.

## Transaction Rule

- Do not add or omit `COMMIT WORK` and `ROLLBACK WORK` blindly.
- Decide based on the business function and the closest repository example.
- If transaction scope is unclear, Claude should explain the issue and ask.

## TABLES Rule

- `TABLES` is allowed when actually needed for selection screen, dynpro, or screen field binding.
- Do not declare `TABLES` unnecessarily for simple internal logic.
- If only the field type is needed, prefer `TYPE`-based declarations.

## READ TABLE Rule

- Follow repository style.
- For existence-only checks, prefer `TRANSPORTING NO FIELDS`.
- Check `sy-subrc` after `READ TABLE` when the result matters.

## LOOP Rule

- Follow repository style.
- Keep read-only loops and modifying loops conceptually clear.
- Use `INTO` for simple read/format flows when natural.
- Use `ASSIGNING` when direct modification or reference-style handling is more appropriate.

## sy-subrc Rule

- Check `sy-subrc` after result-sensitive operations when needed.
- The follow-up action depends on the function.

Possible follow-up modes:

- message only
- branch handling and continue
- stop further execution

Claude must decide based on the business flow, not by one mechanical rule.

## Flow Control Rule

Use flow control according to actual scope.

- `CHECK`: stop current block when a simple precondition is not met
- `EXIT`: leave current loop or repeated block
- `RETURN`: finish current FORM, METHOD, or MODULE
- `LEAVE TO SCREEN`: screen navigation
- `LEAVE LIST-PROCESSING`: only for list-processing flow

When a message is shown and execution must stop, pair the message with the correct flow control statement.

## OKCODE Rule

- Manage screen command flow through `gv_okcode`.
- Declare `gv_okcode TYPE sy-ucomm` globally in `TOP`.
- In PAI, prefer `CASE gv_okcode`.

## PF-STATUS and Toolbar Rule

- Application toolbar handling should use `PF-STATUS` plus `gv_okcode` plus `CASE gv_okcode`.
- ALV toolbar and application toolbar are different concepts and must not be mixed carelessly.
- Toolbar implementation should be decided after Claude identifies which toolbar type is needed:
  ALV toolbar or application toolbar.
- If unclear, Claude should ask the user first.

## Message Rule

- Prefer message class usage over hardcoded text.
- Connect message class from the report definition at the top level.
- Text symbol naming may follow functional prefixes.

Examples:

- create-related: `C01`, `C02`, `C03`
- delete-related: `D01`, `D02`, `D03`
- modify-related: `M01`, `M02`, `M03`

## Comment Style Rule

- Keep repository-style section comments.
- Standalone comment lines must begin with `*--`.
- Inline comments must be written after the code using `"` style.

Examples:

```abap
*-- For ALV
DATA : gt_fcat   TYPE lvc_t_fcat,
       gs_fcat   TYPE lvc_s_fcat.
```

```abap
DATA : go_container TYPE REF TO cl_gui_custom_container. " Main container
```

## Macro Rule

- Define macros in `TOP`.
- Use macros only for short, highly repetitive patterns.

Common allowed macro purposes from the repository:

- initialization
- popup confirmation
- range build
- toolbar button append
- message text build
- date calculation
- conversion function wrap

Do not hide complex business logic inside macros.

## Frequently Reused Repository Names Rule

When a repeated repository name already exists, keep it if the role matches.

Common repeated names:

- `go_container`
- `go_alv_grid`
- `go_pop_grid`
- `go_event`
- `gs_body`
- `gt_body`
- `gs_layout`
- `gs_variant`
- `gs_fcat`
- `gt_fcat`
- `gv_okcode`
- `gv_dynnr`
- `set_init_value`
- `create_object`
- `display_screen`
- `get_main_data`
- `get_detail_data`
- `get_pop_data`
- `set_field_catalog`
- `set_layout`
- `on_double_click`
- `on_hotspot_click`
- `handle_add_toolbar`
- `handle_user_command`
- `handle_modify_value`

## Topics That Must Be Kept in Separate Documents

The following topics are intentionally split into separate rule documents:

- AMDP
- Gateway CRUD

Those topics must follow their own dedicated Markdown files in the same folder.
