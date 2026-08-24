# Agentic Documentation Audit & Remediation — Case Study

*A generic case study describing a tool built to assess and improve AI-agent readiness across an engineering organization's codebases, using swarms of AI agents.*

## Problem

As AI-assisted development scales across an organization, the quality of codebase documentation directly determines how effectively AI agents can operate on a given repository. A steering-file convention (an `AGENTS.md`-style file read by the AI coding assistant) is the primary mechanism for giving agents the context they need — project structure, build commands, architecture, maintenance rules — but there was no way to answer a basic organizational question: **how many of our applications are actually ready for agentic development?**

Answering this manually across hundreds of applications is infeasible — reading each repo, evaluating documentation quality, and verifying accuracy against live code would take weeks of engineering time.

## What Was Built

A two-part system — **audit** and **remediation** — using swarms of AI agents to answer the org-wide readiness question and then fix the gaps, delivered through shareable links to an internal agent-orchestration platform.

### Part 1: The Audit Swarm
1. Discovers all pipelines in the org (via an internal pipeline-catalog API).
2. Filters down to production applications (in the first run: roughly **630 apps out of ~940 pipelines**).
3. Checks every repo for the presence of a steering/AGENTS file (in the first run: roughly **3,200 repos scanned**).
4. Generates CSV + Markdown reports, broken down per-application and per-owner.
5. Each worker agent uses internal code-search and internal-documentation-lookup tools to browse the actual codebase, fetch linked documentation, and verify claims against live code. **Workers are read-only** — no write or shell access.

Example run invocation and output pattern:
```
Swarm Audit: type=agentic-doc concurrency=5 retries=3
Phase 1: Fetching pipelines... Found ~930 pipelines
Phase 2: Fetching pipeline details... [progress bar] ~940 succeeded, 0 failed in ~8 minutes
  Skipped a handful of deleted/not-found pipelines
  Skipped pipelines with no production stage
```

### Part 2: The Doc Writer Swarm
For applications that fail the audit, a second swarm generates the complete documentation suite:
1. **Research phase** — several parallel agents analyze the codebase across dimensions: architecture, services, dependencies, testing, operations, configuration.
2. **Generation phase** — more parallel agents write individual documentation files (the steering file, architecture docs, service docs, dependency guides, operations guides).
3. **Satellite generation** — agents create steering files for non-central repos that reference the central docs.
4. **Validation phase** — additional agents score each document on a 1–5 scale for completeness and accuracy.
5. **Fix phase** — documents scoring below threshold are automatically revised.
6. **Change-request creation** — all changes are committed, and a draft change request is created for human review.

Example scorecard from a first run (documents scored 1–5):

| Document | Score |
|---|---|
| Steering/entry-point file | 3.0 / 5 |
| Architecture doc | 4.0 / 5 |
| Index doc | 4.0 / 5 |
| Testing doc | 3.8 / 5 |
| Troubleshooting doc | 4.0 / 5 |
| Service-level compliance docs (several) | 3.7–3.8 / 5 |
| Dependency guides (several) | 4.0 / 5 |
| Satellite/referencing docs (several) | 3.7 / 5 |

After scoring, a fix pass ran on the lowest-scoring docs: roughly 7 documents fixed in parallel, all succeeding within a few minutes.

## Architecture

Both paths (audit and remediation) are repod as reusable skills for the org's agent platform, distributed via the normal internal repo/version-set mechanism, and installable both by human teams and directly by agents through the orchestration platform.

- **Orchestrator** — routes to either the audit or doc-writer path based on the user's request, using a large-context coding-agent model.
- **Audit path**: pipeline-catalog API → pipeline-detail API → repo-inventory API → several concurrent read-only worker agents (a smaller/faster model) → report generation (CSV + summary markdown).
- **Remediation path**: code-search API → workspace setup → research swarm → generation swarm → satellite swarm → validation swarm → fix swarm (as needed) → change-request creation → shareable fix links.

## Timeline (Appendix)

| Milestone | Description |
|---|---|
| Day 1 | Built the audit swarm — pipeline discovery, documentation pre-check, worker agent evaluation, report generation |
| Day 2 | Built the doc writer swarm — research, generation, validation, fix, change-request creation |
| Day 3 | repod as a reusable capability, added orchestration-platform integration, ran the first full org audit |

## Results: First Org Audit

*(The source material ends mid-results table — the specific top-line readiness numbers from the first org-wide audit weren't fully captured. The scorecard above, from the remediation pass, is the most complete numeric result available.)*
