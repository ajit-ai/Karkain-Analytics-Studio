# Karkain Analytics Studio — Product Specification

**Document status:** Design COMPLETE. Research = COMPLETE. Repository creation = COMPLETE. Application implementation = NOT STARTED. Application compile/execute verification = NOT STARTED (toolchain path itself VERIFIED on Karkain v1.2.0).


## A. Product definition

**Product name:** Karkain Analytics Studio ("Analytics Studio").

**Purpose and problem.** BI suites (Tableau, Power BI, Superset) deliver rich visual dashboards but carry server deployments, per-seat licensing and GUI-first workflows that do not suit developers, ops teams, offline or edge contexts. There is a need for a headless, zero-infrastructure analytics layer over local data that defines measures once and produces deterministic reports and terminal dashboards, with scheduled sharing.

**Vision.** A single native binary in which a semantic model over local `db2`/CSV data drives reproducible measures, reports and text dashboards, with scheduled report outputs shared via simple file/HTTP mechanisms.

**Intended users / segments.** Developers and ops engineers wanting terminal analytics; offline/edge users needing local reports; small teams avoiding per-seat BI licenses; CI workflows that generate and verify metric reports.

**Usage scenarios.** (1) Define dimensions and measures over a cleaned dataset. (2) Run a grouped report and verify totals. (3) Render a terminal dashboard of several report tiles. (4) Generate a scheduled report and share it as a file or HTTP POST.

**Market positioning.** A terminal/headless analytics complement to BI suites — it is also explicitly distinct from Karkain Data Studio.

**Boundaries versus Karkain Data Studio.** Data Studio is for exploration and transformation: loading raw data, profiling, cleaning, deriving columns and visualizing distributions. Analytics Studio begins where that ends: it consumes a cleaned dataset and adds a semantic model (dimensions/measures), reusable report definitions, dashboard composition and scheduled sharing. Analytics Studio does not ingest raw files for cleaning or profile arbitrary columns; it assumes a prepared dataset. The two share the `db2` foundation but serve different stages of the workflow.

**Primary value proposition.** Define measures once, get deterministic reports and terminal dashboards, and schedule sharing — without a BI server or seat license.

**Explicit non-goals / boundaries.** No no-code GUI dashboards (BLOCKED); no full JSON DOM; no external SQL connectivity/joins; no multi-user governance/SSO/enterprise semantic layer; no TLS/WebSocket/UDP/concurrent HTTP server; not a data exploration tool. These follow Karkain v1.2.0 limits.

## B. Functional architecture

**Module inventory.**

| Module | Responsibility |
|---|---|
| AS-CLI | Config-driven command dispatch. |
| AS-MODEL | Semantic model definitions: dimensions, measures, grain, versioning. |
| AS-MEASURE | Deterministic aggregation via `numerics`/`db2`. |
| AS-REPORT | Report definitions: filters, groupings, sort, rendering. |
| AS-DASH | Terminal dashboards composed of report tiles. |
| AS-SCHED | Scheduled report generation (gated on verified time capability). |
| AS-SHARE | Output to files and simple HTTP POST via `http` building blocks. |
| AS-AUDIT | Share/run audit log. |
| AS-DIAG | Run status, metrics and diagnostics. |

**Core workflows.** Load model → compute measures → build report → render dashboard tile → (schedule) → share output → audit. Reports read a prepared dataset (CSV or `db2` table), never raw exploration state.

**Admin workflows.** Define/update models and reports in config; configure dashboards (tile layout), schedules and share targets; inspect run/share history.

**Error/recovery workflows.** Invalid model or report definitions are rejected before computation; a failed share is retried within a bounded limit and logged; a failed scheduled run leaves the previous report snapshot intact.

**Roles & permissions.** Single-user, process-local; file-system permissions are the only boundary. No multi-user authorization model.

**Configuration/customization.** Versioned config defines models, reports, dashboards, schedules, share targets and output formatting. No machine-specific paths in repository docs.

**Import/export.** Import: a prepared dataset (CSV or `db2` table) and model/report definitions. Export: rendered reports (text), dashboard snapshots, share artifacts (files / HTTP POST payloads).

**Integration requirements.** Invoked by CI as a compiled binary; reads local data via `io`/`db`; optional share via `http` building blocks; no server integration in MVP.

## C. Competitor analysis

