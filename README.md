# Karkain Analytics Studio

A CLI/TUI analytics tool for defining data models and measures, building reports and terminal dashboards, and sharing them on a schedule — built exclusively on Karkain Language v1.2.0.

## Status

| Area | Status |
|---|---|
| Research | COMPLETE |
| Product design | COMPLETE |
| Repository creation | COMPLETE |
| Implementation | NOT STARTED |
| Compile/execute verification of the application | NOT STARTED (toolchain itself VERIFIED on Karkain v1.2.0) |

## What it does

Karkain Analytics Studio turns cleaned local data into repeatable analytics. It lets a user define a semantic data model (dimensions and measures) over a CSV or embedded `db2` dataset, compute deterministic aggregations, assemble reports, render dashboards as text in a terminal, and produce scheduled report outputs that can be shared as files or via simple HTTP POST using Karkain's building blocks. It is a reporting and dashboarding tool, not a data exploration workspace — that role belongs to Karkain Data Studio.

## Who it serves

- Developers and ops engineers who want terminal/headless analytics without a BI server.
- Offline and edge users needing local reports and ASCII dashboards.
- Small teams wanting scheduled report outputs without per-seat BI licensing.
- CI workflows that generate and check deterministic metric reports.

## Planned features

All items below are **(planned)**; none is implemented.

- **Data models**
  - (planned) Define a semantic model: dimensions, measures and grain over a `db2`/CSV dataset.
  - (planned) Model versioning and validation against the source schema.
- **Measures and computation**
  - (planned) Sum, count, average, min/max and distinct-count measures using `numerics`.
  - (planned) Derived measures and deterministic, reproducible aggregation.
- **Reports**
  - (planned) Report definitions with filters, groupings and sort order.
  - (planned) Tabular and summary report rendering to text.
- **Dashboards**
  - (planned) Terminal dashboards composed of multiple report tiles (text charts/tables).
  - (planned) Refresh and snapshot of a dashboard state.
- **Scheduled sharing**
  - (planned) Scheduled report generation gated on a verified time capability.
  - (planned) Output to local files and simple HTTP POST via `http` building blocks.
- **Operations**
  - (planned) Config-file-driven command dispatch, run diagnostics and share audit log.

## Explicit non-goals

- Not a data exploration/transformation workspace; that is Karkain Data Studio.
- No no-code GUI dashboards; the GUI is BLOCKED in Karkain v1.2.0.
- No full JSON DOM, no external SQL connectivity or joins.
- No multi-user governance, SSO or enterprise semantic layer.
- No TLS, WebSocket, UDP or concurrent HTTP server framework.
- No per-seat BI cloud service.

## Karkain foundation

Exclusive foundation: Karkain Language v1.2.0. All application source will be Karkain. No other implementation language. No compiler modifications.

## Platform support status

- Windows x86-64 toolchain: VERIFIED.
- Linux cross-target output: PRESENT but execution NOT VERIFIED.
- macOS: NOT VERIFIED.

## Build and run status

No application binary exists yet. The intended generic commands are `karkain build src/main.kark -o karkain-analytics-studio` and `karkain run src/main.kark`. These become active when implementation starts; no compile or run of this application has been performed.

## Known blockers

- No native GUI toolkit (BLOCKED); dashboards are CLI/TUI, GUI is a planned blocked layer.
- `http` is building blocks only — no routing/middleware/concurrent server framework; scheduled sharing uses simple POST, gated on a verified time capability.
- `time`/`system`/`env`/`process` are PARTIAL shell wrappers; `get_args()` returns an empty array, so config-driven dispatch and time-gated scheduling are required.
- `json` is PARTIAL; no full DOM/streaming.
- No TLS/WebSocket/UDP; no external SQL drivers or joins.

## Repository

GitHub: https://github.com/ajit-ai/Karkain-Analytics-Studio
Issues: https://github.com/ajit-ai/Karkain-Analytics-Studio/issues
License: Apache-2.0.
