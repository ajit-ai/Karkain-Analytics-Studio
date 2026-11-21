# Competitor Analysis - Karkain Analytics Studio

Source: consolidated portfolio research _portfolio-foundation/research/domain2-data.md, section for Karkain Analytics Studio. All changing facts accessed 2026-11-21. Evidence labels: VERIFIED FACT / RESEARCH-BASED INTERPRETATION / PROPOSAL / UNVERIFIED ASSUMPTION. Competitor capabilities and prices are reproduced as researched, never invented.

## 4. Karkain Analytics Studio (BI / data visualization)

### Competitor positioning and intended users
**[VERIFIED]** Tableau (Salesforce) is an end-to-end BI platform with role-based Creator/Explorer/Viewer licensing for analysts and business users, plus Tableau Prep Builder and Server/Cloud. **[VERIFIED]** Microsoft Power BI spans Desktop authoring (free), Pro/Premium-per-user cloud sharing, Embedded and Report Server, deeply integrated with Microsoft 365 and Fabric. **[VERIFIED]** Apache Superset is an open-source "modern data exploration and visualization platform," enterprise-ready, lightweight and scalable, used at large organizations (per the ASF announcement). **[INTERPRETATION]** Tableau optimizes visual polish, Power BI optimizes ecosystem/cost, Superset optimizes openness and flexibility.

### Major capabilities/modules
**[VERIFIED]** Tableau: no-code visualization, dashboards, data prep and connectors with Enterprise governance tier. Power BI: Desktop report authoring, semantic models, dataflows/datamarts, advanced AI/Copilot, XMLA endpoint read/write, refresh rates 8/day (free) vs 48/day (Pro/PPU) and model memory limits of 1 GB (free) / 100 GB (PPU). Superset: 40+ pre-installed visualizations, drag-and-drop chart builder, SQL Lab with Jinja templating, physical/virtual datasets, semantic layer, caching, cross-filters/drill-to-detail, CSS theming, feature flags and a plug-in architecture.

### Platform support / deployment / packaging
**[INTERPRETATION/VERIFIED]** Tableau Desktop is Windows/macOS with Server on-prem or Cloud SaaS; Power BI Desktop is Windows-only with cloud service, Embedded and Windows Report Server; Superset is a browser-delivered web app self-hosted via Docker/Kubernetes (installation documented in its Administrator Guide). **[VERIFIED]** Superset explicitly leverages existing data infrastructure "without requiring yet another ingestion layer."

### Architecture, extensibility, integration
**[VERIFIED]** Superset connects to any SQL-based database, including cloud-native engines at petabyte scale, via its plug-in visualization model and REST API/extensions. Power BI integrates with Fabric OneLake and exposes XMLA; Tableau uses a connector ecosystem. **[INTERPRETATION]** All three are server/service architectures; none is a headless local analytics engine.

### Security/admin, performance, licensing
**[VERIFIED]** Power BI offers data security/encryption and role/permission licensing; Tableau Enterprise adds governance; Superset documents security configuration. **[VERIFIED]** Licensing (official pages only): Tableau Enterprise Creator $115, Explorer $70 and Viewer $35 per user/month billed annually; a $15 Viewer price is shown on the official pricing page. Tableau Standard Creator/Explorer list prices could not be confirmed on an official page: **UNKNOWN â€” not verified** (widely quoted third-party figures are deliberately excluded). Power BI Free; Pro $14/user/month; Premium Per User $24/user/month; Embedded and Fabric capacity variable. Superset is Apache 2.0 open source. **[INTERPRETATION]** Per-seat BI licensing creates cost pressure for large viewer populations.

### Strengths, weaknesses, gaps
**[VERIFIED/INTERPRETATION]** Tableau leads on visualization depth but is the most expensive; Power BI is cost-effective but Windows-Desktop-bound; Superset is free and flexible but requires self-hosting/operations. **[INTERPRETATION]** Gap: terminal/CLI analytics and zero-infrastructure local reporting for developers, ops and offline/edge contexts are underserved.

### Opportunities for Karkain differentiation â€” **[PROPOSAL]**
A CLI/TUI analytics tool over local CSV and `db2`: scripted reports, ASCII/terminal charts, deterministic aggregation, single native binary, no server or seat licenses. Position as a developer/ops complement to BI suites; disclose that full JSON DOM, GUI dashboards and external SQL connectivity are out of scope.

| Capability | Karkain Analytics Studio (proposed) | Tableau | Power BI | Superset |
|---|---|---|---|---|
| No-code GUI dashboards | Blocked; CLI/TUI | Strong | Strong | Strong |
| SQL IDE / semantic layer | `db2` subset | Connectors | DAX/semantic models | SQL Lab + semantic layer |
| Local embedded data source | Yes (`db2`, CSV) | Extracts | Desktop model | External DBs |
| Self-host open source | Yes (native binary) | No | Report Server only | Yes (Apache 2.0) |
| Terminal/headless analytics | Yes | No | No | No |
| Full JSON / nested data | No (partial) | Yes | Yes | Via DB |
| Multi-user governance | No | Enterprise | Enterprise | Yes |

**Sources (accessed 2026-11-21):** https://www.tableau.com/pricing ; https://www.tableau.com/product-and-pricing-selector ; https://powerbi.microsoft.com/en-us/pricing/ ; https://superset.apache.org/ ; https://preset.io/apache-superset/ ; https://news.apache.org/foundation/entry/the-apache-software-foundation-announces70