Researched competitors are **Tableau**, **Microsoft Power BI** and **Apache Superset**, consistent with `docs/competitor-analysis/COMPETITORS.md` and `domain2-data.md`. Labels: **[VERIFIED]** official-page fact (accessed 2026-11-21), **[INTERPRETATION]** synthesis, **[PROPOSAL]** Karkain recommendation, **UNKNOWN — not verified**.

**[VERIFIED]** Tableau is an end-to-end BI platform with Creator/Explorer/Viewer roles; Power BI spans Desktop authoring and Pro/Premium cloud sharing with semantic models and DAX; Superset is an open-source data exploration and visualization platform with 40+ visualizations, SQL Lab and a semantic layer. **[INTERPRETATION]** Tableau optimizes visual polish, Power BI ecosystem/cost, Superset openness.

**Feature comparison matrix (Karkain = proposed).**

| Capability | Karkain Analytics Studio (proposed) | Tableau | Power BI | Superset |
|---|---|---|---|---|
| No-code GUI dashboards | Blocked; CLI/TUI | Strong | Strong | Strong |
| SQL IDE / semantic layer | `db2` subset + config model | Connectors | DAX/semantic models | SQL Lab + semantic layer |
| Local embedded data source | Yes (`db2`, prepared CSV) | Extracts | Desktop model | External DBs |
| Self-host / open source | Yes (native binary) | No | Report Server only | Yes (Apache 2.0) |
| Terminal/headless analytics | Yes | No | No | No |
| Full JSON / nested data | No (partial) | Yes | Yes | Via DB |
| Multi-user governance | No | Enterprise | Enterprise | Yes |

**Platform/deployment.** **[VERIFIED]** Tableau Desktop is Windows/macOS with Server/Cloud; Power BI Desktop is Windows-only with cloud/Embedded/Report Server; Superset is a browser web app self-hosted via Docker/Kubernetes. Karkain Analytics Studio: one native Windows binary (toolchain VERIFIED); Linux/macOS cross artifacts present but execution unverified.

**Licensing.** **[VERIFIED (official pages, accessed 2026-11-21)]** Tableau Enterprise Creator $115, Explorer $70, Viewer $35 per user/month billed annually (a $15 Viewer price is also shown); Tableau Standard list prices **UNKNOWN — not verified**. Power BI Free, Pro $14/user/month, Premium Per User $24/user/month; Embedded/Fabric variable. Superset is Apache 2.0. No price is invented. Karkain Analytics Studio is Apache-2.0.

**Strengths/limitations.** **[VERIFIED/INTERPRETATION]** Tableau leads on visual depth but is most expensive; Power BI is cost-effective but Windows-Desktop-bound; Superset is free/flexible but needs operations. Karkain offers headless local analytics but not visual richness or governance.

**Market gaps / differentiation.** **[INTERPRETATION]** Terminal/CLI analytics and zero-infrastructure local reporting are underserved. **[PROPOSAL]** Differentiate on measure-once determinism, text dashboards, single binary and scheduled sharing — complementarity to BI suites, and a layer above Data Studio.

**Features deliberately not copied.** No-code visual canvas, DAX, per-seat cloud licensing, enterprise governance, external SQL connectors, full JSON DOM.

## D. Technical architecture

**Application architecture.** AS-CLI dispatches commands; AS-MODEL defines the semantic layer; AS-MEASURE computes aggregations; AS-REPORT and AS-DASH render text output; AS-SCHED triggers generation; AS-SHARE distributes outputs; AS-AUDIT/AS-DIAG observe everything.

**Component boundaries.** Model definitions are separated from computation so measures are reusable; rendering is separated from computation so multiple report tiles can share results; sharing is isolated and testable without a server.

**Process/service boundaries.** Single process, no daemon in MVP. Cooperative `async` building blocks may sequence scheduled jobs, but sharing uses `http` building blocks only — there is no routing, middleware or concurrent HTTP server framework.

**UI architecture.** The UI is CLI/TUI/service-first because Karkain v1.2.0 has no native GUI toolkit (BLOCKED); dashboards are rendered as deterministic text (tables, bars, sparklines). Any graphical GUI is a planned, blocked layer contingent on ecosystem capability and is not claimed.

**Domain model.** Model, Dimension, Measure, Report, Dashboard, Tile, Snapshot, Schedule, ShareJob, AuditEntry — see section E.

