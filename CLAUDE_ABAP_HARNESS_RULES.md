# Claude ABAP Harness Rules

## Purpose

This document defines repository-first ABAP rules for Claude.

When there is no fixed rule yet, Claude should:

1. Find the closest existing example in the repository.
2. Explain the repository style first.
3. Present a practical alternative if needed.
4. Ask the user which direction to lock as a rule.

## Core Principle

- Base new code on the closest repository example.
- Copy structure, naming, declarations, screen flow, and ALV flow first; change only what is necessary.
- Repository consistency wins over cleaner-looking rewrites unless the user asks otherwise.

## Rule Decision Prompt Style

When a rule is not fixed, explain the repository style, suggest an alternative if useful, explain priority, then ask before adding the rule.

## Example Search Rule

- Before writing code, search similar repository examples.
- Prefer the closest functional shape:
  ALV, popup ALV, editable ALV, splitter, tab strip, selection screen, Gateway, AMDP, ADBC, module pool, and so on.
- If several match, choose the most reusable structure and naming.

## Hard Ban Rule

Forbidden unless the user explicitly allows an exception.

- Do not rewrite repository style into a cleaner but different style.
- Do not modernize include split, naming, ALV flow, or screen flow.
- Do not use `SELECT *`.
- Do not fetch broad data then filter in ABAP when SQL can filter.
- Do not use `FOR ALL ENTRIES` without checking the driver table.
- Do not put long SQL, DB changes, heavy loops, or business logic inside ALV event methods or PAI modules.
- Do not use `STARTING AT` for main screen `100` unless dialog-style display is required.
- Do not use `STOP` as the normal escape path after a screen has already been displayed.
- Do not leave an ALV screen or popup without freeing GUI resources when that screen owns them.
- Do not add or remove `COMMIT WORK` or `ROLLBACK WORK` without transaction reasoning.
- Do not switch from Open SQL reports to CDS, AMDP, ADBC, Gateway, or class-heavy architecture unless required.

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

- Global declarations belong in `TOP`.
- Use grouped `DATA :` style for globals.
- Local and temporary variables may use inline syntax.

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

## Naming Exception Rule

- If a variable, object, table, range, parameter, or helper value does not fit fixed prefixes, do not invent a new prefix silently.
- Explain why existing prefixes do not fit, suggest a naming direction, and ask first.
- If the role can be expressed with an existing prefix plus a clearer suffix, prefer that over creating a new prefix.

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
- If icon, color, celltab, text, status, or edit helpers are needed, build a processed structure instead of forcing the DB table shape.

Typical processed-structure cases include editable ALV, color/icon/status display, text helper fields, and subset-only display.

## Work Area / Structure Rule

- If the DB table or DDIC structure is used as-is, declare it with `TYPE`.
- If processed fields are required, create a local structure with `BEGIN OF gs_xxx ... END OF gs_xxx`.
- Keep the structure and internal table aligned to the same base shape.

## ALV Display Structure Rule

- If output is not the exact DB structure, default to `gs_body` and `gt_body`.
- Add helper fields such as `icon`, `color`, `celltab`, text, and status to the processed structure.
- If the table meaning must be clearer, specific names are allowed.

Allowed exceptions include `gt_main`, `gt_detail`, `gt_popup`, `gt_scarr`, and `gt_mara`; default remains `gs_body` and `gt_body`.

## Field Catalog Rule

- The default pattern is `FORM set_field_catalog`.
- Centralize field catalog construction where practical.
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

Common exceptions include `gs_playout`, `gs_pop_layout`, `gs_top_layout`, and `gs_bottom_layout`.

## ALV Variant Rule

- For ALV layout variants, use `gs_variant` by default.
- Set `gs_variant-report = sy-repid` before `set_table_for_first_display`.
- If a distinct ALV handle is needed, set `gs_variant-handle` such as `ALV1`, `GRD1`, `ZID`, or another repository-style handle.
- Local `ls_variant TYPE disvariant` is allowed when the example keeps the variant local to one display routine.
- Pass the variant into ALV first display through `is_variant`.
- Commonly pass `i_save = 'A'`, `i_default = 'X'`, and `is_variant` together.

## Event Handling Rule

- ALV event methods should be defined in `C01`.
- Event methods should remain thin and delegate to `PERFORM`.
- Actual processing belongs in `F01`.

- Preferred pattern: event method calls a same-purpose FORM such as `PERFORM on_double_click USING e_row e_column`.
Do not put long SQL or heavy business logic directly into the event method unless truly necessary.

