# SAC Add-in for Excel: Formula Skill

A Claude skill for building, reviewing and debugging formulas in the **SAP Analytics Cloud
add-in for Microsoft Excel**. It covers all 16 `SAP.*` functions, reporting and planning
patterns, write-back, data action and multi action triggers, generating workbooks with code,
and troubleshooting.

It works for any industry and any model. Every example uses neutral placeholder IDs that you
swap for your own.

**[→ Browse the 20 interactive use cases](docs/use-cases.html)** (open locally, or enable GitHub Pages on `/docs`)

---

## What's inside

```
sac-excel-addin-formulas/          ← the skill (zip this folder to install)
├── SKILL.md                       router: modes, function selector, rules, core snippets
├── references/
│   ├── function-reference.md      all 16 functions, every option code, wrong names, AfO/EPM mapping, release notes
│   ├── patterns.md                16 reporting / filter / time / metadata patterns
│   ├── planning.md                SETDATA rules, process → publish, planning area, input forms,
│   │                              driver-based, allocations, bulk load, triggers, VBA, checklist
│   ├── report-architecture.md     intake questions, data source anchor + Control sheet, layout,
│   │                              column engines, sizing, getting real IDs, delivery modes
│   ├── workbook-engineering.md    binding generated workbooks, zip surgery, saved-state parsing,
│   │                              migration order, repair playbook, verification
│   └── troubleshooting.md         symptom → cause, debugging routine, SAP notes
└── scripts/
    ├── build_formula.py           build any SAP.* formula or a whole formula grid
    └── sac_xlsx_tools.py          inspect · profile-donor · bind · verify (.xlsx/.xlsm)
docs/use-cases.html                20 use cases with mini sheets, clickable formulas, copy buttons
tests/                             unit tests for both scripts
```

## Function coverage

| Area | Functions |
|---|---|
| Data | `GETDATA` · `SETDATA` · `GETDATACOMMENT` |
| Filters & time | `GETDIMENSIONFILTER` · `OVERWRITEDIMENSIONFILTER` · `ASYMMETRICFILTER` · `BLOCK` · `TIMEOFFSET` |
| Metadata | `GETTABLENAME` · `GETTABLESTATUS` · `GETDIMENSIONS` · `GETVARIABLES` · `MEMBERPROPERTY` |
| Planning triggers | `GETPLANNINGTRIGGERNAME` · `GETPLANNINGTRIGGERPARAMETERS` · `GETPLANNINGTRIGGERPARAMETERVALUE` |

## Install

**Claude apps (claude.ai / desktop):** zip the `sac-excel-addin-formulas/` folder, with
`SKILL.md` at the top level of the folder, and upload it as a custom skill in Claude's settings.
See Anthropic's documentation on custom skills.

**Claude Code:** copy the folder into your skills directory:
```bash
cp -r sac-excel-addin-formulas ~/.claude/skills/
```

The skill triggers on anything about the SAC Excel add-in, `SAP.GETDATA`, SAC write-back from
Excel, and so on.

## Use the scripts directly

Python 3.8+. `build_formula.py` and `sac_xlsx_tools.py` use only the standard library.

```bash
# one formula
python sac-excel-addin-formulas/scripts/build_formula.py GETDATA \
  --table DS_1 --pairs Account=REVENUE Date=202501 Version=public.Actual
# =SAP.GETDATA("DS_1","Account","REVENUE","Date","202501","Version","public.Actual")

# rolling 12-month header
python sac-excel-addin-formulas/scripts/build_formula.py TIMEOFFSET \
  --table DS_1 --ref '$B$5' --offsets=-11..0 --display 0 --orientation 1

# a whole guarded formula grid (tab-separated: cell, formula)
python sac-excel-addin-formulas/scripts/build_formula.py grid --table 'Ctl!$B$3' \
  --rows A5:A40 --cols C3:N3 --row-dim Account --col-dim Date --fixed Version=public.Actual --guard

# workbook tools
python sac-excel-addin-formulas/scripts/sac_xlsx_tools.py inspect connected.xlsx
python sac-excel-addin-formulas/scripts/sac_xlsx_tools.py bind generated.xlsx --donor donor.xlsx -o bound.xlsx --strip-iferror
python sac-excel-addin-formulas/scripts/sac_xlsx_tools.py verify bound.xlsx --expect-bound
```

Run the tests (`openpyxl` is needed only to create test workbooks):
```bash
pip install openpyxl
python -m unittest discover -s tests -v
```

## Ten rules that prevent most problems

1. **IDs, not descriptions**, for every dimension and member. A description returns blank or 0 without any error.
2. **Pin every dimension the number depends on.** Unpinned dimensions follow the data source filter.
3. **Refresh sheet functions** after changing a filter.
4. **Several tables in one string:** `"Table1, Table2"`.
5. **TIMEOFFSET** is `(Table, Reference_date, "offsets", …)`. It needs a time hierarchy and doesn't work on BW or S/4HANA sources.
6. **SETDATA:** leaf members only, unique combinations, then Process Data → Publish.
7. **No IFERROR around data cells.** Guard with `IF(input="",…)` instead.
8. **No volatile functions** near SAP formulas. Turn on *Optimize Data Retrieval* when there are many formula cells.
9. **`SAP.GETMEMBERS` and `SAP.GETMESSAGE` don't exist.** Check names against the function reference.
10. **Formulas generated by code must be bound.** Never re-save a connected workbook with openpyxl.

## Accuracy and versions

Signatures and option codes follow SAP's *Analyze and Plan with Formulas* guide (2026 edition)
and the add-in's *What's New* notes up to version 2026.19. The workbook-binding rules are
field-tested; the file format isn't officially documented. SAP ships the add-in every few
weeks, so if anything here disagrees with your add-in's function dialog
(**fx → SAP Analytics Cloud**), trust the dialog and please open an issue.

## Contributing

Issues and pull requests are welcome. Corrections are especially useful when they come with
the add-in version and a screenshot of the function dialog. Please keep examples
business-neutral: no customer names, real IDs or tenant URLs.

## Disclaimer

This is a community project. It is not affiliated with, sponsored by or endorsed by SAP SE.
SAP, SAP Analytics Cloud, SAP Datasphere and SAP S/4HANA are trademarks or registered
trademarks of SAP SE. Microsoft Excel is a trademark of Microsoft Corporation.

## License

[MIT](LICENSE)
