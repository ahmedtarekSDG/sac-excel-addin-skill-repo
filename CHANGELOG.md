# Changelog

## 2.0.0 (2026-11)

A full review against SAP's current formula guide, rebuilt to be business- and model-neutral.

### Fixed (breaking: these signatures were wrong in 1.x)
- `SAP.TIMEOFFSET` is `(Table, Reference_date, "offsets", [Display], [Orientation], [Dimension])`.
  It spills a list. It is not `(DS, Dim, Member, Offset)`.
- `SAP.OVERWRITEDIMENSIONFILTER`: several tables go in **one string** (`"Table1, Table2"`) or a
  range. Added `Selection_type` (0–5), `Member_display` (0–4) and `Separator`.
- `SAP.ASYMMETRICFILTER` is `(Tables, Block1, …)`, and `SAP.BLOCK` is `(Dim, Members, … ≤5 pairs)`
  with no table argument.
- `SAP.GETDATACOMMENT`: `Display` is the 2nd argument.
- `SAP.GETDIMENSIONFILTER`, `GETDIMENSIONS`, `GETTABLESTATUS`, `GETVARIABLES`, `MEMBERPROPERTY` and
  the trigger functions now have their full optional arguments and codes.
- Removed `SAP.GETMEMBERS` and `SAP.GETMESSAGE`. They are not add-in functions. Alternatives are documented.
- Unspecified dimensions in GETDATA/SETDATA **do** take the data source's filter, but only after
  the formulas are refreshed. The advice to pin dimensions explicitly is kept.

### Added
- `planning.md`: the full write-back workflow, planning area extension, mixed-input known issue,
  input-form, driver, spread and bulk-load patterns, data and multi action triggers, VBA, and a checklist.
- `report-architecture.md`: intake, data source anchor (formula data sources need no table),
  Control sheet layout, column engines, neutral formatting conventions, sizing, delivery modes.
- `workbook-engineering.md`: the binding playbook for generated workbooks (donor cloning,
  cell forms, zip surgery, three-step migration order, repairs, verification).
- `troubleshooting.md`: symptom → cause decoder, debugging routine, SAP notes.
- `scripts/sac_xlsx_tools.py`: `inspect`, `profile-donor`, `bind`, `verify`.
- `scripts/build_formula.py`: rewritten for all 16 functions; `grid` subcommand; `--sep ";"`;
  rule for telling member IDs from cell references in ambiguous cases.
- `docs/use-cases.html`: 20 interactive use cases plus a function reference.
- Release notes up to 2026.19: Optimize Data Retrieval, offline mode, multi action triggers,
  renameable formula data sources.

## 1.x (2026-06)
- First version: function reference, patterns, formula builder.