## Editable ALV Rule

For editable ALV, follow the repeated repository pattern:

- add `celltab TYPE lvc_t_styl` into the processed structure
- set `gs_layout-stylefname = 'CELLTAB'`
- register edit event with `cl_gui_alv_grid=>mc_evt_modified`
- move post-edit handling into FORM logic such as `handle_modify_value`
- use `mc_style_enabled` and `mc_style_disabled` for cell-level control

## ALV Refresh Stable Rule

- Refresh logic should be centralized in a dedicated `FORM refresh_table`.
- When refreshing ALV, keep row and column position stable.
- Preferred repository pattern:
  `gs_stable = VALUE #( col = abap_true row = abap_true ).`
- Local `ls_stable` or `ls_stbl TYPE lvc_s_stbl` is allowed when the example keeps refresh state local.
- Call refresh through:
  `go_alv_grid->refresh_table_display`
  with `is_stable = gs_stable` or the local stable structure.
- After ALV data add, delete, edit, mode toggle, or redisplay when the container is already bound, use `PERFORM refresh_table`.

## Color Rule

- If ALV row or cell color is needed, create a processed structure field for color handling.
- Use processed display structures, not raw DB structures.
- Connect the color field through layout settings such as `ctab_fname`.

## Icon Handling Rule

- For ALV display, status, toolbar, or selection-screen function keys, use only SAP standard icons from the SAP icon table.
- Claude must not invent new icon constants, fake `icon_*` names, emoji-like values, or custom icon text.
- Choose an existing SAP icon whose meaning matches the function.
- Common repository-style examples include `icon_led_green`, `icon_led_red`, `icon_refresh`, `icon_insert_row`, `icon_delete_row`, `icon_toggle_display_change`, and `icon_xls`.
- If the right SAP icon is unclear, Claude must ask which existing SAP icon to use instead of guessing.
- For ALV status columns, add an icon/status field to the processed display structure instead of forcing the DB table shape.
- Keep icon assignment in data-shaping or state-change FORM logic, not inside field catalog setup only.

## Popup ALV Rule

- Popup trigger is not fixed.
- Popup may be opened by double-click, hotspot, screen button, ALV toolbar button, or other user command depending on the requirement.
- Popup screen management should still be separated.

- Use separate popup resources for popup screen, data, ALV object, field catalog, and layout.
- Preferred names include `go_pop_grid`, `gt_pfcat`, `gs_playout`, and `gs_pop_layout`.
- Preferred flow: prepare popup data, call popup screen, then display popup content.

## Multi-ALV and Splitter Rule

- When more than one ALV exists, use role-based names.
- Preferred grid names include `go_top_grid`, `go_bottom_grid`, `go_left_grid`, and `go_right_grid`.
- Keep data, field catalog, and layout names aligned, such as `gt_top`, `gt_bottom`, `gt_tfcat`, `gt_bfcat`, `gs_top_layout`, and `gs_bottom_layout`.
- Keep declarations global in `TOP`.

## Tab Strip Rule

- Use `gv_dynnr TYPE sy-dynnr` for subscreen switching.
- Manage tab change with normal screen flow and `OKCODE`.
- Separate tab data, ALV objects, field catalogs, and layouts only when needed.
- Reuse common FORM logic whenever possible.

## Selection Screen Rule

- Selection screens should follow repeated repository style.
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

- Use declaration-level restrictions first:
  `OBLIGATORY`, `NO INTERVALS`, `NO-EXTENSION`, `MODIF ID`
- Use `AT SELECTION-SCREEN` for business-rule validation that cannot be expressed by declaration only.

Examples include mutually dependent fields, radio-button dependent mandatory conditions, invalid date combinations, and business-specific value combinations.

## Range Rule

- For screen-entered conditions, prefer `SELECT-OPTIONS`.
- For internally built conditions, use `RANGES` or `TYPE RANGE OF`.
- Prefer `DATA lr_xxx TYPE RANGE OF <field>` for local or method-level range objects.
- Repository-style global `RANGES : lr_xxx FOR ...` is also allowed when the code shape matches existing examples.

## Range Naming Rule

- internal range object: `lr_`
- screen select-option: `so_`

Examples include `lr_bukrs`, `lr_belnr`, `lr_connid`, `so_budat`, and `so_matnr`.

## Range Build Rule