**Data storage strategy.** Prepared datasets remain in CSV/`db2`; models/reports/dashboards are config definitions; snapshots and audit logs are local files. No BI server or metadata database service is used.

**API/interface architecture.** Internal Karkain module APIs; external interface is config files, CLI text output and optional HTTP POST. No REST API/API server in MVP.

**Event/message architecture.** No event bus; scheduled jobs are triggered sequentially and gated on a verified time capability.

**Logging/diagnostics/observability.** AS-AUDIT records each run/share with outcome; AS-DIAG emits run-level metrics; build-time lint/check used.

**Configuration management.** Versioned config for models, reports, dashboards, schedules and share targets.

**Plugin/extension architecture.** A measure-function registry and renderer registry keyed by name; extensions are Karkain modules, not new languages.

## E. Data design

**Main entities.** `Model` (name, version, grain, source); `Dimension` (name, type); `Measure` (name, expression); `Report` (model, filters, group-by, sort); `Dashboard` (tiles, layout); `Tile` (report, render type); `Snapshot` (report output, timestamp); `Schedule` (interval, target); `ShareJob` (snapshot, destination, status); `AuditEntry` (run/share, outcome).

**Relationships.** A Model owns Dimensions and Measures; a Report references a Model; a Dashboard owns Tiles that reference Reports; a Snapshot is produced by a Report; a Schedule produces Snapshots and ShareJobs; AuditEntries record runs/shares.

**Ownership and validation.** State is owned by one process. Validation: a Model's dimensions/measures are checked against the prepared dataset schema before computation; report filters/group-by must reference defined fields; share targets are validated before use.

**Lifecycle and persistence.** Models/reports are created and versioned; snapshots are immutable once written; schedules produce new snapshots; share jobs move queued→sent/failed; audit is append-only.

**Transactions, backup/restore.** Computation is read-only over the prepared dataset. A scheduled run writes a new snapshot atomically via file write then rename where available; on failure the previous snapshot remains. Because OS atomic-write is PARTIAL, snapshot integrity is validated by re-reading, not assumed. No database backup/restore is needed here (the `db2` dataset is managed by Data Studio/Database Studio).

**Migration.** No format predates v1.2.0; model/report configs carry version fields for future migration.

**Import/export formats.** Prepared CSV or `db2` dataset input; report output as delimited text and plain text tables; share payload as simple JSON-lines/text via HTTP POST (partial JSON only).

**Audit history.** Append-only audit log of report runs and share jobs, plus immutable snapshots, provide traceability.

**Storage decision.** A full analytics database is not warranted: prepared data already lives in CSV/`db2`, and definitions are configuration. The `db2` engine is used only if a measure needs grouped aggregation over a prepared table; otherwise `numerics` computes deterministic aggregates directly.

## F. Karkain implementation architecture

**Required Karkain capabilities / compiler.** Karkain v1.2.0 only; front-end lint and Go-engine C-free native Windows build verified. No compiler modifications.

**stdlib actually present (per capability register).** `numerics` (PRESENT: deterministic vector/matrix math for measures), `db` (PRESENT/PARTIAL: `db2` SQL subset, no joins/external drivers), `io` (PRESENT: file read/write/append), `http` (PRESENT: `http_post` and builders/parsers — building blocks only, no framework/concurrency/TLS), `encoding`/`crypto` (PRESENT), `collections` (PRESENT), `async` (PRESENT: cooperative, optional), `json` (PARTIAL: `json_get_field` only), `time`/`system`/`env`/`process` (PARTIAL shell wrappers).

**Required ecosystem components.** None beyond v1.2.0 stdlib and compiler; optional `karkain test`/`lint`/`ide info`.

**Missing dependencies.** GUI toolkit; full JSON DOM/streaming; portable time/scheduling API; TLS for secure sharing; concurrent HTTP framework; external SQL/joins.

**Native implementation opportunities.** Measure functions over `numerics`, report/group-by logic, text renderer, snapshot writer and simple share payloads can be pure Karkain.

**FFI/OS integration needs.** None required for MVP. Avoid PowerShell-backed `filesystem`/`time`; use `io`. No FFI is justified, so none is proposed.

**Build/packaging.** `karkain build src/main.kark -o karkain-analytics-studio` with the verified Go-engine/C-free native Windows target; single-binary distribution.

