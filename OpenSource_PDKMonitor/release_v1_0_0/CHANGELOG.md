# Changelog — CORNERSTONE Open Platform PDK Monitor

All notable changes to this project will be documented in this file. The
format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and the project adheres to [Semantic Versioning](https://semver.org/).

## [1.0.0] — 2026-05-28 *(rev. 6 — sync fidelity, moveable modals, cross-platform BB move)*

Same v1.0.0 stamp. Rev. 6 makes the dashboard noticeably more
fluid in day-to-day use. The sync flow now mirrors GitHub
reality (files deleted on the remote drop out locally, and the
button label shows the working-link count); every modal is now
moveable + minimizable so long-running ops can be tucked away
while you keep browsing; and a new 🔀 Move to platform action
lets you reorganise BBs across Technology Platforms without
manually re-creating them.

### Added (rev. 6)

- **🔀 Move BB to another Technology Platform.** New button on
  the BB Details panel footer (between ↻ Sync and Edit) opens
  a modal listing every other platform on the dashboard. Pick
  the destination, confirm, and the BB is spliced out of its
  current group's `entities[]` into the target group's. Every
  file (GDS / OASIS, S-matrix, tests, fab, scripts, documents,
  EDA links, variants, fetch history, last-sync stats,
  ownership, AI cache) travels with the entity because it all
  lives on the entity reference itself. The visible tab
  switches to the destination and the BB Details panel
  re-opens on the moved BB so you keep your place.
- **Moveable + minimizable modals.** Every modal (Sync,
  Cross-check, AI Insight, Chat, Prepare-for-PDK, Manage,
  Contributors, Distribution, BB edit, every fetch) now has a
  drag handle (⠿ grip + the whole header bar) and a ─
  minimize button. Minimized modals collapse to a 360 × 48 px
  pill at the bottom-right with the title and an accent-coloured
  border; clicking the pill restores the previous size +
  position. The dashboard behind stays fully interactive while
  a modal is open or minimized, so long syncs / AI calls /
  fetches no longer block browsing.
- **Sync pre-wipe + live file count + dead-URL pruning.** The
  sync flow now:
  - Runs a pre-wipe that clears every folder of each
    `(BB, section)` pair touched by the jobs, so files deleted
    on the remote drop out locally instead of lingering in
    stale folders.
  - Replays each URL in append mode (no `replace: true`) so
    multiple URLs for the same section correctly UNION their
    results.
  - Tracks per-job delta (`filesFound` vs. `urlsEmpty` from a
    before/after section count) and persists
    `{ ranAt, urlsTotal, filesFound, urlsEmpty, deadUrls[] }`
    on the relevant scope (`e.lastSyncStats` per BB,
    `g.lastSyncStats` per platform, `MODEL.lastGlobalSyncStats`
    for the whole dashboard).
  - Each sync button label re-renders from the saved stats —
    platform group head reads `↻ Sync · X files · Y removed`
    (Y in red if > 0), header reads `↻ Sync all · X files · Y
    removed`. The per-BB button is the compact form
    `↻ Sync (N)` where N is the number of working URLs
    (`urlsTotal − urlsEmpty`).
  - If any URL came back empty, a **🗑 Remove N dead URLs from
    history** button appears in the sync modal footer to drop
    those URLs from `fetchHistory` in one click.
- **Per-job log lines** in the sync log now show the
  contribution: `+5 files from this URL` or
  `⚠ 0 files — this URL is empty / removed on the remote`.

### Fixed (rev. 6)

- **Stale local files after files were deleted on the GitHub
  remote.** The old sync used `runSectionGithubFetch(...,
  {replace:true})`, which only wiped the canonical default
  folder (`sg_files` / `tg_files` / `ft_files`). Files
  previously placed in differently-named folders by the legacy
  `runGithubFetch` path stayed visible. The new pre-wipe (see
  Added) clears every folder of the section before each
  replay.
- **Multi-URL clobbering inside one section.** With
  `replace:true`, each URL replay re-wiped the default folder
  and refilled it — last URL won, intermediate fetches were
  lost. The pre-wipe + append-mode loop produces the correct
  UNION of every URL's current contents.
- **Sync button stuck at the URL count.** The button now
  reflects the number of *working* links after the last sync
  rather than the raw `fetchHistory.length`.

### Notes (rev. 6)

- The modal chrome change is purely additive — closing /
  reopening a modal resets it to centred so nothing inherits
  the previous position.
- Move BB is local-only: files already published to
  `users/<id>/` on GitHub stay put until you next publish
  from the new platform. The post-move "heads-up" panel
  spells this out.

---

## [1.0.0] — 2026-05-27 *(rev. 5 — AI Insight: per-BB summaries, chat agent with tool use, contributor Excel export)*

Same v1.0.0 stamp. Rev. 5 adds an end-to-end AI synthesis layer
on top of the existing dashboard. The implementation is hybrid:
deterministic metric extraction in the browser, then an LLM
(configurable — Anthropic Messages, OpenAI chat completions, or
any OpenAI-compatible gateway) only narrates the numbers, with
every quoted value cross-checked against a citation map. The
release also ships a conversational chat agent with read-only
tool use, a contributor Excel report, and a modern colour-ring
"AI" logo on every entry-point.

### Added (rev. 5)

- **🧠 AI Insight button in the header** (renamed in-button text
  to "Insight" with the new gradient logo) opens the main AI
  panel. Status / usage / per-platform shortcuts in one view.
- **AI Settings modal** — endpoint URL, model name, API key,
  monthly token cap with live usage bar, egress mode
  (metrics-only / metrics+extracts / full), prompt style
  (brief / balanced / deep). All settings persist in
  `localStorage` under `pdk-monitor.ai.settings`, never published.
- **🔌 Test connection button** in AI Settings — fires a minimal
  call against the form's current values (not saved values) and
  reports back with HTTP code, response time, token usage,
  auto-detected API family, and targeted hints for 401 / 403 /
  404 / 429 / 5xx / CORS failures. Collapsible response-body
  panel for raw debugging.
- **👁 Reveal API key / 📋 Copy** buttons next to the key field.
  A live meta line shows length, prefix-detected family
  (Anthropic `sk-ant-…`, OpenAI `sk-proj-…` / `sk-…`, OpenRouter
  `sk-or-…`, Groq `gsk_…`), and a head…tail preview. Two
  guard warnings fire automatically: leading/trailing whitespace
  detection, and endpoint/key family mismatch.
- **Per-BB 🧠 AI tab** on every BB Details panel:
  - **Deterministic extractor** (`extractMetricsForBB`) walks
    `allDatasets` + `testGroups` for the BB, decides per-trace
    whether values are linear |S| or already in dB, and emits
    cohort statistics (mean ± σ ± n, min/max, peak position,
    1-dB bandwidth around peak) per S-parameter name.
  - **Derived headline metrics** — S21 peak, S31 peak, |S21−S31|
    imbalance, S21 1-dB bandwidth, S11/S22 reflections — each
    citation-tagged.
  - **File inventory chip row** — counts of S-matrix / tests /
    scripts / docs / fab / variants + a layout-present flag.
  - **🧠 Summarise this BB button** — hands metrics + citation map
    to the LLM, renders the response with markdown subset and
    **citation discipline**: every numeric token is matched
    against the citation map at 1-/2-/3-decimal places, and
    mismatches get a wavy red underline ("number not found in
    the citation map — may be hallucinated").
  - **📜 Citation map** drawer lists every metric the LLM was
    allowed to quote with source + scope + unit.
  - **📋 Copy / ⬇ Download .md / 🗑 Clear cached summary** actions.
- **Content-hash caching** — SHA-256 of `(metrics JSON + model +
  prompt style)` keys each BB's cached summary in `localStorage`.
  Re-clicking 🧠 on unchanged data costs zero tokens. Cache
  entry stores model + endpoint + egress + tokens + created_at +
  created_by + extractor version (provenance).
- **Cost meter** — Anthropic + OpenAI usage objects parsed
  after each call; per-month input + output token counts
  surface in the AI panel and Settings, with a progress bar
  and a hard cap that disables all 🧠 buttons when reached.
- **Platform-level executive summary** — 📋 Brief button per
  platform; iterates that platform's BBs, takes the
  one-paragraph headline from each cached summary (computes
  missing ones), composes a Markdown brief, downloads as
  `<platform>-executive-summary.md`.
- **Dashboard-level executive summary** — 📋 Executive summary
  (whole dashboard) in the AI main panel; roll-up of every
  platform's brief.
- **💬 Chat agent — multi-turn with read-only tool use.** Opens
  from the AI panel. The agent has six tools that walk MODEL:
  - `dashboard_overview` — platforms / BB / variant / contributor
    / file counts.
  - `list_platforms` — every platform with material + thickness
    + GitHub URL.
  - `list_bbs(platform?, bb_type?, status?, owner?, search?)` —
    filtered listing with file-inventory snapshots.
  - `get_bb_details(name, platform?)` — full metric block for
    one BB (same shape as the 🧠 tab).
  - `search_dashboard(query, limit?)` — free-text search across
    names, descriptions, owners, parameters.
  - `get_contributor_stats(contributor?)` — per-contributor
    activity table sorted by total contributions.
  - `summarise_bb(name, platform?, force?)` — runs (or returns
    cached) AI summary for one BB.
  Conversation history persists per browser
  (`pdk-monitor.ai.chat.history`). Every tool call + result is
  shown inline in the chat. Anthropic uses native tool-use
  protocol; OpenAI-compatible endpoints use OpenAI's `tools`
  schema (both message shapes translated in both directions
  so providers can be switched without clearing history).
  Hard cap of 6 tool-use iterations per turn to avoid runaway
  loops. Suggested-prompt chips at the bottom of the chat for
  quick start.
- **Cmd/Ctrl+Enter sends** in the chat input (Enter inserts a
  newline so multi-line prompts work).
- **⬇ Export .md** of the chat transcript including every tool
  call and input.
- **📥 Download Excel report (Contributors modal)** — always-visible
  button in the footer (all modes). Generates
  `contributors-statistics-YYYY-MM-DD.xlsx` with five sheets:
  Summary (per contributor: BBs / variants / platforms touched /
  file breakdown / first + last activity), Per-platform pivot
  matrix (rows × columns × counts), BBs (every BB with owner +
  type + status + last-updated), Files (flat list of every
  uploaded file with size + section + owner), About metadata.
  Walks `subBlocks` so variants are attributed correctly.
- **Modern AI logo** — a conic-gradient circle (purple → pink →
  orange → cyan → purple) with white "AI" lettering inside.
  Ring rotates once every 8 s; soft 3.6 s purple-to-cyan glow
  pulses behind it. Three sizes (sm 18 px / md 22 px /
  lg 30 px). Replaces the 🧠 emoji on the header button (which
  now reads "AI Insight" with a violet-to-cyan translucent
  background) and the per-BB AI tab.

### Fixed (rev. 5)

- **Tooltip text** on the AI buttons drops the leading 🧠 emoji
  so it reads cleanly in OS tooltip boxes.

### Notes (rev. 5)

- **API key safety.** Keys live in `localStorage` on the user's
  machine only; they are stripped from every publish payload
  by the existing binary-purge step.
- **CORS caveat.** Anthropic's Messages API requires the
  `anthropic-dangerous-direct-browser-access: true` header
  (the dashboard already sends this). Some accounts / regions
  still block direct browser calls — the Settings modal's
  notes panel calls this out and suggests fallbacks
  (OpenRouter, Ollama, a small Cloudflare Worker proxy).
- **Citation discipline is not omniscient.** It catches the
  common case (LLM quotes a number not in the citation map),
  but a number that matches by coincidence at 1-decimal
  precision will still pass. Provenance + the visible
  citation drawer give you the audit trail.

---

## [1.0.0] — 2026-05-27 *(rev. 4 — admin safety, cross-check analysis, variants as first-class, scroll & footer polish)*

Same v1.0.0 stamp. Rev. 4 builds on rev. 3 with a hard safety
boundary against admin publishes wiping contributor folders, a full
cross-dashboard cross-check analysis suite with three pie charts and
selective Excel/zip export, variants surfaced as their own category
everywhere (UI, analysis, Excel), an admin-editable footer, a
working file-list scroll on every BB tab, lazy-hydrated cross-user
downloads with real bytes + correct MIME, broader fetch coverage
for Scripts and S-matrix, a label resolver for custom BB types, the
Prepare-for-PDK flow opened to Admin, and several layout / re-size
fixes so platform names stay visible at any column width.

### Added (rev. 4)

- **CRITICAL admin safety gate against publishing into `users/`.**
  Three layers of defence stop an admin's Direct commit from
  pruning every contributor's files:
  1. The `confirmedDirectCommit` flow hard-blocks any admin push
     whose normalised path matches `^users(/|$)` — toast + alert +
     log entry, no API calls fired.
  2. Externalize fires only when the path matches
     `^users/[^/]+/dashboard\.json$` — i.e. a strict per-user
     slice. The root `users/dashboard.json` no longer triggers
     externalize.
  3. `pruneOrphanRepoFiles` refuses any `usersDir` value that
     isn't `users/<exactly-one-segment>` and returns a no-op
     summary, so even if a caller mis-routes, the contents of
     `users/` cannot be deleted.
- **Cross-check analysis tool.** A new **🔎 Cross-check vs. uploaded
  dashboard** button next to 🔀 Resolve duplicates opens a JSON
  picker. Drop another dashboard (full snapshot, slice, exported
  cross-check payload, or even a flat `entities[]` / `blocks[]`
  /`platforms[]` list) and the dashboard:
  - Walks every BB across both sides (recursing into `subBlocks` /
    `variants` / `children`).
  - Matches by `(lowercased name) @@ (lowercased platform)`.
  - Splits the result into Common / Here-only / There-only.
  - Renders three SVG donut charts: **per-platform**,
    **here/there/common**, and **variants as a category** so a
    contributor's variant additions are visible at a glance.
  - Shows a side-by-side summary card with "w/o variants" and
    "with variants" counts per side.
  - Exposes a **Download Excel report** button (multi-sheet:
    Summary, Common, Here-only, There-only, Variants here,
    Variants there, Per-platform counts).
  - Per-BB checkboxes on the Here-only / There-only lists with a
    **Download selected (.zip)** action — bundles every file
    associated with each ticked BB (GDS / OAS / S-matrix / tests /
    fab / scripts / documents / EDA-links / files) into a single
    zip with the canonical CORNERSTONE folder layout.
- **Variants tracked as a first-class category.** A recursive walker
  flags every sub-block / variant with `isVariant: true` and a
  `parentName` link. The same flag flows into the cross-check
  donuts, the Excel report, and the BB-list header bar so the user
  can see at a glance how many variants a dashboard carries.
- **Header bar variant chip.** The stats row in the main board
  header now shows `🌿 N variants` and `M total` (top-level BBs +
  every recursive variant). Tooltip clarifies that the "X blocks"
  total to the left excludes variants.
- **Editable footer (Terms / TAPR notice / Software).** Admin mode
  exposes inline editors for the three footer strings (Terms link
  text, the hardware-licence sentence, the software-licence
  sentence). Edits persist to `localStorage` under
  `pdk-monitor.legalTexts`, with a reset-to-default button per
  field. Public read-only renders the saved text verbatim.
- **🧹 Clear cache button visible in every role.** Previously only
  read-only mode showed Clear cache; user-admin and admin saw the
  legacy "Reset" label. The button is now exposed in all three
  modes with the same effect (wipe IndexedDB + localStorage +
  sessionStorage and reseed factory defaults). The duplicate Clear
  cache button next to it in read-only has been removed.
- **Prepare-for-PDK flow available in Admin mode.** The 🎁 Prepare
  for community PDK action no longer hides behind user-admin
  gating — admins can now use the same modal to push curated
  files into the community repo. (Admin Direct commit still
  bypasses cooldowns and is still blocked from publishing into
  `users/` by the safety gate above.)
- **Tab-named externalize subfolders.** Per-user externalize now
  writes binaries under tab-named subfolders instead of the
  `PDKMonitor_*` prefix:
  `users/<id>/<bb-slug>/<section>/<filename>` where `<section>`
  is one of `layout`, `smatrix`, `tests`, `fab`, `eda`,
  `scripts`, `documents`, `files`, `variants`. Existing items
  still load from the legacy paths; new commits use the new
  layout.
- **EDA-link externalize.** When a BB carries `edaLinks`, they are
  serialised to `<bb-slug>/eda/eda_links.json` on publish (and
  pruned from the repo if removed locally), so adopters fetching
  the slice see the EDA / cloud-IDE references too.
- **`scheduleBoardRefresh()` debounce in `saveModel`.** A 40 ms
  debounced `render()` runs on every model save, so BB card
  badges (file counts, tab indicators, status pills, the new
  variant chip) update immediately after an upload without
  waiting for the next interaction.
- **Platform column min-width — content-aware floor.** Columns can
  now shrink until the technology platform name is about to be
  covered by the Sync / Import buttons. `.title-area` uses
  `flex: 1 0 auto` so action buttons wrap onto the next line
  before the title shrinks. Group-head also wraps so categories
  flow gracefully instead of forcing horizontal scroll.
- **BB-row first-5-letters guarantee.** Each BB row in the list
  reserves enough min-width that at the narrowest column setting
  the first 5 letters of the BB name remain visible (status pill
  + category tag yield first).
- **User-admin can edit BBs created by other users.** Permissions
  simplified to a collective contributor model: admin → true,
  user-admin → true (any owner), read-only → false. Destructive
  ops (delete BB, delete file) still require ownership; everyone
  can add files and edit details on any BB.

### Fixed (rev. 4)

- **Admin publish wiping `users/` folders.** This was the trigger
  for the safety gates above — a one-off admin commit to
  `users/dashboard.json` had silently pruned every per-user
  subfolder because the orphan-file pruner walked from `users/`
  as a root. Recovery: revert via GitHub Commits → "Revert this
  commit" or `git checkout <pre-incident-sha> -- users/`.
- **Cross-check JSON loader was too strict.** Previously-working
  dashboard exports failed to load. New `_xcUnwrapPayload`
  recursively handles `.tabs[]`, `.model.tabs`, `.payload.tabs`,
  `.dashboard.tabs`, `.data.tabs`, `.state.tabs`, `.MODEL.tabs`,
  `.snapshot`, `.body`, array wrappers, plus synthesises a
  `tabs[]` from flat `groups[]` / `platforms[]` / `columns[]` /
  `entities[]` / `blocks[]` / `bbs[]` / `items[]`.
- **BB Details panel unclickable.** Renaming `mayModify` to
  `mayEdit` left two callsites stale (`Edit` button + "created
  by you" chip). Restored a single canonical `mayModify` binding.
- **Scroll cascade across BB tabs.** Three nested scrollers
  (`.panel-body`, `.folder-list-wrap`, `.folder-body`) fought
  over wheel events:
  - `.panel-body` / `.modal-body` get `min-height: 0` so they
    can shrink inside a flex parent and engage their own
    `overflow-y: auto`.
  - `.folder-list-wrap` drops its `max-height: 72vh` cap; the
    outer panel handles scrolling.
  - `.folder-body` keeps an inner ~3-item scroll at
    `max-height: 280px` with `overscroll-behavior: contain`
    and extra `scroll-padding-bottom` so the × on the last
    row always reaches the viewport.
- **Title shrinks before buttons.** `.title-area` was `flex: 1`
  (= `1 1 0%`) so it shrank first and the platform name got
  covered. Changed to `flex: 1 0 auto` — buttons wrap first,
  title stays.
- **Custom BB type id surfacing.** `bbTypeLabel(t)` resolver
  applied everywhere — Details "Type" line, YAML export, PR body,
  markdown report, Excel export, search index. Custom Manage-list
  entries (e.g. `x_9pogrls`) now render as "heater" wherever
  they appear.
- **⬇ Download on cross-user file delivered a JSON sidecar.**
  Lazy-hydrate now fetches the original bytes from the source
  repo on first click, caches the bytes locally, and saves with
  the correct MIME type + filename.
- **BB card badges stale after upload.** The new debounced
  `scheduleBoardRefresh()` re-renders after every `saveModel`,
  so newly-added S-matrix / test / fab files surface their tab
  badge immediately.

### Removed (rev. 4)

- **"(with variants)" suffix from the header total chip.** Chip
  now reads simply `M total` (with tooltip explaining the maths)
  instead of `M total (with variants)`.
- **Duplicate Clear cache button in read-only.** Two adjacent
  Clear cache buttons appeared after the role-parity work;
  collapsed to one.

---

## [1.0.0] — 2026-05-27 *(rev. 3 — Prepare-for-PDK, cooldowns, fetch fidelity, OASIS support)*

Same v1.0.0 stamp. Rev. 3 lands a polished cross-user contribution
flow (Prepare for community PDK with per-section file multi-select,
two-button publish with sensible cooldowns), accepts OASIS layout
files alongside GDS in the Layout section, accepts every file type
in Scripts / S-matrix GitHub fetch, and finally makes downloading
another contributor's file return the original bytes instead of a
JSON sidecar.

### Added (rev. 3)

- **Layout section accepts GDS + OASIS as essential formats; SVG
  kept as a preview-only fallback.** The Layout tab's Upload
  button is now labelled "Upload .gds / .oas / .svg" and the file
  picker accepts `.gds`, `.oas`, `.oasis`, `.svg`. The upload-mode
  tab reads "Layout (GDS/OAS/SVG)". When a .oas / .oasis file is
  uploaded or fetched, its bytes are stored verbatim under the
  existing layout slot (so download and publish round-trip
  losslessly) and a placeholder SVG announces the filename + size
  in the viz stage; for the actual polygon view, open the file in
  KLayout. A new `Download .oas` button appears alongside
  `Download .gds`. The ⤓ GitHub fetch dialog picks `.gds`, `.oas`,
  and `.oasis` from a folder URL — preferring a name-matched GDS
  first, then a name-matched OAS, then any GDS, then any OAS.
  Prepare-for-PDK's per-section file picker labels OAS uploads as
  "(OASIS layout)" so they're unambiguous in the multi-select.
- **Prepare-for-PDK — per-section file multi-select.** Every row in
  the Prepare-for-PDK modal's section-target table now shows a
  `📄 N/M ▾` toggle. Expand to see every file that section would
  commit, each with its own checkbox. Select-all / Clear buttons
  per panel. Selection persists per-BB in `localStorage`. Untick
  individual files to exclude them — useful for landing only the
  GDS + YAML and leaving WIP datasets behind, or skipping a single
  noisy test file from a folder.
- **Dual publish buttons with separate cooldowns.** The Prepare
  modal now exposes two publish paths side-by-side:
  - **📤 Open Pull Request** — groups all files by target repo and
    opens one PR per `(owner, repo, branch)`. Throttled to **1 PR
    per hour per identity** to keep reviewers' notifications sane.
    Failed PRs don't burn the slot.
  - **⚡ Direct commit** — commits straight to each target branch.
    Lenient progressive backoff: 0 s, 30 s, 90 s, 180 s, 300 s,
    600 s, 1200 s. Allows **2 commits inside 1 min** and
    **5 commits inside 10 min**, then gradually slows. Per-identity
    in `localStorage`. Admin role bypasses both.
- **Live cooldown countdown.** Both buttons show a 1-Hz ticker
  ("📤 PR available in 47m 03s", "⚡ Direct commit (2m 14s)") so
  the user always knows when the next attempt is available.
- **Editable target URL in Commit-tests-to-repo.** The previous
  static URL line is now an editable input pre-filled with the
  computed `<platform>/test/<bb>/` path. Saved per-BB in
  `localStorage["pdk-monitor.commitTestsTarget"]`. A **Reset to
  default** button repopulates the field. On commit, the edited
  URL is parsed by `parseGithubUrl` and used as the destination —
  so tests can be committed anywhere (e.g. your own
  `users/<id>/<bb>/tests/`).
- **Scripts GitHub fetch — accepts any file type.** Previously
  only `.py` files landed in the Scripts tab; everything else
  silently leaked into Tests. Now Scripts fetch routes textual
  files (`.py`, `.ipynb`, `.sh`, `.js`, `.ts`, `.m`, `.r`,
  `.jl`, `.f90`, `.c/.h/.cpp`, `.java`, `.go`, `.rs`, `.sql`,
  `.md`, config formats, etc.) as editable source, and binaries
  (`.exe`, `.so`, `.jar`, …) as base64 `binaryContent` so they
  survive publish round-trips.
- **S-matrix GitHub fetch — broader extension set.** Touchstone
  variants `.s/y/z/h/gNp`, `.hdf5`/`.h5`, `.tdms`, `.mat`,
  `.npz`/`.npy`, `.prn`, `.asc`, `.out`, `.log`, `.pickle`/`.pkl`,
  `.cti`/`.cit`, plus the previous CSV/TSV/XLSX/JSON/TXT list are
  all accepted. Small extension-less files are also kept so labs
  that emit untyped touchstone dumps work.
- **Lazy-hydrate on Download for cross-user items.** When a file
  card carries a `repoPath` but no in-memory bytes (the typical
  state for files contributed by another user-admin after the
  publish-strip), clicking ⬇ Download now walks every enabled
  Distribution source, fetches the bytes via the GitHub Contents
  API (with `git/blobs` fallback for files > 1 MB), writes them
  back onto the record, and completes the download with the
  original filename and bytes. First click hits GitHub, every
  subsequent click is local. So `S21_meas.csv` from another user
  now comes down as a real CSV instead of a `.metadata.json`
  sidecar.
- **Correct MIME on download.** The saved Blob is now tagged
  with the right Content-Type for the filename (text/csv,
  application/pdf, image/png, application/x-yaml, etc.) instead
  of generic `application/octet-stream`. OS / browser show the
  right icon, open with the right app.
- **`bbTypeLabel(t)` resolver.** Custom BB types added via
  Manage-lists get a `uid("x")` id (e.g. `x_9pogrls`). The id was
  surfacing raw on the BB Details "Type" field, the YAML export,
  the PR body, the markdown portfolio report, and the Excel
  export. A new label resolver (same shape as `statusLabel` /
  `categoryLabel`) looks up the matching `MODEL.bbTypes` entry by
  id or name. Plumbed through every user-facing render site.
  Search index now matches against both the raw id and the
  resolved label, so typing "heater" finds BBs whose `bbType` is
  `x_9pogrls` but whose Manage-list label is "heater".

### Removed (rev. 3)

- **No README on Prepare-for-PDK publish.** The auto-generated
  `<bb>.README.md` upload has been removed from both the commit
  flow and the BB zip download — only principal files (GDS /
  YAML / datasets / scripts / docs / `eda_links.json` / variants)
  land in the target. The PR body still carries a description
  when publishing as a PR.
- **No SVG upload on Prepare-for-PDK.** SVG previews are
  procedural and regenerable from the GDS or BB type at render
  time; they no longer ship to the community PDK to keep the
  target folder clean.

### Fixed (rev. 3)

- **File-list scroll inside Tests / S-matrix / Fab tabs.** The
  unreachable-× bug and the "scroll doesn't work at all"
  regression had a single root cause: three nested scroll
  containers (`.panel-body`, `.folder-list-wrap`,
  `.folder-body`) fighting over the same wheel events. Fix:
  - `.panel-body` and `.modal-body` get `min-height: 0` so the
    flex layout can actually shrink them to their allocated
    space and `overflow-y: auto` engages a real scrollbar.
  - `.folder-list-wrap` drops its `max-height: 72vh` cap — the
    outer panel scroll covers it.
  - `.folder-body` keeps an inner scroll at `max-height: 280px`
    (~3 file cards) so opening a 30-file folder still shows a
    compact card, with `overscroll-behavior: contain` to let
    wheel events fall through to the outer scroll once the
    inner reaches a boundary. Extra `padding-bottom` +
    `scroll-padding-bottom` so the last row's wrapped action
    buttons (including ×) are always reachable.
- **Prepare-for-PDK GDS missing on fetch.** Root cause: the
  modal was mutating `e.gdsRepoPath` to point at the
  cornerstone-community target after upload, but adopters
  hydrate from the SOURCE repo (the user's slice), not the
  target. Fix: never mutate `gdsRepoPath` / `repoPath` in
  Prepare-for-PDK; those track the user's own slice. Added
  re-fetch from the user's source repo when bytes are stripped
  after externalize.
- **Prepare-for-PDK YAML routing.** Layout section dropdown now
  spells out that the GDS / OASIS + YAML descriptor both land
  in `components/<bb>/`. Fabrication dropdown renamed from
  "Fabrication artefacts" to "🔬 Fabrication". Added a
  **Create new folders if missing** tick (default ON) so an
  off-by-one typo can no longer create stray directories.
- **User-admin BB edit rights.** User-admins can now edit all
  BBs created by other users (collective contributor model).
  Previously they could only edit their own. `canModifyId(id)`
  simplified to: admin → true, user-admin → true regardless of
  owner, read-only → false.

---

## [1.0.0] — 2026-05-26 *(rev. 2)*

Final post-release polish bundled into the v1.0.0 stamp. Same public
version number — every patch below is an incremental fix or feature
that landed during the first 48 hours of feedback.

### Added — post-release patches (rev. 2)

- **Selective fetch picker** — 📚 Fetch user repos now opens a
  checklist of every discovered slice before pulling; user ticks
  which ones to install. Default: all ticked.
- **Read-only column + side-panel resize** — both resize handles
  visible in read-only (layout-only state, persisted locally; side
  panel width to `localStorage`).
- **🧹 Clear cache button for read-only** — the Reset button is
  now visible in read-only mode with a "Clear cache" label.
  Wipes IndexedDB + localStorage + sessionStorage and reseeds
  factory defaults. Nothing on GitHub is touched.
- **Per-row Skip / Merge-in checkboxes in cross-platform
  resolver** — lets the user choose which non-keeper copies
  actually fold into the keeper, instead of all-or-nothing.
- **Backfill ownership from `who` at model load** — `ensureLists`
  now fills `MODEL.ownership[id]` from each item's `who` field
  when the map entry is missing. Fixes "user-admin can't delete
  their own legacy uploads".
- **Opt-out for auto-walk of `users/`** — checkbox in Fetch &
  adopt, **default OFF**. 📥 Fetch all enabled sources now pulls
  ONLY the configured main `dashboard.json` (no silent fold-in of
  every user contribution). Tick to restore prior auto-walk.
- **📦 Download .zip button per BB** — packs every file on a BB
  (GDS, S-matrix, tests, fab, scripts, documents, files) into a
  single zip with the CORNERSTONE folder layout
  (`components/sparams/characterisation/fabrication/scripts/documents/`)
  plus an auto-generated YAML descriptor.
- **Auto-generated YAML on GDS upload** — when a `.gds` is
  uploaded for a BB, the dashboard auto-parses the layers used
  and attaches a YAML descriptor (`<bb-slug>.yaml`) to the BB's
  Documents tab. Format mirrors the cornerstone-community
  `Si_220nm_passive` convention.
- **Photodetector sketch — material-neutral.** The procedural PD
  sketch no longer hard-codes Ge; the absorber gradient is now
  driven by the platform's `matStyle`. Title is just
  "Photodetector"; the absorber material is platform-determined.

### Fixed (rev. 2)

- Cross-platform resolver keeper choice now actually persists
  across re-renders (key mismatch between `data-sig` and the bare
  cross-platform sigKey).
- Auto-resolve all includes cross-platform clusters too — fixes
  the "always 0" report when only cross-platform dupes existed.
- On-load consolidator collapses pre-existing duplicate "Files" /
  "Imported" folders on every BB (cleans up dashboards that
  accumulated dupes from older versions).
- S-matrix fetch hydrate now re-parses bytes so CSVs render as
  📊 `N pts · Y traces` instead of inert raw text. Section-fetch
  walker now goes 5 levels deep; smatrix mode filters non-data
  extensions.

---

## [1.0.0] — 2026-05-26 *(initial public release)*

The official first public release of the CORNERSTONE Open Platform
PDK Monitor. Authored by **Emre Kaplan** during a focused 25-day
beta from 1 May 2026 → 26 May 2026.

### Architecture

- **Single-file HTML dashboard.** Open `pdk-monitor.html` in any
  modern browser — no install, no server, no build step. All state
  lives in the browser's IndexedDB + `localStorage`; sharing happens
  through GitHub.

- **Real-files architecture.** When a user-admin publishes their
  slice, every uploaded item (CSV, GDS, S-matrix file, document,
  script) is written as a SEPARATE real file in the repo under
  `users/<id>/<section>/<bb>/<filename>`. The `dashboard.json`
  carries only metadata + `repoPath` references. Pool members
  hydrate the real bytes on fetch.

- **Three-tier role model.**
  - **Admin** — full control, owns the main `dashboard.json`.
  - **User-admin** — owns their own `users/<id>/` folder; can
    publish, prune, and wipe it without ever touching the main
    `dashboard.json`.
  - **Read-only** — view, search, run scripts, download.

### Features

#### Platforms & BBs

- Multi-platform tabs (SOI 220, TFLN 300, SiN 200/300, etc.) with
  per-platform BB libraries grouped by BB type.
- Per-BB tabs: Overview, Layout (GDS), S-matrix, Tests, Fab,
  Scripts, Documents.
- Inline GDS preview rendered from raw GDS bytes; layer/datatype
  metadata exposed in the Layout tab.
- Custom sketch upload (PNG / SVG / JPG) per BB, with augmented
  procedural sketches as the default (material gradients, I/O
  arrows, dimension lines).
- Per-platform BB-type shortlist on the column head — filter
  visible BBs by type.

#### S-matrix / Tests / Fabrication

- Parsers for CSV / TSV / XLSX / S1P–S8P touchstone / DAT / JSON /
  TXT — every file type plots as `📊 N pts · Y traces`.
- Canonical "Files" folder per BB with the stable id
  (`sg_files` / `tg_files` / `ft_files`); on-load + post-merge
  consolidator collapses duplicate folders across version
  histories.
- Folder-name routing — a GitHub folder named `tests/` /
  `sparams/` / `fab/` auto-routes contents to the matching BB
  section.
- Per-section GitHub fetch with smart filtering (smatrix mode
  skips READMEs, licences, configs; depth-5 walker for nested
  layouts).

#### GDS Layers

- Top-level Layers list: name, layer/datatype, description, colour.
- Hover popover with full description.
- Sortable by layer number; filterable by BB type via the
  Manage-list dropdown.

#### Distribution & multi-user pool

- **Shared repositories** — configure multiple sources, each with
  enable / disable.
- **📥 Fetch all enabled sources** — pulls main `dashboard.json` +
  auto-walks every plausible `users/` location (sibling, parent,
  repo-root, `OpenSource_PDKMonitor/users`).
- **🔍 Discover user slices** — explicit walker, multi-pick UI.
- **🪪 Fetch my slice** *(user-admin)* — pulls + bootstraps the
  signed-in user's own slice.
- **📚 Fetch user repos** *(advanced)* — one-click pool assembly,
  walks every `users/<id>/` across every enabled source.
- **Merge into my dashboard** — additive, never overwrites local
  changes.
- **🔁 Replace whole dashboard** *(danger)* — wipes local copy +
  uses merged remote. Always downloads a Backup `.json` first.
- **📤 Publish as Pull Request** / **⚡ Direct commit** — both
  routes available; user-admin direct commits auto-route to their
  `users/<id>/dashboard.json` and externalize binaries to real
  files in their folder.
- **🌱 Seed user folders from sign-in list** *(admin)* — creates
  `users/<id>/dashboard.json` for every entry in the password list.
- **🗑 Wipe my repo folder** *(user-admin, danger)* — nuclear
  delete of every file under `users/<id>/`; local model untouched.

#### User-admin write-back mirror

- Every ⚡ Direct commit externalizes new / changed binary items as
  real files, then prunes orphan files that are no longer
  referenced — so the repo folder mirrors the local model
  exactly.
- Idempotent — re-publishing only uploads new / changed items.

#### Robustness

- Three-tier publish fallback: PUT → retry on conflict → git tree
  API. Survives BadObjectState / 422 / 500.
- Eight retries with exponential + jittered backoff.
- Heavy-binary strip (256 KB documents, 4 MB datasets, 2 MB parsed
  arrays) keeps `dashboard.json` under the GitHub contents API
  cap when binaries can't be externalized.
- Multi-location user folder discovery (sibling, parent + /users,
  repo-root, `OpenSource_PDKMonitor/users`).
- Hydrate re-parses CSV / XLSX / touchstone on fetch so adopters
  see real parsed data, not inert raw text.

#### Duplicate resolver

- Same-name BBs / groups / tabs collapsed via cluster picker.
- Cross-platform BB clusters with user-selectable keeper (radio
  buttons) and per-cluster "Merge into selected keeper" /
  "Attach as variants of keeper" actions.
- **Bulk select + bulk actions** — every cluster (same-parent and
  cross-platform) shows a checkbox. Tick the clusters you want
  to action, then use the toolbar buttons:
  - **Select all / Deselect all** master checkbox
  - **📥 Merge N selected** — applies the merge to every ticked
    cluster (cross-platform respects per-cluster radio choices)
  - **🌿 Variants N selected** — attaches the others as variants
    of the keeper for every ticked cross-platform cluster;
    same-parent clusters fall back to a regular merge
  - **🚀 Auto-resolve all (N)** — auto-picks keepers (most
    content + most recent) and merges every cluster, including
    cross-platform. Confirm prompt shows the cross-platform
    count separately. Selection state is preserved across
    re-scans by cluster signature.
- On-load consolidator runs automatically — folders with the same
  normalised name fold into one canonical folder per BB.

#### Scripts

- Inline Python runner (Pyodide-based) with sandbox.
- `data` / `tests` / `bb` globals expose parsed data.
- **💻 Run locally…** packs script + every BB file into a folder
  with VS Code / PyCharm / Sublime deeplinks.

#### Security

- Admin password lives in `localStorage` only — never published.
- User-admin sign-ins (guestPasswords) publish as
  `_distributedSecurity` so adopters get the sign-in list.
- GitHub token lives in `localStorage`; the dashboard never bundles
  it.

### Limits

- Per-file upload cap: 50 MB (smaller of GitHub contents API limit
  and browser memory).
- Total project size: ~10 GB across all files for the in-browser
  IndexedDB store.
- Recommended pool size: up to ~20 user-admin slices per main
  repository; pool assembly scales O(N) in API calls.

### What's in the bundle

```
pdk-monitor.html
pdk-monitor-v1_0_0.html        ← versioned copy for GitHub direct upload
dashboard-v1_0_0.json          ← canonical starter dashboard JSON
PDK-Monitor-User-Manual.pdf
PDK-Monitor-User-Manual.docx
TERMS.md
LICENSE-TAPR.txt
CHANGELOG.md
RELEASE_NOTES.md
README.md
```

### Licence

Hardware content: **TAPR Open Hardware License v1.0** (see
`LICENSE-TAPR.txt`).

### Credits

Authored by **Emre Kaplan** (a.emrekaplan@gmail.com) for the
CORNERSTONE Open Platform — the UK silicon-photonics rapid
prototyping foundry programme at the University of Southampton
(https://www.cornerstone.sotonfab.co.uk/).

The portal is open to everyone for further development.

---

## Development history (pre-1.0.0)

Daily-iteration log between the 1 May 2026 beta and the 26 May 2026
official release. Internal version numbers were used during this
period and are NOT part of the public versioning scheme — every
feature listed below is included in v1.0.0.

- Beta `b0.1` (01 May 2026) — initial single-page dashboard with
  platform list, BB list, basic file uploads.
- Platform import workflow + GDS parsing.
- GDS Layers list with hover popovers.
- Role-based access (admin / user-admin / read-only).
- Distribution panel (configure sources, fetch, merge, replace).
- Robust publish helper with PUT → retry → tree-API fallback.
- User-admin slice publishing into `users/<id>/dashboard.json`.
- Duplicate resolver (within-platform).
- Cross-platform duplicate resolver with variant attachment.
- Real-files architecture — externalize binaries, hydrate on fetch.
- Orphan file pruning on publish + 🗑 Wipe my repo folder button.
- Multi-location `users/` discovery (both single-file + directory
  modes).
- 📚 Fetch user repos one-click pool assembly.
- Empty S-matrix entries fix (hydrate re-parse + smatrix-only
  extension filter + depth-5 walker).
- On-load + post-merge duplicate folder consolidator.
