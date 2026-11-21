# Karkain Analytics Studio — First Implementation Backlog

## First vertical slice

**Slice goal.** Prove the smallest real analytics workflow end to end: load a semantic model with one measure over a prepared dataset, compute a grouped report, and render a deterministic text report — no empty scaffolding.

**Karkain modules/files expected.**

- `src/main.kark` — config-driven command dispatch (AS-CLI).
- `src/model/loader.kark` — model/dimension/measure loader (AS-MODEL).
- `src/measure/agg.kark` — sum/count/avg via `numerics` (AS-MEASURE).
- `src/report/report.kark` — filter/group-by/report renderer (AS-REPORT).
- `src/diag/log.kark` — run record emission (AS-DIAG).
- `configs/analytics.karkcfg` — dataset path, model definition, report definition.

**Exact input.** A prepared `examples/sales.csv`:

```
region,amount
east,10
west,20
east,5
```

and config selecting dataset `examples/sales.csv`, model dimension `region`, measure `total=sum(amount)`, report group-by `region`, command `report`.

**Exact expected observable output** (stdout, deterministic):

```
model=sales measure=total
region|total
east|15
west|20
rows=2 status=ok
```

plus one AS-DIAG run record with command=`report`, groups=2, status=`ok`.

**Verification command pattern (Karkain v1.2.0).** From the repository root, with the verified toolchain configuration applied:

```
karkain lint src/main.kark
karkain run src/main.kark --target native-x86_64-windows
karkain build src/main.kark -o karkain-analytics-studio --target native-x86_64-windows
.\karkain-analytics-studio
```

The built executable's stdout must match the golden output above byte-for-byte.

## Backlog items

| ID | Feature | Files/modules | Dependencies | Acceptance criteria | Tests | Complexity | Blocking Karkain capability | Definition of done |
|---|---|---|---|---|---|---|---|---|
| AS-001 | Config-driven CLI dispatch | `src/main.kark`, `configs/analytics.karkcfg` | none | Loads config, selects `report`, ignores empty `get_args()` | Unit, E2E | M | `get_args()` empty; `system()` shell wrapper | Command runs from config |
| AS-002 | Model definition loader | `src/model/loader.kark` | AS-001 | Parses dimensions/measures; validates vs schema | Unit | M | no config-schema stdlib | Fixture model loads/validates |
| AS-003 | Prepared dataset reader | `src/data/reader.kark` | AS-002 | Reads CSV/`db2` dataset rows via `io`/`db` | Unit, integration | M | `db2` subset, no joins/external drivers | Rows match fixture |
| AS-004 | Basic measures | `src/measure/agg.kark` | AS-003 | sum/count/avg exact via `numerics` | Unit | M | float builtins byte-unreliable → use `numerics` | Measure values match hand calc |
| AS-005 | Grouped report | `src/report/report.kark` | AS-004 | Filter/group-by/sort produce correct rows | Unit, integration | L | none | Golden report matches |
| AS-006 | Derived measures | `src/measure/derived.kark` | AS-004 | Derived expressions computed deterministically | Unit | M | none | Derived values correct |
| AS-007 | Text renderer | `src/report/render.kark` | AS-005 | Tables/bars as deterministic text | Unit, E2E | M | GUI BLOCKED | Rendered output matches golden |
| AS-008 | Terminal dashboard | `src/dash/dash.kark` | AS-005, AS-007 | Multiple tiles composed in config layout | Integration, E2E | L | GUI BLOCKED | Dashboard output deterministic |
| AS-009 | Snapshot output | `src/snapshot/snapshot.kark` | AS-005 | Immutable report snapshot written+verified | Integration | M | atomic write PARTIAL | Re-read snapshot matches |
| AS-010 | Share to file / HTTP POST | `src/share/share.kark` | AS-009 | Writes file and simple `http_post` payload | Integration | L | `http` building blocks only, no TLS | Share target receives expected payload |
| AS-011 | Share audit log | `src/audit/log.kark` | AS-010 | Each run/share recorded with outcome | Observability, E2E | M | none | Audit records complete |
| AS-012 | Scheduling probe | `src/sched/tick.kark` | AS-001 | Verified time source drives scheduled run | Platform, integration | L | `time`/`system` PARTIAL, `get_args()` empty | Probe result recorded; gated |
| AS-013 | Actual executable E2E | all | AS-001..AS-011 | Built `.exe` reproduces all goldens | Actual-compiled-executable | M | Go-engine/C-free target required | Binary E2E pass |

## Dependency order and gates

1. AS-001 → AS-002 → AS-003 → AS-004 → AS-005 (Gate 1: vertical slice renders report).
2. AS-006, AS-007 (Gate 2: derived measures and text rendering).
3. AS-008, AS-009 (Gate 3: dashboard and snapshots).
4. AS-010, AS-011 (Gate 4: share outputs and audit).
5. AS-012 (Gate 5: scheduling only after time probe passes).
6. AS-013 (Gate 6: binary verification and packaging).

## Status

All items: **NOT STARTED**. Implementation must not begin until all designs and repository initialization are complete. No tests have been run for this application.