**Explicit blockers (recorded, not faked).** GUI BLOCKED → CLI/TUI dashboards. `http` building blocks only → no concurrent server/framework, sharing is simple POST with no TLS. `time` PARTIAL, `get_args()` empty, `system()` exit-code-only → config dispatch and scheduling gated on a verified time probe. JSON PARTIAL; external SQL/joins absent. No alternate language may be used as a workaround; capability additions belong to language/ecosystem work.

## G. Non-functional requirements

Targets use workload + measurement method (~4 GB RAM); PLANNED goals, not measured results.

| Area | Target (workload → method) |
|---|---|
| Performance | Aggregate 100k prepared rows into a 10-group report → time to rendered report, p50 of 5 runs. |
| Latency | Render a 6-tile text dashboard from cached measures → elapsed time. |
| Memory | 100k-row aggregation → peak RSS sampled from OS tool. |
| CPU | Aggregation workload → average CPU% over run. |
| Scalability | Row counts 1k/10k/100k → near-linear time growth recorded. |
| Reliability | Invalid model definition → rejected before compute with clear diagnostic; no partial output. |
| Security | Local processing; share targets validated; no TLS claimed (reviewed). |
| Privacy | No telemetry; share payloads only to configured targets (log inspection). |
| Portability | Windows build VERIFIED; Linux/macOS cross artifacts (execution unverified). |
| Maintainability | Each module lint-clean (`karkain lint`). |
| Accessibility | Deterministic text output, no color dependency, screen-reader friendly. |
| Observability | Every run/share emits AS-AUDIT + AS-DIAG records. |
| Recovery | Failed scheduled run leaves previous snapshot intact (fault injection). |

## H. Compatibility and portability

**Supported OS.** Windows toolchain VERIFIED; Linux and macOS NOT VERIFIED. **Architectures.** x86-64 verified; others unverified. **Karkain v1.2.0 compatibility matrix.** v1.2.0 only. **Binary/package formats.** Native Windows `.exe`; source + config distribution. **Install/uninstall.** Copy/remove binary and config; snapshots/audit are user data. **Config/data migration.** Versioned model/report configs; no pre-v1.2.0 compatibility required. **Upgrade/rollback.** Replace binary; definitions readable if version unchanged. **Cross-platform differences.** Avoid PARTIAL `time`/`filesystem` shell wrappers; use `io`. **Unsupported features.** GUI dashboards, full JSON DOM, external SQL/joins, TLS, multi-user governance, concurrent HTTP server.

## I. Testing strategy

All tests are **PLANNED**; none have been run for this application.

- **Unit:** measure functions, group-by, model validation, renderer → exact expected values on fixtures.
- **Integration:** model→measure→report→snapshot → expected report output.
- **E2E:** config-driven run → golden text output, snapshot and audit log.
- **Platform-specific:** Windows native execution; cross-target artifact creation.
- **Security:** share-target validation; no unexpected network calls.
- **Performance:** aggregation timings per section G recorded.
- **Regression:** golden reports/dashboards frozen after each milestone.
- **Compatibility:** lint/build with v1.2.0 only.
- **Actual-compiled-executable:** run built `.exe` for E2E.
- **Failure/recovery:** invalid model and failed share → rejection, bounded retry, snapshot preserved.

## J. Implementation roadmap

**MVP.** AS-CLI, AS-MODEL basic dimensions/measures, AS-MEASURE sum/count/avg, AS-REPORT simple table, AS-DIAG.
**Core functional release.** Derived measures, filters/group-by/sort, AS-DASH multi-tile terminal dashboard, AS-AUDIT.
**Advanced release.** AS-SHARE file/HTTP POST outputs, AS-SCHED scheduling gated on a verified time probe, snapshots.
**Production-readiness release.** Hardened validation/recovery tests, cross-target packaging, documentation.

**Feature dependencies.** Reports depend on models/measures; dashboards depend on reports; snapshots depend on reports; sharing depends on snapshots; scheduling depends on a verified time capability and sharing.

**Milestones/gates.** Gate 1: compiled binary computes a measure and renders a report. Gate 2: dashboards and audit. Gate 3: snapshot and share outputs. Gate 4: scheduling (only after time probe) and full test pass. No dates assigned.

**Risks/mitigations.** No GUI → CLI/TUI dashboards (accepted). HTTP building blocks/no TLS → simple, non-sensitive shares only (mitigated). No portable time → scheduling gated (mitigated). Partial JSON → flat payloads (mitigated). External SQL absent → prepared dataset scope (accepted).