- When building range lines manually, set `sign`, `option`, `low`, and `high` explicitly.
- Repeated range construction may use repository macros such as `_range` or `_ranges`.
- Do not overuse macros if explicit code is clearer.

## SQL Selection Rule

- `SELECT *` is absolutely forbidden.
- Always list only the required fields.
- Keep selected columns compact.
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

Use `Open SQL` for normal reports and ALV reads, `CDS` for reusable semantic views, `AMDP` for DB pushdown, and `ADBC` for dynamic or special DB access.

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

- DB retrieval uses `get_`; ALV shaping/processing uses `set_`.

Do not merge heavy data retrieval and output shaping into one FORM when separation is clearer.

## DB Change Rule

- `INSERT`, `MODIFY`, `UPDATE`, and `DELETE` should follow the repository style.
- Prefer this flow: validation, DB execution, then result message handling.
- Success and failure should be clearly communicated through message handling.

## Transaction Rule

- Do not add or omit `COMMIT WORK` and `ROLLBACK WORK` blindly.
- Decide based on the business function and the closest repository example.
- If transaction scope is unclear, Claude should explain the issue and ask.

## BDC Reference Rule

- BDC and batch-input topics follow `CLAUDE_BDC_RULES.md`.
- Keep only the decision to use BDC here; details belong in the BDC document.

## Module Pool Rule

- Module pool work follows repository-style main program plus include split.
- Repeated repository pattern: main module pool program plus `TOP`, `O01`, `I01`, and `F01`.
- Keep the main module pool program minimal and use it mainly to connect the includes.

## Module Pool Include Rule

- Main module pool includes `...TOP`, `...O01`, `...I01`, and `...F01` in that order.
- Keep PBO modules in `O01`, PAI modules in `I01`, and reusable business logic in `F01`.
- Do not collapse module pool flow back into one long source unless the repository example for that task is already structured that way.

## Module Pool PBO Rule

- PBO logic should be implemented as `MODULE ... OUTPUT`.
- Repeated repository pattern: `MODULE status_0100 OUTPUT.`, `SET PF-STATUS ...`, and `SET TITLEBAR ...`.
- Screen initialization or display preparation may be delegated from PBO module to a FORM such as `display_screen`.

## Module Pool PAI Rule

- PAI logic should be implemented as `MODULE ... INPUT`.
- Repository style prefers `MODULE user_command_0100 INPUT.` with `CASE gv_okcode`.
- PAI modules should stay thin and delegate actual processing to `PERFORM`.
- Exit logic may use a dedicated `MODULE exit INPUT.` and handle cleanup before `LEAVE TO SCREEN 0`.

## Screen Navigation Rule

- For module pool and screen-based work, keep repository navigation flow explicit.
- Main screen `100` should usually be opened without `STARTING AT`.
- Popup, detail, and helper screens may use `STARTING AT` when the repository example opens them as a dialog-sized screen.
- Preferred examples: main `CALL SCREEN 100.`, popup `CALL SCREEN 110 STARTING AT 05 06.`
- `CALL SCREEN`, `LEAVE TO SCREEN`, and `LEAVE TO SCREEN 0` should follow the closest repository example.
- When closing an ALV screen or popup, free GUI resources first when needed, then use `LEAVE TO SCREEN 0`.
- After a screen has already been displayed, do not use `STOP` as the normal escape path. Use `EXIT` or screen navigation flow instead.
- Do not replace normal dynpro flow with a different navigation concept unless the user explicitly wants to change the style.

## Screen Number Rule

- Screen numbers should follow the repeated grouped pattern below.
- Main screens use the round hundred number.
- Popup screens use the matching `+10` number.
- Sub/detail/helper screens use the matching `+1` number.

- Preferred groups: `100/110/101`, `200/210/201`, and `300/310/301` for main/popup/sub.

- Do not invent irregular screen numbers first when the flow naturally matches one of these groups.

## Dynpro Naming Rule

- PBO and PAI module names should follow the screen number when screen-specific.
- Even when `CALL SCREEN` uses `100`, `110`, or `101`, module names may keep the repository's four-digit suffix style such as `0100`, `0110`, or `0101`.
- Repeated repository pattern includes `status_0100`, `user_command_0100`, `status_0110`, `user_command_0110`, `status_0101`, `user_command_0101`, `status_0210`, and `user_command_0210`.
- Keep `PF-STATUS` and `TITLEBAR` names aligned the same way when the screen is role-specific.
- Common aligned examples include `MENU100/TITLE100`, `MENU110/TITLE110`, and `MENU210/TITLE210`.

