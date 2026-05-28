# CORNERSTONE Open Platform — PDK Monitor

**Version 1.0.0 · 28 May 2026** *(rev. 6)*

A self-contained, single-file HTML dashboard for tracking
silicon-photonics Process Design Kit (PDK) content. Open
[`pdk-monitor.html`](./pdk-monitor.html) in any modern browser
(Chrome / Edge / Firefox / Safari) — no install, no server, no
build step. All state lives in the browser's IndexedDB and
`localStorage`; sharing happens through GitHub.

> **New in rev. 6** — **Sync mirrors GitHub** (pre-wipe before
> replay so files you deleted on the remote drop out locally;
> live file-count badge on each sync button; one-click prune
> for dead URLs; per-BB button simplified to `↻ Sync (N)` of
> working links). **Moveable + minimizable modals** — drag any
> modal by its header, click ─ to collapse it to a corner pill
> while you keep browsing the dashboard, click the pill to
> restore. **🔀 Move BB to another platform** — new BB Details
> footer button that splices a BB across Technology Platforms
> with every file, variant, history record and AI cache
> travelling along.
>
> **From rev. 5** — **AI Insight** end-to-end. A new gradient
> circle **AI Insight** button in the header opens a panel with
> Settings (endpoint, model, key, monthly token cap, egress
> modes, 🔌 Test connection, 👁 Reveal key) and shortcuts to
> per-platform briefs + dashboard executive summary. Every BB
> Details panel has a new **🧠 AI tab** that extracts cohort
> metrics in-browser, then asks the LLM to narrate — every
> numeric claim is citation-checked. A **💬 Chat with the
> agent** modal supports multi-turn conversation with six
> read-only tools (platforms, BBs, contributors, metrics,
> search, on-demand summaries) so you can ask "list every BB
> with no S-matrix data" or "which contributors added the
> most BBs to SOI?" and get a grounded answer. Contributors
> modal now has a **📥 Download Excel report** button (all
> modes) with five sheets of per-contributor statistics. New
> modern conic-gradient "AI" logo.
>
> **From rev. 4** — **Cross-check analysis** vs. another
> dashboard with three pie charts, multi-sheet Excel report, and
> selective per-BB zip download (all files in canonical
> folders). **Variants** are now a first-class category — header
> bar shows `🌿 N variants` + `M total`, cross-check splits
> w/o vs. with variants. **Admin safety gate** — three layers of
> defence block admin publishes into `users/` so contributor
> folders can never be wiped. **Admin-editable footer**, **Clear
> cache** in every role, **Prepare-for-PDK** opened to Admin,
> **scroll fixed** across every BB tab, **tab-named externalize
> subfolders** (layout/smatrix/tests/fab/eda/scripts/documents/
> files/variants), instant BB-card refresh after upload, and
> column-width tuning so the platform name stays visible at any
> width.
>
> Carry-over from rev. 3 — Layout section accepts GDS + OASIS
> (.oas / .oasis) as essential formats with SVG as preview
> fallback. Polished cross-user contribution flow: 🎁
> Prepare-for-PDK with per-section file multi-select + two
> publish paths (📤 PR with 1/hour cooldown, ⚡ Direct commit
> with progressive backoff). Editable Commit-tests-to-repo
> target URL. Scripts / S-matrix GitHub fetch accepts every
> file type. Custom Manage-list BB types resolve to their
> proper label everywhere. ⬇ Download on another user's file
> fetches the original bytes instead of a JSON sidecar.

## What's in this release

| File | What it is |
|---|---|
| `pdk-monitor.html` | The dashboard. Open this in your browser. |
| `pdk-monitor-v1_0_0.html` | Versioned copy of the HTML — upload this filename to a GitHub release page. |
| `dashboard-v1_0_0.json` | Canonical starter dashboard (default platforms / BB types / layers). Host alongside the HTML for fresh installs. |
| `PDK-Monitor-User-Manual.pdf` | Full user manual (printable PDF). |
| `PDK-Monitor-User-Manual.docx` | Same manual as a Word document. |
| `TERMS.md` | Terms of use. |
| `LICENSE-TAPR.txt` | TAPR Open Hardware License v1.0. |
| `CHANGELOG.md` | Per-version change log. |
| `RELEASE_NOTES.md` | Release-specific notes. |
| `README.md` | This file. |

## Quick start

1. **Save** `pdk-monitor.html` to disk (or to your team's GitHub
   release page as `pdk-monitor-v1_0_0.html`).
2. **Open** it in any modern browser. The dashboard boots into
   **Read-only** mode with the seeded factory defaults.
3. **Sign in.** Click the mode pill in the header. The default
   admin password is `CS2026_PDK_Design&Test` — change it from
   *Manage → Passwords* after the first sign-in.
4. **Pull a real PDK from GitHub.** Open a Technology Platform
   column, click *Edit*, paste the platform's GitHub folder URL
   (e.g.
   `https://github.com/cornerstone-uos/cornerstone-community/tree/main/Si_220nm_passive`),
   save, then click **⤓ Import platform**.

## Three roles

| Role | Capabilities |
|---|---|
| **Read-only** | View everything, run scripts, search & download |
| **User-admin** | All Read-only + add / edit / remove items in their own `users/<id>/` slice |
| **Admin** | Full control across the whole dashboard |

## Multi-user pool workflow

1. Admin sets up a Shared repository in Distribution.
2. Admin creates user-admin sign-ins in *Manage → User-admins*.
3. Admin clicks **🌱 Seed user folders from sign-in list** to
   create a starter `users/<id>/dashboard.json` for each one.
4. Each user-admin signs in, uploads their BBs / S-matrix /
   tests / scripts, clicks **⚡ Direct commit**. Their files
   land as real files under `users/<id>/<section>/<bb>/<filename>`.
5. Admin (or anyone) clicks **📥 Fetch all enabled sources** +
   **Merge into my dashboard** to assemble the full pool view
   with every contributor's real lab data integrated.

For one-click assembly: open *Multi-user pool sync (advanced)* in
the Fetch panel and click **📚 Fetch user repos**.

## Resolving name collisions

After pulling from multiple sources, click **🔀 Resolve duplicates
by name** in the Distribution panel. The resolver lists every
cluster (same-parent and cross-platform) with a checkbox; tick the
ones you want to action and use the toolbar buttons:

* **📥 Merge N selected** — fold the others into the keeper
* **🌿 Variants N selected** — attach the others as `subBlocks` of
  the keeper (cross-platform only; same-parent falls back to merge)
* **🚀 Auto-resolve all** — one click handles every cluster,
  including cross-platform

A Backup `.json` downloads before any change. Selection state
survives re-scans.

## Sharing this release on GitHub

1. Create a release on your GitHub repo (e.g. `v1.0.0`).
2. Attach the bundle `pdk-monitor-v1.0.0.zip`.
3. Optionally, attach the versioned standalone files for direct
   download:
   * `pdk-monitor-v1_0_0.html` — the dashboard itself
   * `dashboard-v1_0_0.json` — the canonical starter dashboard
   * `PDK-Monitor-User-Manual.pdf`
4. Point users to `pdk-monitor-v1_0_0.html` as the entry point.

## Licence

Hardware content distributed through the dashboard: **TAPR Open
Hardware License v1.0** (see `LICENSE-TAPR.txt`).

## Author

**Emre Kaplan** — a.emrekaplan@gmail.com

The portal is open to everyone for further development.
