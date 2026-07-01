# DNA Migration Tracker — Agent Context

This repo is a single-file static site (`index.html`) deployed on GitHub Pages. It tracks the migration of Lightdash dashboards from the legacy Analytics project to the new BeautyPie Intelligence project.

**Do not create additional files unless explicitly asked. All data and logic lives in `index.html`.**

---

## Updating dashboard/tile migration status

All data is in the `const DATA` object inside the `<script>` tag in `index.html`.

### Tile status values

```js
{ name: "Tile name", tab: "🇺🇸 US", migrated: true }       // migrated
{ name: "Tile name", tab: "🇬🇧 UK", migrated: false }      // pending
{ name: "Tile name", migrated: "wontdo", note: "reason" }  // deprecated
```

- `tab` is optional — used when a dashboard has multiple regional/named tabs
- `note` is optional on any tile — shown as a callout beneath the tile name

### Dashboard-level deprecation

Add `wontdo: true` (and optionally `note`) to the dashboard object:

```js
{
    name: "Dashboard Name",
    wontdo: true,
    note: "Replaced by BeautyPie Intelligence: Revenue Overview",
    tiles: [ ... ]
}
```

### Updating `lastUpdated`

Always update `DATA.lastUpdated` to the current date and time (ISO format `YYYY-MM-DDTHH:MM:SS`) when making any changes. Example: `"2026-06-30T09:41:00"`.

---

## Adding or updating data issues

Issues appear in the sticky sidebar on the right, and each has a dedicated detail page via hash routing (`#issue/0`, `#issue/1`, etc.).

Add entries to `DATA.dataIssues`:

```js
{
    severity: "high",           // "high" | "medium" | "low"
    title: "Short summary",     // shown in sidebar (keep concise)
    detail: "One-liner status", // shown beneath title in sidebar
    body: "Full explanation.\n\nCan be multi-paragraph, shown on the detail page.",
    affectedDashboards: ["Dashboard Name A", "Dashboard Name B"],  // optional
}
```

- `severity` controls the badge colour: `high` = red, `medium` = amber, `low` = blue
- `title` and `detail` are shown in the sidebar summary
- `body` is only shown on the detail page (use `\n\n` for paragraph breaks — rendered with `white-space: pre-wrap`)
- `affectedDashboards` is optional; omit if not relevant
- To resolve an issue, remove it from the array entirely

---

## Architecture

- **Single file:** `index.html` — HTML, CSS, and JS all in one file
- **No build step, no dependencies**
- **Routing:** Hash-based (`#issue/N`). The `route()` function shows/hides `#mainView` and `#issuePage` depending on the hash
- **Rendering:** The `render()` function reads `DATA` and builds the DOM. It is called once on load; `route()` is also called on load and on every `hashchange` event
- **Deployment:** GitHub Pages — push to `main` and the site updates automatically