## Popup Screen Naming Rule

- If a screen is popup-oriented, the screen-specific processing FORM or MODULE name may also include the popup role.
- Repeated repository-style examples include `init_popup_process`, `init_pop1_process`, `init_pop2_process`, `pop_exit`, and `pop2_exit`.
- Popup container, grid, and screen logic names should stay aligned to the popup level instead of using unrelated names.

## FORM Parameter Rule

- Use simple `FORM ... .` with no parameters for global-state routines.
- Use `USING` for inputs and `CHANGING` for explicit write-back.
- Do not introduce `TABLES` in new FORM routines by default.
- Use `USING` or `CHANGING` instead unless preserving a repository-style classic FORM.

## FORM Scalar Parameter Rule

- For scalar inputs, prefer `USING VALUE(pv_xxx)`.
- Keep parameter names repository-style with `pv_` for scalar input and `po_` or `pr_` for object/reference roles when meaningful.
- Multi-argument helper FORMs may use positional `USING` parameters when the repository already treats that FORM as a compact utility.

Common examples include `FORM set_field_catalog USING VALUE(pv_key) ...`, `FORM on_double_click USING pv_row pv_column.`, and `FORM add_row USING ... pv_field pv_text.`

## FORM CHANGING Rule

- Use `CHANGING` when a FORM returns a value or intentionally mutates caller-owned data.
- Repository examples use it for confirmation answers, flags, and dialog-return values.
- Keep `CHANGING` parameters explicit rather than hiding that write-back through unrelated globals when the routine is naturally reusable.

Common examples include `FORM popup_confirm CHANGING pv_answer.` and `FORM check_line TABLES pt_roid ... CHANGING pv_flag.`

## FORM TABLES Rule

- Do not use `TABLES` for new FORM routines by default.
- Prefer `USING` for input tables and `CHANGING` for table mutation or return.
- `TABLES` is allowed only when preserving or extending an existing repository-style classic FORM.
- If `TABLES` is used with a structured row type, keep the repository-style `STRUCTURE` declaration such as `TABLES pt_roid STRUCTURE lvc_s_roid`.
- Do not replace existing repository-style `TABLES` mechanically if the surrounding code is still written in the same classic FORM style.

## TABLES Rule

- `TABLES` is allowed only when needed for selection screen, dynpro, or screen field binding.
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

- Check `sy-subrc` after result-sensitive operations.
- Follow-up depends on the function.

Possible follow-up modes include message only, branch and continue, or stop further execution. Claude must decide based on the business flow, not one mechanical rule.

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

- Application toolbar uses `PF-STATUS`, `gv_okcode`, and `CASE gv_okcode`.
- Do not mix ALV toolbar and application toolbar carelessly.
- Toolbar implementation should be decided after Claude identifies which toolbar type is needed:
  ALV toolbar or application toolbar.
- If unclear, Claude should ask the user first.

## Message Rule

- Prefer message classes over hardcoded text.
- Connect message class from the report definition at the top level.
- Text symbol naming may follow functional prefixes.

Examples: create-related `C01`/`C02`, delete-related `D01`/`D02`, modify-related `M01`/`M02`.

## Comment Style Rule

- Keep repository-style section comments.
- Standalone comment lines must begin with `*--`.
- Inline comments must be written after the code using `"` style.

Example style: standalone `*-- For ALV`; inline `DATA : go_container TYPE REF TO cl_gui_custom_container. " Main container`.

## Macro Reference Rule

- Macro usage must follow `CLAUDE_MACRO_RULES.md`.
- Keep macro usage small and repetitive. Do not hide complex business logic inside macros.

## Frequently Reused Repository Names Rule

When a repeated repository name already exists, keep it if the role matches.

Common repeated names include `go_container`, `go_alv_grid`, `go_pop_grid`, `go_event`, `gs_body`, `gt_body`, `gs_layout`, `gs_variant`, `gs_fcat`, `gt_fcat`, `gv_okcode`, `gv_dynnr`, `set_init_value`, `create_object`, `display_screen`, `get_main_data`, `get_detail_data`, `get_pop_data`, `set_field_catalog`, `set_layout`, `on_double_click`, `on_hotspot_click`, `handle_add_toolbar`, `handle_user_command`, and `handle_modify_value`.

## Topics That Must Be Kept in Separate Documents

The following topics must follow their dedicated Markdown files: CDS, BDC, Macro, AMDP, and Gateway CRUD.
