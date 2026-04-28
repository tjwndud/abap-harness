# Claude ABAP Harness

Use this file as the default Claude Code instruction file for ABAP programs.
Load only this file by default. Open detailed rule files only when the task needs that topic.

## Operating Rule

- Follow the closest repository example before inventing style.
- Before implementation, ask for or inspect the closest existing ABAP example unless the user already provided one.
- If no example is available, say repository consistency will be weaker and proceed only with the harness rules.
- If a rule is not fixed, explain the repository pattern, suggest an alternative if useful, and ask before adding a rule.
- Repository consistency wins over cleaner rewrites unless the user asks otherwise.
- Prefer the closest functional shape: ALV, popup, editable ALV, splitter, tab strip, selection screen, module pool, Gateway, AMDP, ADBC, CDS.

## Hard Bans

- Do not modernize include split, naming, ALV flow, screen flow, or selection screen style for taste.
- Do not use `SELECT *`.
- Do not fetch broad data then filter in ABAP when SQL can filter.
- Do not use `FOR ALL ENTRIES` without checking the driver table.
- Do not put heavy SQL, DB changes, loops, or business logic in ALV event methods or PAI modules.
- Do not use `STARTING AT` for main screen `100` unless dialog-style display is required.
- Do not use `STOP` after a screen is displayed; use `EXIT` or screen flow.
- Do not leave an ALV screen or popup without freeing owned GUI resources.
- Do not add or remove `COMMIT WORK` or `ROLLBACK WORK` without transaction reasoning.
- Do not switch from Open SQL reports to CDS, AMDP, ADBC, Gateway, or class-heavy design unless required.

## File And Include Shape

- Use include split: `TOP`, `C01`, `O01`, `I01`, `F01`.
- `TOP`: globals, tables, types, macros, common variables, ALV declarations.
- `C01`: event classes. `O01`: PBO. `I01`: PAI. `F01`: FORM and business logic.
- Prefer TOP order: macros, `TABLES`, class instances, internal tables/work areas, ALV declarations, common variables, screen or popup helpers.
- Keep globals in `TOP` with grouped `DATA :` style.
- Local temporary variables may use inline syntax.
- Do not convert top-level globals into inline style.

## Naming

- Use repository prefixes: `go_`, `gs_`, `gt_`, `gv_`, `gc_`, `lr_`, `so_`, `pa_`.
- Use `pv_` for scalar FORM input parameters.
- If a role does not fit existing prefixes, explain why and ask before inventing a new prefix.
- Default ALV names: `go_container`, `go_alv_grid`, `go_pop_grid`, `go_event`.
- Default data names: `gs_body`, `gt_body`, `gs_layout`, `gs_variant`, `gs_fcat`, `gt_fcat`, `gv_okcode`, `gv_dynnr`.

## Data Shape

- Use DB/DDIC type directly only when it fits exactly; otherwise create a processed structure.
- Processed fields include icon, color, celltab, text, status, and edit helpers.
- Default display structure is `gs_body` and `gt_body`.
- Specific names are allowed when the role is clearer, such as `gt_main`, `gt_detail`, `gt_popup`, `gt_scarr`, `gt_mara`.

## ALV

- Build field catalog in `FORM set_field_catalog`.
- Single ALV uses global `gs_fcat` and `gt_fcat`; multiple ALVs share `gs_fcat` and use target catalogs like `gt_tfcat`, `gt_bfcat`, `gt_pfcat`.
- Main layout uses `gs_layout`; role-specific layouts may use `gs_pop_layout`, `gs_top_layout`, `gs_bottom_layout`.
- Variant uses `gs_variant-report = sy-repid`; set `handle` when multiple ALVs need separation.
- First display commonly passes `is_variant`, `i_save = 'A'`, `i_default = 'X'`, and `is_layout`.
- Event methods belong in `C01`, stay thin, and delegate to `PERFORM`.
- Editable ALV uses `celltab`, `gs_layout-stylefname`, edit-event registration, and FORM-based modify handling.
- Refresh in `FORM refresh_table`; pass stable row/col via `lvc_s_stbl`, commonly `VALUE #( col = abap_true row = abap_true )`.
- Use `PERFORM refresh_table` after add, delete, edit, mode toggle, or redisplay.
- For colors and icons, use processed fields and connect them through layout/field behavior.
- Icons must be real SAP icon table constants; do not invent fake `icon_*` names.
- Common valid examples include `icon_led_green`, `icon_led_red`, `icon_refresh`, `icon_insert_row`, `icon_delete_row`, `icon_toggle_display_change`, and `icon_xls`.
- If the right icon is unclear, ask which SAP icon to use.

## Popup, Splitter, Tab

