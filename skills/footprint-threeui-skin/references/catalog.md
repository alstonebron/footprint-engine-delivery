# Catalog Registration & Deployment

## Catalog files (workspace root, git-tracked)

- `catalog-v3.json` — source data: `{ brand, tagline, updated, templates[] }`.
  Each template: `{ id, name, category, url, description, tags[], live, status }`.
- `catalog-page-v3.html` — the rendered catalog page. Self-contained; template
  data is inline as `var DATA = {...}` (compact JSON, one line per entry,
  em-dashes as `\u2014`). Both files must stay in sync.

Live catalog URLs follow the pattern:
`https://dsh.growwithfootprint.com/cinematic/templates/<id>/index.html`
(the DSH server has a Caddy `/cinematic*` route serving `/home/work/sites/cinematic/`).

## Adding entries

Use a Node script (see `_add_threeui_catalog.js` in workspace root):

1. Read `catalog-v3.json`, append entries (skip if `id` already present),
   write back with `JSON.stringify(cat, null, 2) + '\n'`.
2. For the HTML page: insert new compact-JSON lines after the anchor entry.
   **Build each line with `JSON.stringify(entry)`** — hand-rolled string
   interpolation breaks on quotes inside descriptions. Verify afterwards:
   `h.match(/var DATA = (\{[\s\S]*?\});/)` → `JSON.parse` must succeed.

Category for ThreeUI re-skins: `Cinematic Templates`.
Tags: start with `threeui`, then `cinematic`/`three.js`/`webgl`/`template`
plus template-specific tags.

## Deployment

Templates deploy to the DSH server through the **footprint-engine-delivery**
GitHub repo (`alstonebron/footprint-engine-delivery`, branch `master`):

1. `git clone https://github.com/alstonebron/footprint-engine-delivery.git`
2. Copy template folders in (e.g. `cinematic/templates/<id>/index.html` +
   asset folders), plus updated `catalog-page-v3.html` / `catalog-v3.json`.
3. Commit + push. The DSH side syncs the repo into the served path.

Then verify each live URL returns 200:
`curl -o NUL -w "%{http_code}" https://dsh.growwithfootprint.com/cinematic/templates/<id>/index.html`

If the repo-to-server sync is not automatic, drive the DSH agent via the
`dsh` MCP (`harness_query`): ask it to pull the repo and rsync into
`/home/work/sites/cinematic/templates/`, then curl-verify. Per the dsh-usage
skill, ensure the harness is in Full Access mode first.
