---
description: Renders the traceability-spine coverage report and stage ledger from local files only. No network access.
argument-hint: "[--html]"
---

# /ddd-status — Roll-up Report

Orchestration only (Constitution C9) — the actual computation lives in `scripts/status-report.py` (REQ-32: local files only, no network — the script itself never imports `urllib`/`socket`/`http`/`requests`) and, for `--html`, `scripts/render-report.py`.

Read `plugin_root` from `.ddd-redesign/state.json` (set by `/ddd-init`) first — the scripts below are `<plugin_root>/scripts/<name>.py`, never a bare relative path, since this command's working directory is the target repository. If `plugin_root` is not yet set (no `/ddd-init` has run), say so and stop — there is nothing to report on yet.

## What this command does

Runs `python3 <plugin_root>/scripts/status-report.py --dir .ddd-redesign` and prints its output: per-stage status ledger, Build Stream conformity % and Consumer Migration Stream adoption % (always reported separately, never blended — REQ-13), ensemble agreement distribution, Divergences by classification, unresolved high risks, and the count of Interfaces with `sunset_authority: null`.

If invoked with `--html`, additionally run `python3 <plugin_root>/scripts/render-report.py --dir .ddd-redesign` to regenerate `.ddd-redesign/report/index.html`, and tell the operator where to open it. Report generation is never a gate — if it fails, the failure is recorded in the fallback page and this command still completes (`12-extension-seams.md` §5).

## What this command never does

Write to any inventory, model, or slice artifact. Make network calls of any kind. Modify `state.json` beyond what `status-report.py` itself reads (it is read-only). This command is safe to run at any point in an engagement, in any stage, as many times as wanted.
