# sim-to-kg
# sim-to-kg

**Turn process simulations and CAD assemblies into a knowledge graph, and cross-check them against P&IDs.**

`sim-to-kg` is a [Claude skill](https://code.claude.com/docs/en/skills) and a sibling of [`pid-to-kg`](https://github.com/ScottDuncanAI/PID-to-Knowledge-Graph). Where `pid-to-kg` reads drawings with vision, this skill reads **structured sources** (simulation and CAD) and reconciles them with each other by equipment tag.

| Source | Extractor | Requires |
|---|---|---|
| Aspen HYSYS | `extract_hysys.py` (COM) | Windows, HYSYS licence, `pywin32` |
| Aspen Plus | `extract_aspen_plus_inp.py` (exported `.inp`) | nothing |
| SolidWorks | `extract_solidworks.py` (API/COM) | Windows, SolidWorks, `pywin32` |
| Any CAD | `extract_step.py` (STEP AP214/203/242) | nothing |
| P&ID graph | `import_pid_kg.py` (from pid-to-kg) | its `graph.json` |

Output (`build_kg.py`): `summary.md`, `qa_report.md`, `graph.json`, and `graph.duckdb` if `duckdb` is installed. The QA report lists equipment missing from some sources, tag mismatches, and same-tag equipment whose downstream neighbours differ between sources.

## Status: read this first

- The Aspen Plus `.inp`, STEP, importer, merge and QA code is covered by a synthetic end-to-end test (`python tests/test_pipeline.py`).
- **The HYSYS and SolidWorks COM extractors have not been run against a live installation.** They follow the vendors' object models, guard every read, and record failures in `props._notes`. Expect to adjust attribute names for your version (`extract_hysys.py --discover` helps).
- The pid-to-kg importer guesses that project's JSON schema; adjust `KIND_MAP` if counts look wrong.
- Output is machine-extracted. A qualified engineer must review it before use in design, MOC, HAZOP or LOPA.

## Install

1. Copy `skills/sim-to-kg` into your Claude skills folder: `%USERPROFILE%\.claude\skills\` (Windows) or `~/.claude/skills/` (macOS/Linux).
2. `pip install pywin32 duckdb` (pywin32 only for the COM extractors).
3. In Claude Code, open your project folder and ask, e.g. *"Build a knowledge graph from plant.inp and skid.step and check them against pid_graph.json."*

## Quick try (no simulator or CAD needed)

```
python tests/test_pipeline.py
python skills/sim-to-kg/scripts/extract_aspen_plus_inp.py examples/demo.inp ap.json
python skills/sim-to-kg/scripts/extract_step.py examples/demo.step cad.json
python skills/sim-to-kg/scripts/import_pid_kg.py examples/demo_pid_graph.json pid.json
python skills/sim-to-kg/scripts/build_kg.py out pid.json ap.json cad.json
```

## Limitations

HYSYS energy streams, Adjust/Recycle/spreadsheet logic, and SolidWorks Routing pipe runs are not extracted yet. See `skills/sim-to-kg/references/limitations.md`.

## License

MIT.

[ÙØ§Ø±Ø³Û](README.md)
