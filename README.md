# GHG Standards Tracker

A weekly-updated, vendor-neutral reference for sustainability professionals tracking the simultaneous revision of the standards that govern corporate GHG accounting and disclosure:

- **GHG Protocol** — Corporate, Scope 2, Scope 3, AMI, Land Sector & Removals, Product, Project
- **ISO** — 14060-family harmonization and the GHGP–ISO consolidated corporate standard
- **SBTi** — Corporate Net-Zero Standard V2.0 and sector standards
- **ISSB / IFRS** — S1/S2, the December 2025 S2 amendments, and jurisdictional adoption
- **EU** — CSRD/Omnibus, revised ESRS, CBAM
- **US** — SEC climate rule rescission, California SB 253/261, state programs

## Structure

```
index.html        # the entire dashboard (single file, no build step, no dependencies)
data/data.json    # all content: frameworks, workstreams, updates, timeline, crosswalk
UPDATE_GUIDE.md   # how the weekly refresh works
VERIFICATION.md   # how the dataset is audited monthly against primary sources
```

## Data quality

Content is refreshed weekly and **re-audited monthly**: parallel AI research agents re-verify every load-bearing date, status, and figure against primary sources, findings are cross-checked and classified by severity, and verified corrections are applied to the live dataset. See [VERIFICATION.md](VERIFICATION.md) for the full process and its limitations. Spotted an error? Open an issue — flagged items get priority in the next audit.

The page loads its dataset from a live endpoint (`https://jhuaienvydgdnqwgwiau.supabase.co/functions/v1/ghg-data`), which is refreshed weekly — so content updates never require a redeploy. The bundled `data/data.json` is the fallback snapshot: the site uses it when the endpoint is unreachable, and the endpoint seeds itself from it if its store is ever empty. The snapshot is re-synced to the repo periodically.

## Run locally

```
python3 -m http.server 8000
# open http://localhost:8000
```

(Opening `index.html` directly as a file won't work because browsers block `fetch` on `file://`.)

## Deploy on Cloudflare Pages

1. Push this repo to GitHub.
2. In the Cloudflare dashboard: **Workers & Pages → Create → Pages → Connect to Git**, pick the repo.
3. Build settings: **Framework preset = None**, build command *empty*, output directory `/`.
4. Deploy. Every push to `main` auto-deploys, so a weekly commit to `data/data.json` republishes the site.

Each page has its own URL (`/updates`, `/frameworks`, `/whats-changing`, `/how-they-relate`, `/timeline`, `/method`, `/audit`). This relies on Cloudflare Pages' single-page-app fallback, which serves `index.html` for any unmatched path — **do not add a `404.html` to this repo**, or deep links will break.

## Data model (short version)

`data.json` top-level keys:

| key | what it holds |
|---|---|
| `meta` | `last_updated` (drives the header badge), disclaimer, cadence |
| `headline_metrics` | the KPI cards on the Overview tab |
| `frameworks[]` | one entry per framework; each has `workstreams[]` with `status_label`, `summary`, `key_points`, `confidence {level, reasoning}`, `next_milestone`, `sources` |
| `updates[]` | the dated feed; recency buckets (past week / past month / earlier) are computed client-side from `date` |
| `timeline` | lanes → `spans` (phase bands) and `milestones` (diamonds); `projected: true` renders hatched/hollow |
| `crosswalk` | relationships between frameworks + divergence watchlist |
| `confidence_legend` | definitions shown on the Method tab |

Confidence levels: `confirmed` / `high` / `medium` / `low` — every rating carries visible reasoning. See `UPDATE_GUIDE.md` for the rules used to assign them.

## Disclaimer

Independent and non-commercial. Not affiliated with any standard-setter or regulator. Not legal or compliance advice — verify deadlines against the primary sources linked throughout.