- Popup trigger is flexible, but resources must be separated.
- Popup flow: prepare data, call popup screen, display content.
- Popup names commonly use `go_pop_grid`, `gt_pfcat`, `gs_playout`, `gs_pop_layout`.
- Popup helper names may use repository style such as `init_popup_process`, `init_pop1_process`, `pop_exit`, and `pop2_exit`.
- Multi-ALV names should be role-based: `go_top_grid`, `go_bottom_grid`, `go_left_grid`, `go_right_grid`; align data, field catalog, and layout names by role.
- Tab strip uses `gv_dynnr TYPE sy-dynnr` and normal `OKCODE` flow.

## Selection Screen And Ranges

- Follow repository selection-screen style: `PARAMETERS`, `SELECT-OPTIONS`, `RADIOBUTTON GROUP`, `MODIF ID`, `AT SELECTION-SCREEN`.
- Prefer `PARAMETERS` for mandatory single-value conditions.
- Use `SELECT-OPTIONS` for ranges, multiple values, include/exclude, or business flexibility.
- Use declaration restrictions first: `OBLIGATORY`, `NO INTERVALS`, `NO-EXTENSION`, `MODIF ID`; use `AT SELECTION-SCREEN` for business validation.
- Screen select-options use `so_`; internal ranges use `lr_`.
- Build range lines with explicit `sign`, `option`, `low`, `high`.

## SQL And DB

- List only required fields; prefer `INTO TABLE` for multi-row fetch.
- `INTO CORRESPONDING FIELDS OF TABLE` is allowed for processed structures.
- Default technology priority: `Open SQL`, `CDS`, `AMDP`, `ADBC`; query priority: `JOIN`, `FOR ALL ENTRIES`, `Subquery`.
- Check FAE driver table before query.
- DB retrieval FORM names use `get_`; ALV shaping FORM names use `set_`.
- DB changes follow validation, DB execution, result message handling.
- Transaction scope must be reasoned from the business flow and repository example.

## Module Pool And Screen

- Module pool main program includes `TOP`, `O01`, `I01`, `F01`.
- PBO belongs in `O01`; PAI in `I01`; reusable logic in `F01`.
- PBO uses `MODULE status_0100 OUTPUT`, `SET PF-STATUS`, `SET TITLEBAR`; PAI uses `MODULE user_command_0100 INPUT` and `CASE gv_okcode`.
- Main screen `100` usually uses `CALL SCREEN 100`.
- Popup may use `CALL SCREEN 110 STARTING AT 05 06`.
- Screen groups are main/popup/sub: `100/110/101`, `200/210/201`, `300/310/301`.
- Module suffixes may stay four-digit: `status_0100`, `user_command_0110`, `status_0101`.
- Align screen-specific status/title names when used, such as `MENU100/TITLE100`, `MENU110/TITLE110`, and `MENU210/TITLE210`.
- Close ALV screens and popups by freeing owned GUI resources, then `LEAVE TO SCREEN 0`.

## FORM And Flow

- Use parameterless `FORM ... .` for global-state routines; use `USING` for inputs and `CHANGING` for explicit write-back.
- Scalar input parameters commonly use `USING VALUE(pv_xxx)`; object or reference roles may use `po_` or `pr_` when meaningful.
- Do not introduce `TABLES` in new FORM routines by default.
- Use `TABLES ... STRUCTURE ...` only when preserving or extending classic repository FORM style.
- Check `sy-subrc` after result-sensitive operations.
- Use `CHECK`, `EXIT`, `RETURN`, `LEAVE TO SCREEN`, and `LEAVE LIST-PROCESSING` according to actual scope.
- Pair messages with the correct flow-control statement.

## UI, Messages, Comments

- Manage screen commands through global `gv_okcode TYPE sy-ucomm`; application toolbar uses `PF-STATUS`, `gv_okcode`, and `CASE gv_okcode`.
- Do not mix ALV toolbar and application toolbar.
- Prefer message classes over hardcoded text.
- Keep repository comment style: standalone `*--`, inline `"`.
- Macros stay in `TOP`, only for short repeated patterns such as init, range build, popup confirm, toolbar append, message text, date helper, or conversion wrapper.
- Do not hide business logic inside macros.

## Framework Navigation

- Start from this root `CLAUDE.md` for every ABAP task.
- Use `docs/INDEX.md` as the framework map when you need to find a focused reference.
- Use `docs/ARCHITECTURE.md` to understand how this harness is organized.
- Open topic rules only when the task needs them:
  - `docs/rules/BDC.md`
  - `docs/rules/CDS.md`
  - `docs/rules/AMDP.md`
  - `docs/rules/GATEWAY.md`
- Use operations notes only for maintenance or handoff:
  - `docs/operations/HANDOFF_NEXT_STEPS.md`
  - `docs/operations/FEEDBACK.md`
