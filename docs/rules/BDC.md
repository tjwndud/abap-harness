# Claude BDC Rules

## Purpose

This document defines BDC and batch-input rules Claude must follow for this repository.

BDC work is a specific flow and should not make the main ABAP harness heavier.
When BDC is needed, Claude must follow this document together with the default `CLAUDE.md`.

## BDC Rule

- For BDC work, follow the repository pattern centered on global BDC tables in `TOP` and FORM-driven flow in `F01`.
- Preferred declarations:
  `gt_bdcdata TYPE TABLE OF bdcdata`
  `gs_bdcdata TYPE bdcdata`
  `gt_messtab TYPE TABLE OF bdcmsgcoll`
  `gs_messtab TYPE bdcmsgcoll`
  `gs_params  TYPE ctu_params`
- Keep BDC options in a dedicated parameter structure such as `gs_params`.
- Keep BDC mode in a common variable such as `gv_mode`.

## BDC Build Rule

- Repository examples build BDC steps through one helper FORM instead of appending `bdcdata` fields manually everywhere.
- Preferred helper shape:
  `FORM dynpro USING pv_begin pv_fnam pv_fval.`
- Use the helper FORM for both screen-begin rows and field rows.
- Repository style may call the helper with chained `PERFORM ... USING :` entries.

## BDC Execution Rule

- Repository style prefers `CALL TRANSACTION` with `USING gt_bdcdata`.
- Pass BDC runtime options through `OPTIONS FROM gs_params`.
- Collect runtime messages through `MESSAGES INTO gt_messtab`.
- Set `dismode` and `updmode` explicitly before execution.

## BDC Result Handling Rule

- After `CALL TRANSACTION`, inspect `gt_messtab` explicitly.
- If success is identified by a known message pattern in the repository example, read that message first and store the business result in a global variable if needed.
- If posting or processing fails, read the relevant message row and build a readable text with repository-style message handling.
- `MESSAGE_TEXT_BUILD` is allowed and preferred when the repository uses it to convert BDC message parts into readable text.
