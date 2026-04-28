Review the ABAP change against the harness framework.

Read first:

- `/CLAUDE.md`
- `/docs/INDEX.md`

Then load the relevant topic rule only if the changed code touches that area:

- `/docs/rules/BDC.md`
- `/docs/rules/CDS.md`
- `/docs/rules/AMDP.md`
- `/docs/rules/GATEWAY.md`

Checklist:

| Item | Result | Notes |
| --- | --- | --- |
| Repository example followed |  |  |
| Include and FORM shape preserved |  |  |
| SQL uses explicit fields and safe driver checks |  |  |
| ALV or screen flow keeps logic out of event/PAI methods |  |  |
| Topic-specific rule loaded when needed |  |  |
| Transaction and message flow are justified |  |  |

List concrete issues first with file and line references.
