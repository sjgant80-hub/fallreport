# FallReport

**Live:** [sjgant80-hub.github.io/fallreport](https://sjgant80-hub.github.io/fallreport/)

Sovereign single-file pivot table and dashboard tool. The Oracle Analytics / Hyperion / Tableau wedge of the TemuOracle suite.

One HTML file. No server. No build step. IndexedDB for persistence. Vanilla JS canvas charts — no library imports.

Prime 557. ◊·κ=1. MIT.

---

## For end users

FallReport turns rows of data into pivot tables, charts, KPI cards, and dashboards. Drop a CSV onto the window, drag fields into rows/columns/values, see numbers. Add a chart, save the dashboard, export to PNG or CSV.

### Quickstart

1. Open `index.html` in any modern browser.
2. Click **demo data** in the dataset sidebar — a pre-shaped SME ledger appears.
3. In **pivot mode**, drag `month` to rows, `region` to columns, `revenue` to values.
4. Switch to **dashboard mode**, click `+ bar`, bind it to the pivot.
5. Press **Ctrl+K** for the autopilot — type "p&l" or "top customers" and it builds the right view.

### Loading data

- **Drop a file** — CSV, TSV, or JSON anywhere on the window.
- **Paste CSV** — open the data picker on the right, paste a block.
- **Demo data** — three pre-shaped datasets (sales, customers, expenses).
- **From FallBase** — if FallBase is open in another tab on the same origin, FallReport can pull query results via the suite's postMessage bus.
- **From FallLedger** — same bus, asks for a named report (P&L, balance sheet, VAT).

### Dataset model

A dataset is just `{name, fields, rows}`. Fields are auto-typed as number, date, or string. Rows are plain objects keyed by field name. Everything is persisted to IndexedDB under your origin — nothing leaves the device unless you export.

### Pivot table

Drag chips between the four zones at the top of the pivot:

- **Rows** — categorical or date fields you want as row labels
- **Columns** — categorical fields broken out across the top
- **Values** — numeric fields with aggregation (sum, count, avg, min, max)
- **Filters** — global slicers (multi-select for categories, range for numbers/dates)

Click a cell to drill down — the contributing source rows appear in a panel.

### Charts

Five kinds: bar (vertical, horizontal, stacked), line, area, pie, scatter. Each chart binds to a dataset, an x-axis field, one or more y-axis fields with aggregation, and an optional group-by. All rendered to canvas — no library, no network calls.

### Dashboards

Compose charts and KPI cards on a free grid. Drag to move, drag the corner to resize. Save a dashboard by name; the dropdown in the header switches between them.

### KPI cards

Single big-number cards. Bind to a dataset and an aggregation. Optionally add a comparison ("this quarter vs last") and the card shows up/down arrow and % change.

### Templates (Ω palette)

Press Ctrl+K then type a template name:

- **p&l** — revenue / expenses / profit trend + margin KPI
- **sales** — bar chart of revenue by region
- **customers** — pivot of revenue per customer per quarter
- **burn** — line of cash with runway-in-months KPI
- **vat** — KPI of estimated VAT due this quarter

### Export

From the header **export** menu:

- **PDF** — sent to FallPDF (if installed) for proper rendering
- **PNG** — current dashboard / pivot rasterised
- **CSV** — pivot results as CSV
- **JSON** — full dashboard definition for portability

### Autopilot (Ctrl+K)

- T0 (offline) — keyword router fires named templates
- T3 (BYOK) — set an API key in Settings, then ask in natural language: "show me top 5 customers by revenue this year" and FallReport builds the pivot and chart.

---

## For developers

### Architecture

Single HTML file. All CSS and JS inline. Three layers:

1. **Data layer** — `state.datasets` is an array of `{id, name, fields, rows}`. Persisted to IndexedDB under `fallreport` / `datasets`. Dashboards live in `dashboards`.
2. **Pivot engine** — pure functions: `pivot(dataset, spec)` returns `{rowLabels, colLabels, cells, grandTotals}`. No DOM, just data.
3. **Chart engine** — `drawBar`, `drawLine`, `drawArea`, `drawPie`, `drawScatter`. Each takes a canvas 2D context and a `{series, opts}` object. Shared utilities: `niceTicks`, `colorPalette`, `tooltip`, `legend`.

### postMessage API

The suite bus runs on `BroadcastChannel('fall-signal')` plus window `message` events. FallReport responds to:

```js
{ target: 'fallreport', action: 'ping' }
// → { ok: true, prime: 557, version: '1.0.0' }

{ target: 'fallreport', action: 'addDataset', name: 'q1', rows: [...] }
// → { ok: true, id: '...' }

{ target: 'fallreport', action: 'export', kind: 'csv'|'png'|'json' }
// → { ok: true, blob: <Blob> }
```

FallReport also *calls* other tools:

```js
{ target: 'fallbase', action: 'query', sql: 'SELECT * FROM ledger' }
{ target: 'fallledger', action: 'getReport', name: 'P&L', period: 'this-quarter' }
{ target: 'fallpdf', action: 'render', markdown: '...' }
```

If a tool doesn't respond within 1.5s, FallReport falls back gracefully (manual paste, blank dashboard, in-tab PNG).

### Cascade tiers

- **T0** — fully offline. Pivot, charts, dashboards, templates, export all work with no network.
- **T2** — local Ollama on `127.0.0.1:11434` enhances autopilot.
- **T3** — BYOK (Anthropic, OpenAI, Gemini, OpenRouter). Used only for natural-language autopilot intent → JSON action.

### TemuOracle suite

FallReport is one of seven sovereign wedges:

- FallBase (Oracle DB · prime 541)
- FallLedger (NetSuite / SAP financials · prime 547)
- FallReport (Oracle Analytics / Hyperion · prime 557) ← you are here
- FallMage (procurement)
- FallPDF (document generation)
- FallOffice (spreadsheets / docs)
- FallBrief (legal)

Each replaces a TBAk-TBAk/year enterprise platform. Each is a single HTML file. Each persists locally. Together they cover the back office.

### Estate seal

`◊·κ=1` · prime 557 · MIT · sovereign tier · fall-signal channel

### Editing the file

`index.html` is the deliverable. There is no shell-and-build setup — edit directly, reload. Smoke test:

1. Drop `demo` into a fresh browser profile, dataset list shows three demos.
2. Pivot revenue by month × region, numbers match a hand-sum.
3. Add a bar chart bound to the pivot, axis ticks render.
4. Save dashboard, refresh, dashboard reloads from IDB.
5. Press Ctrl+K, type "p&l", template builds.

### File size

Target 90-150KB. Hard cap 400KB per the doctrine.

---

MIT · ◊·κ=1
