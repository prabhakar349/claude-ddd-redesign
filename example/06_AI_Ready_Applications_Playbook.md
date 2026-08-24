# AI-Ready Applications Playbook

*A generic playbook defining what an "AI-Ready" application looks like, how to build one from scratch, and how to migrate existing applications to this model. Genericized from an internal engineering-org playbook; illustrative examples use placeholder service names (Hub A, Hub B, Hub C, Service X) in place of any specific product.*

## Executive Summary

This playbook addresses:

1. **AI-Ready Architecture Definition** — what "AI-Ready" means, how to prioritize optimization work streams, and how this indexes on a target architecture.
2. **Tooling to Convert Legacy Codebases** — concrete tools, patterns, and AI-assisted workflows to accelerate migration.
3. **E2E Testing Architecture & Guardrails** — maximizing test coverage for continuous deployment with AI-integrated testing.

**Core thesis:** AI agents fail not because of capability limitations, but because codebases lack the structured documentation, bounded context, and explicit contracts that enable autonomous operation. In one internal pilot program, roughly 75% of agent failures traced back to missing tribal knowledge.

**Problem statement:** High AI tool adoption does not automatically translate to high-velocity software delivery. In one organization studied, AI tool adoption stood at **97.4%**, yet delivery metrics varied widely by team — for example, one high-performing team shipped roughly **2x** the change-requests per builder and **3x** the weekly production changes of the org-wide average.

The differentiator isn't tool adoption — it's *how* teams use the tools. Some teams have already shown measurable acceleration using this approach, but the broader organization hadn't yet replicated that success. Building AI-Ready Applications is the replicable framework meant to close that gap.

**Table of contents:** Part 1 — The Foundation (AI-Native Dev Environment); Part 2 — AI-Ready Architecture Definition; Part 3 — Building AI-Ready Applications from Scratch; Part 4 — Making Existing Applications AI-Ready; Part 5 — Tooling for Conversion; Part 6 — Testing Architecture & Guardrails; Appendices.

---

## Part 1: The Foundation — AI-Native Development Environment

Before choosing any development methodology, teams must establish an AI-native development environment. This is the proven prerequisite for consistent, high-velocity delivery — and the reason some teams outperform others despite identical tool adoption.

**Quality is the multiplier, in both directions.** AI accelerates good systems while amplifying the weaknesses of bad ones. A well-architected system becomes dramatically more productive with AI; a poorly designed one produces failures faster. As one team's senior engineers put it: *"A feature change that broke a pipeline once a week can now break it 20 times a day. Velocity without quality is just accelerated failure."*

**The AI-native mental model:** think of it like TDD — you don't write tests after the code, you write them first so correctness is a constraint from the start. AI-native development applies the same discipline: design the system, standards, and guardrails *first*. AI then operates within a structure that channels its output toward value, not chaos.

**Structural conditions that must come first:**
- **Well-defined architecture boundaries** — modular services with clear APIs prevent AI-generated code from creating hidden coupling across system boundaries.
- **Enforced coding standards** — clean-code principles, accessibility standards, and security best practices must be codified and taught to AI tools.
- **Shared context, deliberately set** — context is finite; if it isn't shaped around best practices, AI fills the gap with whatever patterns already exist in the codebase, accelerating technical debt rather than value.
- **Culture change — reframing the mental model** — the required shift isn't training sessions, it's hands-on immersion. Engineers must drive development entirely through AI with zero manual code.

---

## Part 2: AI-Ready Architecture Definition

An **AI-Ready application** is one where an AI agent can autonomously understand, modify, test, and deploy code with minimal human intervention. This requires three pillars:

| Pillar | Description | Why It Matters |
|---|---|---|
| **1. Bounded Context** | Features are localized to 1–2 repositories. Service boundaries align with business capabilities, not technical layers. | AI context windows are finite (~100–200K tokens). Changes spanning 15 repos overflow context and fail. |
| **2. Explicit Knowledge** | All tribal knowledge is documented in machine-readable formats — no implicit conventions, undocumented patterns, or "ask the team" dependencies. | A large share of AI agent failures trace to missing tribal knowledge. |
| **3. Contract-Driven Interfaces** | APIs, events, data models, and integration points are explicit schemas (OpenAPI, AsyncAPI, JSON Schema). | Agents need unambiguous contracts to generate correct integration code. |

### AI-Readiness Scoring (0–100 scale, across several dimensions)

| # | Dimension | Weight | What It Measures | Score 0 | Score 100 |
|---|---|---|---|---|---|
| 1 | Documentation Quality | 25% | Entry-point steering file, architecture docs, service docs, dependency docs | No docs | Complete, current, machine-readable docs |
| 2 | Architecture Clarity | 25% | Service boundaries, change locality, context size | Distributed monolith (15+ repos for a feature, multiple dependencies involved for any change, no isolation) | Domain-oriented (1–2 repos per feature) |
| 3 | Contract Explicitness | 10% | API specs, event schemas, data-model coverage | Tribal knowledge only | OpenAPI / AsyncAPI / JSON Schema |
| 4 | Test Infrastructure | 10% | Unit/integration/E2E coverage and automation | No tests or manual only | >90% coverage, fully automated, CI-integrated |
| 5 | Dependency Clarity | 10% | Upstream/downstream deps documented, with fallback strategies | Undocumented, discovered at integration time | Full dependency matrix with fallback strategies |
| 6 | Quality Guardrails | 20% | Guardrails ensuring AI-added code is safe (full CD, integration test coverage) | No tests or quality guardrails | Full CD, 100% integration test coverage |

**Readiness thresholds:**
- **≥70** — AI-Ready: agents can operate autonomously.
- **50–69** — Partially Ready: agents need significant human guidance.
- **<50** — Not Ready: invest in readiness before agentic development.

### Critical Path Optimization

Prioritize optimization by **change frequency × agent failure rate** — the domains where teams make the most changes *and* where agents fail most are the highest-ROI targets.

| Priority | Criteria | Action |
|---|---|---|
| P0 — Optimize Now | High change frequency + high agent failure rate | Full domain consolidation + documentation overhaul |
| P1 — Optimize Next | High change frequency + low agent failure rate | Documentation improvements, contract formalization |
| P2 — Optimize Later | Low change frequency + high agent failure rate | Lightweight documentation, defer consolidation |
| P3 — Monitor | Low change frequency + low agent failure rate | Maintain current state, document incrementally |

### Indexing on a Persona-Based / Target Architecture

Many organizations define a persona-based target architecture toward which their underlying tech is meant to move. All AI-readiness optimization work should converge toward that target, not just patch the current distributed monolith — the target architecture is the compass, AI-readiness is the accelerant.

**The trap: optimization without alignment.** Without deliberate alignment, teams take the path of least resistance — add a steering/entry-point file to their existing services, document what exists, and call it done. This improves agent success incrementally but doesn't move the org toward the target architecture; teams stay within current service boundaries and the distributed monolith persists.

AI-readiness work and target-architecture convergence are **the same work stream**, not two separate efforts — every documentation sprint, contract formalization, and test-infrastructure investment should be scoped against target-architecture boundaries, not current ones:

| Optimization Activity | Scoped to Current State | Scoped to Target Architecture |
|---|---|---|
| Documentation sprint | Document each existing service independently | Document the target domain boundary; map existing services into it |
| Contract formalization | Formalize APIs between existing services | Formalize the target domain's external API; mark internal service-to-service APIs as "to be consolidated" |
| Test infrastructure | Add tests to each existing service | Build test infrastructure for the target domain; E2E tests follow persona journeys as defined in target architecture |
| New feature work | Spread across repos as usual | Build in the target domain service; strangle existing services |
| Steering/entry-point docs | One per existing repo | One per target domain boundary, referenced by all legacy repos in that domain |

### Architecture Documentation — the C4 Model

Architecture documentation is the backbone of AI-readiness. Without it, agents have no map of the system — they don't know where a change belongs, what services exist, or how they interact. **C4** (Context, Container, Component, Code) provides four levels of zoom for describing software architecture — like a map application, zooming from country level (Context) down to street level (Code).

| Level | Describes | Answers | Illustrative Example |
|---|---|---|---|
| 1. Context | The system as a whole: boundaries, external actors, ecosystem fit | "What is this system? Who uses it? What does it interact with?" | The full platform (persona-based architecture): all persona hubs, foundation services, external systems |
| 2. Container | The services, applications, and data stores within the system | "What are the major building blocks? How do they communicate?" | Each persona hub (Hub A, Hub B, Hub C), each foundation domain (Data Platform, Workflow Orchestration, Identity) |
| 3. Component | The modules/internal structure within a container | "What are the internal parts of this service? How are they organized?" | Each functional cluster within a hub, each BFF core module, each domain service module |
| 4. Code | Implementation details: APIs, schemas, function signatures | "How is this implemented? What are the exact interfaces?" | OpenAPI specs, TypeSpec contracts, database schemas, function signatures |

**Why C4 matters for AI agents:** given a task like "add a new integration to Hub B," an agent traverses the hierarchy: (1) reads the Context doc to find Hub B, (2) reads Hub B's Container doc for its services/organisms/dependencies, (3) reads the relevant Component doc to understand internal structure, (4) reads the Code-level specs (API contracts, schemas) to generate correct implementation. Without this hierarchy, the agent has to guess where things belong — and guessing is where most failures come from.

**How C4 docs link together** (a navigable knowledge graph — Container docs can link across repos):
```
Context doc (system-wide)
├─ links to → Container doc (Hub A)
│    └─ links to → Component doc (a functional cluster within Hub A)
│         └─ links to → Code specs (OpenAPI, TypeSpec)
│              └─ links to → repo docs (HubA-infra/docs/)
├─ links to → Container doc (Hub C)
│    └─ links to → repo docs (HubC/docs/)
└─ links to → Container doc (Foundation Services)
     └─ links to → Component docs per service
```

**Governance: the document tree as the enforcement mechanism.** The primary way teams stay aligned with the target architecture is the architecture document tree itself. The Context-level doc defines the persona-based target architecture and links down to each hub's Container doc; those in turn reference other repositories' docs. Agents traverse this tree to understand the system — the tree *is* the source of truth, and keeping it accurate *is* the governance.

**Document tree structure (example):**
```
docs/architecture/
├─ context-target.md             # C4 Context: persona-based target architecture
│                                 #   Defines all hubs, their boundaries, external systems
│                                 #   Links DOWN to each hub's container doc
├─ container-hub-c.md            # C4 Container: Hub C
│    ├─ ../../../HubC/docs/architecture.md
│    └─ ../../../HubC/docs/bff-api-contracts.yaml
├─ container-hub-a.md            # C4 Container: Hub A
│    └─ ../../../HubA/docs/architecture.md
├─ container-hub-b.md            # C4 Container: Hub B
│    └─ ../../../HubB/docs/architecture.md
└─ container-data-platform.md    # C4 Container: shared platform services
     └─ ../../../DataPlatform/docs/architecture.md
```

**How this works for AI agents, step by step:**
1. Agent receives a task (e.g., "add a new integration to Hub B").
2. Agent reads the entry-point steering file, which points to `docs/architecture/context-target.md`.
3. The Context doc tells the agent: "Hub B owns this lifecycle — see `container-hub-b.md`."
4. The Container doc tells the agent the services in that hub, their boundaries, and links to the actual repo docs.
5. The agent follows the link to the target repo's `docs/architecture.md` for implementation-level context.
6. The agent now has full context: where the change belongs, what the boundaries are, what contracts exist.

**Key rules for the document tree:**

| Rule | Why |
|---|---|
| The Context doc authoritatively defines hub boundaries | Agents (and humans) have one place to understand "what belongs where" |
| Container docs link to docs in other repos via relative paths or URLs | Agents can traverse across repo boundaries without losing context |
| Lower-level docs reference their parent | Agents can climb back up for broader context when needed |
| Every hub's Container doc must exist before a team can claim AI-readiness | No Container doc = agent has no map = agent will fail |
| Cross-repo doc references must be kept current | Stale links break agent traversal — include link validation in CI |

**Cross-repository document references (example):**
```markdown
<!-- In container-hub-b.md -->
## Services
### ServiceB
- Repository: [ServiceB](https://code-host.example/repos/ServiceB)
- Architecture: See [ServiceB/docs/architecture.md](../../../ServiceB/docs/architecture.md)
- API Contract: See [ServiceB/docs/api-contracts.yaml](../../../ServiceB/docs/api-contracts.yaml)
```
The agent traverses these links as needed — e.g., if a task requires understanding a BFF's API to implement a backend change, it follows the reference, reads the BFF's docs, and returns with full context. The document tree acts as a navigable knowledge graph, not just static documentation.

### How to Apply This in Practice

For each domain defined in your target architecture:

1. **Map current state to target state** — which existing services belong to this target domain? What's the target service structure? Who owns the target domain? What's the gap between current and target?
2. **Score AI-readiness against the TARGET domain**, not individual legacy services — documentation (unified docs for the domain, not 15 separate READMEs), architecture (can features be completed within 1–2 repos?), contracts (are the target domain's external APIs formalized?), tests (do E2E tests follow persona journeys through the target domain?), dependencies (are cross-domain dependencies documented at the domain level?).
3. **Prioritize optimization work that moves toward the target:** Phase A (docs) — write docs for the target domain boundary, not individual legacy services; Phase B (contracts) — formalize the target domain's external API; Phase C (consolidation) — migrate code into the target domain service.
4. **Measure convergence quarterly:** % of new features built in the target domain service (vs. legacy); repos-per-feature trending down toward target; AI-readiness score per target domain trending up; number of legacy services retired.

---

## Part 3: Building AI-Ready Applications from Scratch

When building a new application, bake AI-readiness in from day one — dramatically cheaper than retrofitting later.

**What makes a codebase AI-ready** (based on an internal pilot program, two conditions):
1. **No tribal knowledge** — everything the agent needs is written down. Most agent failures in the pilot traced to missing tribal knowledge: conventions, integration patterns, domain-specific rules that lived only in people's heads.
2. **Documentation is navigable** — an entry-point steering file acts as the routing layer, pointing the agent to the right docs for any task. Without it, agents guess at conventions, miss integration requirements, and produce changes needing multiple revision cycles.

These aren't architecture principles — they're empirical findings. Architecture matters, but *documentation structure* is what determines whether an agent can work autonomously. A well-documented monolith will outperform a poorly-documented microservices setup for agentic development, every time.

### Documentation-First Design

Create the documentation structure *before* writing code, so the codebase is AI-ready from the first commit.

**Rule 1 — Eliminate tribal knowledge.** Everything must be written down in machine-readable form:

| # | Tribal Knowledge (Agent Fails) | Documented (Agent Succeeds) |
|---|---|---|
| 1 | "You need to call service X before Y" | Documented workflow in `docs/workflows/` |
| 2 | "This field is actually required" | JSON Schema validation |
| 3 | "Don't deploy during peak hours" | Enforced deployment windows in the pipeline |
| 4 | "Check with team Y before changing this" | Dependency contract in `docs/dependencies/` |
| 5 | "We use a custom auth pattern" | Documented in `docs/operations/auth.md` with code examples |

**Rule 2 — Documentation lives with code.** Versioned with code, updated in the *same commit* as the code change — enforced via steering-file rules (see the Code-to-Documentation Mapping pattern below).

**Rule 3 — the entry-point steering file is the starting point.** Every repository must have one, telling the agent: what rules to follow, where to find docs, how to build/test/deploy, and what patterns to use — onboarding documentation for AI agents, structured for machine consumption (the same information a new engineer would need).

**Rule 4 — architecture docs follow the C4 hierarchy.** Context defines system boundaries and links to Container docs; Container docs define service internals and can reference docs in other repositories. Agents traverse this tree to build understanding, and the tree must align with your target architecture.

### New Application Scaffold

Every new application should include a documentation structure covering what makes it fully AI-ready — not every service needs every file on day one. Start with what covers your service's tribal knowledge; don't wait for agent failures to tell you what's missing.

```
my-service/
├─ AGENTS.md                    # AI entry point (rules, conventions, workflows)
├─ README.md                    # Human entry point
├─ docs/
│  ├─ architecture.md           # C4 model architecture doc (links to parent Context doc)
│  ├─ api-contracts.yaml        # OpenAPI specification (if an API service)
│  ├─ dependencies/
│  │  ├─ README.md              # Dependency overview + matrix
│  │  └─ {service-name}.md      # Per-dependency integration guide
│  ├─ operations/
│  │  ├─ testing.md             # Test strategy, commands, patterns
│  │  ├─ build-deploy.md        # Build, deploy, rollback procedures
│  │  ├─ troubleshooting.md     # Common errors + resolution
│  │  └─ config.md              # Configuration approach
│  └─ guidelines.md             # Code patterns, conventions, import order
├─ .assistant-a/steering/AGENTS.md   # Steering rules for AI coding assistant A (copy or symlink)
├─ .assistant-b/rules/AGENTS.md      # Steering rules for AI coding assistant B (copy or symlink)
├─ src/                         # Application source (structure per team's choice)
└─ tests/                       # Test suites
```
The `src/` structure is up to the team — what matters for AI-readiness is the `docs/` structure and the entry-point steering file. The agent doesn't care how code is organized as long as it can find the documentation that explains it.

### AGENTS.md Template for New Applications

This template is an example — teams should fine-tune it for their own context (type safety rules, error-handling standards, coverage targets can be reused per team/org). If using Method 2 (Centralized Docs repo, see Part 4), these can live in the shared repo instead; this template shows the full self-contained-repo picture (Method 1), where duplication is the accepted tradeoff.

```markdown
# AI Assistant Rules for [Service Name]
**IF YOU ARE AN AI ASSISTANT YOU MUST FOLLOW THESE RULES**

## Entry Point
Start here: [docs/README.md] — Find the right documentation for any task.

## Core Rules
1. **Read Docs First**: Read `docs/architecture.md` before any structural change.
   Read `docs/dependencies/{service}.md` before modifying any integration.
2. **Type Safety**: Never use `any`. Define proper types.
3. **Error Handling**: ALL errors must be caught, logged, and return user-friendly messages.
   See `docs/operations/troubleshooting.md` for patterns.
4. **Testing**: Maintain >90% coverage. See `docs/operations/testing.md`.
5. **Build Validation**: Run `[build command]` before any commit.
6. **Documentation**: Update relevant docs in the same commit as code changes.
   See Code-to-Documentation Mapping below.
7. **API Contracts**: All API changes must update `docs/api-contracts.yaml`.
8. **Dependencies**: New external dependencies require an entry in `docs/dependencies/`.

## Code-to-Documentation Mapping
| Code Path | Primary Doc | Update When |
|---|---|---|
| `src/api/*` | `docs/api-contracts.yaml` | New endpoint or signature change |
| `src/integrations/*` | `docs/dependencies/*` | New integration or pattern change |
| `src/domain/*` | `docs/data-models.md` | New entity or schema change |
| `infrastructure/*` | `docs/operations/build-deploy.md` | New resource or config change |

## Build & Test
[build command]   # Build
[test command]    # Run all tests
[lint command]    # Lint

**ALWAYS FOLLOW THESE RULES WHEN YOU WORK IN THIS PROJECT**
```

---

## Part 4: Making Existing Applications AI-Ready

Most teams won't be building from scratch — they have existing codebases, often with many services, without the documentation structure agents need. This section covers two concrete approaches to making them AI-ready, focused purely on documentation, not on rearchitecting or refactoring.

### Approaches

| # | Approach | Best For | Effort | Tradeoff |
|---|---|---|---|---|
| 1 | **Method 1: Docs + Steering Per Repo** | Teams with few repos (1–5) that are relatively independent | 3–5 days per repo | Simple, self-contained — each repo is independently AI-ready. Duplication of shared knowledge across repos. |
| 2 | **Method 2: Centralized Docs repo** | Teams with many repos that share domain knowledge | ~1 day per consuming repo + 3–5 days for the central repo | Single source of truth, no duplication. Requires a dependency on the docs repo from all consuming repos. |

### Method 1: Docs + Steering Per Repo

Each repository gets its own complete `docs/` folder and entry-point steering file — the agent finds everything within the repo itself. **When to use:** your team owns 1–5 relatively independent repos, or you want the simplest possible setup.

```
MyService/
├─ AGENTS.md                    # AI entry point — rules, conventions, workflows
├─ docs/
│  ├─ architecture.md           # How this service fits in the system (C4 doc)
│  ├─ api-contracts.yaml        # API specs (OpenAPI, GraphQL schema, etc.)
│  ├─ dependencies/
│  │  ├─ README.md              # Dependency overview + matrix
│  │  └─ {service-name}.md      # Per-dependency integration guide
│  └─ operations/
│     ├─ testing.md             # Test strategy, commands, patterns
│     ├─ build-deploy.md        # Build, deploy, rollback procedures
│     ├─ troubleshooting.md     # Common errors + resolution
│     └─ guidelines.md          # Code patterns, conventions, import order
├─ .assistant-a/steering/AGENTS.md   # Assistant A reads from here (copy or symlink)
└─ .assistant-b/rules/AGENTS.md      # Assistant B reads from here (copy or symlink)
```

**Cross-repo references** — even with a per-repo `docs/` folder, `dependencies/` files can reference documentation in other repositories, and the agent traverses those references when it needs broader context:
```markdown
<!-- MyService/docs/dependencies/other-service.md -->
## Other Service Integration
- Repository: OtherService
- API Contract: See [OtherService/docs/api-contracts.yaml](link-to-repo)
- Architecture: See [OtherService/docs/architecture.md](link-to-repo)
- Error Handling: Returns 4xx for validation errors, 5xx for internal failures.
  See [OtherService/docs/operations/troubleshooting.md](link-to-repo)
```
This means the agent working in `MyService` can follow the link to `OtherService`'s docs when it needs to understand the integration — without duplicating those docs locally.

### Method 2: Centralized Docs repo

A dedicated documentation repo acts as the single source of truth for a domain/team. Each service repo's steering file points to this central repo: the agent reads the central docs for shared knowledge (architecture, conventions, dependencies) and the local repo for service-specific detail. **When to use:** your team owns 5+ repos that share domain knowledge, conventions, and cross-cutting dependencies, and you don't want to duplicate architecture/dependency docs across every repo.

```
MyDomainDocs/                    # Central docs repo (its own repo/repo)
└─ docs/
   ├─ AGENTS.md                  # Master AI rules for the domain
   ├─ architecture.md            # Domain-level C4 architecture
   ├─ services/
   │  ├─ service-a.md
   │  ├─ service-b.md
   │  └─ service-c.md
   ├─ dependencies/
   │  ├─ README.md               # Domain dependency matrix
   │  └─ {external-service}.md   # Per-dependency integration guide
   └─ operations/
      ├─ testing.md              # Shared test patterns
      ├─ build-deploy.md         # Shared build/deploy patterns
      ├─ troubleshooting.md      # Shared error handling
      └─ guidelines.md           # Shared code conventions

MyServiceA/  (consuming repo)
├─ .assistant-a/steering/AGENTS.md   # Points to central docs:
│                                     #   "Read ../MyDomainDocs/docs/ for domain rules;
│                                     #    this repo's service-specific docs are below"
└─ docs/
   └─ service-specific.md        # Anything unique to this service

MyServiceB/
└─ .assistant-a/steering/AGENTS.md   # Same — points to central docs
```

**How the agent navigates:** (1) opens `MyServiceA`, reads its steering file; (2) that file says "for domain architecture, conventions, and dependencies, read `../MyDomainDocs/docs/`; for this service specifically, read `docs/service-specific.md`"; (3) the agent reads the central docs for shared context; (4) reads the local service-specific docs for implementation detail; (5) has full context, with no duplication.

**Per-repo steering-file template (for consuming repos):**
```markdown
# AI Assistant Rules for [Service Name]
**IF YOU ARE AN AI ASSISTANT YOU MUST FOLLOW THESE RULES**

## Domain Documentation
This service is part of the [Domain Name] domain.
For domain-level rules, architecture, conventions, and dependencies:
- Read [../MyDomainDocs/docs/AGENTS.md]

## Service-Specific Rules
[Any rules unique to this service that override or extend the domain rules]

## Build & Test
[build command]
[test command]

**ALWAYS FOLLOW THESE RULES WHEN YOU WORK IN THIS PROJECT**
```

**Which method to choose:**
- Do you own more than 5 repos that share domain knowledge? **Yes → Method 2.**
- Do your repos share conventions, dependencies, and architecture patterns? **Yes → Method 2. No → Method 1.**
- Unsure? **Start with Method 1** — nothing is wasted; Method 1 docs become the per-service docs referenced from a Method 2 central repo if you consolidate later.

### Documentation Retrofit (Minimum Viable Investment — one-time cost, permanent velocity gains)

**Step 1 — Foundation:** create the `docs/` directory scaffold; write the entry-point steering file with core rules; document the build/test/deploy workflow; copy the steering file to each AI assistant's expected location.

**Step 2 — Architecture & service documentation:** document high-level architecture (C4 Context/Container); document each service/repo; document API patterns and contracts; document the configuration approach.

**Step 3 — Integration & dependency documentation:** create a dependency matrix; document each external dependency; document integration patterns with code examples; document error handling for integrations.

**Step 4 — Validation:** run an AI-readiness assessment; execute a simple task with an AI agent; identify documentation gaps from agent failures; iterate on the steering-file rules.

---

## Part 5: Tooling for Conversion

The irony: use AI to make your codebase AI-ready. The documentation retrofit in Part 3/4 is itself a task AI agents can accelerate significantly. This section covers concrete tools, workflows, and automation to bootstrap AI-readiness — from initial documentation scaffolding to ongoing freshness.

### 5.1 AI-Assisted Documentation Retrofit

An automated setup tool can act as a CLI that automates the initial documentation setup for any repository, in two phases.

**5.1.1 Phase 1 — Coding guidelines, personas, and steering.** The tool analyzes the target project (framework, design system, testing library, build system) and copies only the relevant documentation from a curated template repo:
1. Detects project type (frontend, BFF, backend, fullstack, monorepo, library, infrastructure).
2. Applies relevant guidelines (`docs/guidelines/`) — framework patterns, testing practices, accessibility, security, API integration, etc.
3. Applies relevant persona files (`docs/personas/`) — e.g., Senior Frontend Engineer, Accessibility Auditor, Security Engineer.
4. Applies the steering file (`.assistant/steering/`) — task-based routing that tells the agent which docs to read for each task type.
5. Applies an auto-update hook — keeps docs automatically updated after every interaction.
6. Uses an AI-assisted CLI to customize all placeholders (project name, build commands, design system) by analyzing the actual codebase.

**How to run:**
```
# From your project root
setup-ai-dev-docs
```
The tool handles the full workflow: preflight checks → template copy → AI-powered customization. No manual placeholder replacement needed.

**What you get after Phase 1:**
```
my-service/
├─ .assistant/
│  ├─ steering/
│  │  └─ AGENTS.md                       # Task-based routing to guidelines + personas
│  └─ hooks/
│     └─ auto-update-docs.hook           # Auto-updates docs after each AI interaction
└─ docs/
   ├─ personas/
   │  ├─ senior-frontend.md              # (only if frontend project)
   │  ├─ accessibility-auditor.md        # (only if frontend project)
   │  └─ security-engineer.md            # (always included)
   └─ guidelines/
      ├─ frontend-best-practices.md      # (only if a frontend-framework project)
      ├─ framework-hooks.md              # (only if applicable)
      ├─ api-integration.md              # (only if frontend/fullstack)
      ├─ frontend-testing.md             # (only if frontend/fullstack)
      ├─ component-testing.md            # (only if frontend/fullstack)
      ├─ ux-design-system.md             # (only if frontend/fullstack)
      ├─ component-library-guidelines.md # (only if applicable)
      ├─ accessibility-audit.md          # (only if frontend/fullstack)
      ├─ bff-logging.md                  # (only if BFF/backend)
      └─ security.md                     # (always included)
```
This gives the "Explicit Knowledge" pillar — the agent knows how to write code in your project. It doesn't yet know what your service *does*, who it talks to, or how to operate it — that's Phase 2.

**5.1.2 Phase 2 — Architecture, dependencies, and operations (WIP).** Instead of copying static templates, this phase uses an AI agent to analyze the codebase and generate first drafts:
1. Analyzes infrastructure-as-code, service configs, and existing READMEs to draft `docs/architecture.md` following C4 structure.
2. Scans dependency manifests, service client imports, and integration code to generate `docs/dependencies/README.md` (dependency matrix) plus stub files per detected dependency.
3. Extracts build/test/deploy commands and pipeline references to generate `docs/operations/testing.md`, `docs/operations/build-deploy.md`, and `docs/operations/troubleshooting.md`.
4. Scans database schemas, entity definitions, and model files to draft `docs/data-models.md`.
5. Detects API route definitions and generates OpenAPI contract stubs (`docs/api-contracts.yaml`).
6. Enhances the steering file with a Code-to-Documentation Mapping table based on the actual project structure.

**Key principle:** AI-generated drafts are starting points, not final docs. Humans review, correct, and fill gaps — dramatically faster than writing from scratch, and the agent's analysis often surfaces dependencies/patterns humans forget to document. The tool generates roughly 60–80% of the content.

### 5.2 Documentation Freshness

The biggest risk after a documentation retrofit is **staleness** — docs that don't match code are worse than no docs; they actively mislead agents. An auto-update hook solves this by making documentation maintenance automatic.

**How it works:** the auto-update hook triggers after every AI agent interaction (an "agent stop" event). It reviews what happened in the conversation — code changes, new patterns, architectural decisions — and makes targeted updates to existing documentation files.
```json
{
  "name": "Auto-Update Documentation",
  "version": "1.0.0",
  "when": { "type": "agentStop" },
  "then": {
    "type": "askAgent",
    "prompt": "Review what just happened in this conversation..."
  }
}
```

**What it updates (five categories):**

| # | Category | Example | Doc Updated |
|---|---|---|---|
| 1 | New patterns or conventions | Team adopts a new error-handling pattern | `docs/guidelines/` |
| 2 | Guideline deviations | Intentional deviation from a documented pattern | `docs/guidelines/` |
| 3 | New libraries or dependencies | Added a new service client | `docs/dependencies/` |
| 4 | Architectural decisions | Changed data flow between components | `docs/architecture.md` |
| 5 | New components or utilities | Created a new shared hook | `docs/guidelines`, steering file |

**What it does *not* do:** never creates new files, only updates existing ones; makes minimal, targeted edits, not wholesale rewrites; does nothing (silently) if no update is needed; doesn't update API contracts or data models — those require explicit human review.

**Why this matters:** from the playbook's anti-patterns — "stale documentation is worse than no docs." The hook enforces that documentation updates happen in the same workflow as code changes, rather than as a separate, easily-deprioritized task.

**Extending the hook:** teams can customize its prompt to cover additional doc types, e.g., extending it to also check `docs/operations/` and `docs/dependencies/` once the Phase 2 scaffold exists.

### 5.3 Anti-Patterns to Avoid

| # | Anti-Pattern | Why It Fails | Do This Instead |
|---|---|---|---|
| 1 | Documentation sprawl | More docs ≠ better — agents get confused by contradictory/redundant docs | Keep docs focused, navigable, single source of truth |
| 2 | Stale documentation | Docs that don't match code are worse than no docs | Mandate doc updates in the same commit as code changes |
| 3 | Generic steering-file rules | Vague rules ("write good code") don't constrain agent behavior | Rules must be specific and actionable — e.g., "Use `Result<T, String>` for command returns. Validate inputs at command boundaries." |
| 4 | Missing integration docs | External dependencies are the #1 source of agent confusion | Document every integration with code examples and error handling |
| 5 | No validation | You don't know if docs work until an agent tries to use them | Always test with an agent before declaring readiness |
| 6 | Trusting AI-generated docs without review | AI drafts are 60–80% correct — the remaining 20–40% can actively mislead agents | Always have a human review AI-generated architecture, dependency, and operations docs before merging |
| 7 | One-time retrofit without maintenance | Documentation decays within weeks without active maintenance | Install the auto-update hook and extend it to cover all doc types |
| 8 | Applying all templates | Projects get cluttered with irrelevant guidelines (e.g., frontend docs in a backend service) | Rely on the setup tool's project analysis (5.1) to copy only relevant files |

---

## Part 6: Testing Architecture & Guardrails

### The Testing Pyramid for AI-Ready Applications

AI agents excel at generating tests when the testing infrastructure is well-documented and patterns are explicit. The goal is a testing architecture where agents can autonomously generate and run all test types. Agent confidence — and therefore output quality — varies by test type, level of documentation, and manual steering. **Key insight: the less confident the agent is, the more documentation and steering you need to provide.**

Confidence is determined by two factors: (1) how much context the agent needs to generate a correct test, and (2) how deterministic the environment is — mocked dependencies are predictable, real services are not.

Pyramid, from highest to lowest AI confidence:
- **Unit Tests** — mocked deps, fast/deterministic. High confidence — agent needs code only.
- **API Tests** — against a BFF/domain endpoint, mocked deps. High confidence — needs route config + sample data.
- **UI Tests** — against a local stack, mocked deps. Medium confidence — needs selectors + flows.
- **Integration Tests** — real service dependencies. Medium confidence — needs env + contract docs.
- **E2E Tests** — against staging/pre-prod, real services. Low confidence — needs heavy docs (full journeys, env config, real test data).

Practical implication: invest documentation effort proportional to which test types you want agents to generate. Unit + API tests (high confidence) need only the code plus a testing guide. UI tests need selector conventions and user-flow docs. Integration and E2E tests need environment setup, test-data provisioning, service-dependency docs, and detailed user journeys.

### Test Coverage Targets

| # | Test Type | Coverage Target | Pipeline Gate | Documentation Investment |
|---|---|---|---|---|
| 1 | Unit Tests | >90% line coverage | Block merge if below | Low: `docs/operations/testing.md` |
| 2 | API Tests | All endpoints, success + failure paths | Block merge | Medium: API testing guide + sample data conventions |
| 3 | UI Tests | Core user workflows | Block deploy to staging | Medium: selector conventions + user stories |
| 4 | Integration Tests | Critical service-integration paths | Block deploy to prod | Medium: dependency docs + env setup |
| 5 | E2E Tests | Primary user journeys | Block deploy to prod | High: full journey docs + env config |

### Test Type Definitions for AI Agents

| # | Test Type | What It Is | Dependencies | AI Confidence |
|---|---|---|---|---|
| 1 | Unit Tests | Tests for individual functions/components/hooks | All dependencies mocked | High — agent needs function signatures and business rules |
| 2 | API Tests | Requests against BFF or domain-service endpoints | Mocked external dependencies | High — agent needs route config, sample data conventions |
| 3 | UI Tests | Browser-automation tests against the local stack (frontend + BFF) | BFF external deps mocked, frontend/BFF real | Medium — agent needs selectors, user flows, local-stack setup |
| 4 | Integration Tests | Service-level tests calling actual dependencies | Real services, not mocked | Medium — agent needs service contracts, auth setup, environment config, test-data provisioning |
| 5 | E2E Tests | Browser-automation tests against staging/pre-prod environments, real services | Nothing mocked | Low — agent needs full user journeys, environment URLs, auth, real test data in the target environment |

### What the Agent Needs Per Test Type

**Unit Tests (High Confidence)** — the agent only needs the code itself and basic conventions.
- Docs required: `docs/guidelines.md` (coding patterns, import order); `docs/operations/testing.md` (test framework, mock patterns, file-naming conventions).
- What the agent reads: function signatures, component props, return types — it infers test cases from the code.
```jsx
// Example (component test)
import { render, screen } from '@testing-library/react';
import { JobCard } from './JobCard';

test('should render job title', () => {
  render(<JobCard job={mockJob} />);
  expect(screen.getByText('Software Engineer')).toBeInTheDocument();
});
```

**API Tests (High Confidence)** — the agent needs route configuration, sample-data conventions, and mock-wiring patterns (works for both BFF and domain-service endpoints).
- Docs required: `docs/operations/api-testing.md` (step-by-step guide — route config, sample data files, mock client wiring, test structure); API route configuration (e.g., `routes.json`); sample-data conventions (centralized mock data files per domain).
- What the agent reads: the operation implementation (inputs/outputs), the route config (method, path, required params), and sample-data files (to write correct assertions).
```ts
// Example (BFF API test)
import { app } from './dev-server';
import request from 'supertest';

describe('GET /jobs', () => {
  it('should return 200 with jobs for valid person', async () => {
    const response = await request(app).get('/jobs').set('person-id', 'user123');
    expect(response.status).toBe(200);
    expect(response.body).toHaveProperty('jobs');
    expect(Array.isArray(response.body.jobs)).toBe(true);
  });

  it('should return 400 when required params missing', async () => {
    const response = await request(app).get('/top-candidates');
    expect(response.status).toBe(400);
  });
});
```
A well-documented API testing guide — step-by-step, with sample-data conventions, route-config reference, and a test checklist — is cited as an excellent example of documentation that enables high-confidence AI test generation.

**UI Tests (Medium Confidence)** — the agent needs test-id selectors, user-flow descriptions, and local-stack setup (browser automation against the local dev stack with mocked BFF dependencies).
- Docs required: `docs/operations/ui-tests.md` (test framework setup, test structure, local-stack commands); selector naming conventions in `docs/operations/testing.md`; user stories with acceptance criteria.
- What the agent reads: component code (for selector attributes), user stories (flow steps), and the UI test guide (framework patterns).

Testable vs. non-testable components:
```jsx
// AI-testable: explicit test-id attributes
<button data-testid="submit-job" onClick={handleSubmit}>Submit</button>
<div data-testid="job-card" className="card">...</div>
<span data-testid="candidate-count">{count}</span>

// NOT AI-testable: no selectors
<button onClick={handleSubmit}>Submit</button>
```
```ts
// Example (UI test)
import { test, expect } from '@playwright/test';

test.describe('Homepage', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/hiring');
  });

  test('should display job list', async ({ page }) => {
    await page.waitForSelector('[data-testid="job-card"]', { timeout: 10000 });
    const jobCards = page.locator('[data-testid="job-card"]');
    await expect(jobCards.first()).toBeVisible();
  });

  test('should open job flyout when clicking a job card', async ({ page }) => {
    await page.waitForSelector('[data-testid="job-card"]', { timeout: 10000 });
    await page.locator('[data-testid="job-title"]').first().click();
    await expect(page.locator('[data-testid="job-flyout"]')).toBeVisible({ timeout: 5000 });
  });
});
```

**Integration Tests (Medium Confidence)** — call actual service dependencies (real APIs, real databases, real auth); the agent needs significantly more context because the environment is non-deterministic.
- Docs required: `docs/dependencies/{service}.md` (per-dependency integration guide — auth patterns, expected responses, error codes); `docs/operations/testing.md` (environment setup, test-data provisioning, auth configuration); service contracts (OpenAPI or equivalent); test-data setup/teardown procedures.
- The agent must understand auth flows, service contracts, and environment-specific configuration. With well-documented dependencies and clear test-data setup, agents can generate these tests, but need more steering than unit/API tests.

**E2E Tests (Low Confidence)** — browser automation against staging/pre-prod with real services, nothing mocked; validates complete user journeys through the actual deployed system.
- Docs required: detailed user stories with step-by-step acceptance criteria; environment URLs and auth configuration for staging/pre-prod; test-data provisioning in target environments; a service dependency map (which services must be healthy for the test to pass); selectors (as with UI tests); expected latencies and timeout configuration for real services.
- Why confidence is low: everything is real — real auth, real services, real data, real latency. The agent needs to understand the full system, not just the code. Flakiness risk is highest here.

### Guardrails

**Guardrail 1 — Test Stability.** Flaky tests destroy pipeline confidence:
- Retry policy: UI and E2E tests get 2 retries; failing 3 times counts as a real failure.
- Quarantine: flaky tests are auto-quarantined after 3 flaky runs in 7 days — they don't block the pipeline, but generate a ticket for the owning team.
- No `sleep()`: use explicit waits (`waitForSelector`, `waitForResponse`), never `sleep()`.
- Isolated test data: each test creates its own data, never depends on shared state.

**Guardrail 2 — Test Speed.**

| # | Test Type | Per-Test Target | Suite Target |
|---|---|---|---|
| 1 | Unit Tests | <1s | <2 minutes |
| 2 | API Tests | <5s | <3 minutes |
| 3 | UI Tests | <30s | <10 minutes (parallelized) |
| 4 | Integration Tests | <30s | <10 minutes |
| 5 | E2E Tests | <60s | <15 minutes (parallelized) |

**Guardrail 3 — Coverage Ratchet.** Coverage can only go up, never down:
- Ratchet mechanism: CI records current coverage; any PR that reduces it is blocked.
- New-code requirement: all new code must have ≥90% coverage — no exceptions.
- Legacy code: existing uncovered code isn't retroactively required to be covered, but any modification to it requires adding tests.

**Guardrail 4 — AI Test Review.** AI-generated tests must be validated for:
- **Assertion quality** — meaningful assertions, not just "doesn't throw."
- **Edge cases** — AI must generate tests for error states, empty states, boundary conditions.
- **Independence** — each test must be runnable in isolation.
- **Determinism** — tests must produce the same result every run.

---

## Appendix A: The C4 Model

The C4 model provides a hierarchical approach to software architecture documentation, cleanly separating strategic architectural decisions from detailed implementation specs, across four levels:

1. **Context level** — the highest-level view of the system: how it interacts with users, stakeholders, and external systems. Accessible to both technical and non-technical stakeholders; focuses on system boundaries and external relationships rather than internal implementation.
2. **Container level** — the main components making up the system (web apps, mobile apps, databases, servers). Each container is an independently executable unit implementing part of the system's functionality; shows inter-container interactions and technology choices (REST APIs, messaging, storage).
3. **Component level** — the architecture of software components *within* a container (modules, services, functional units) and how they interact with each other; useful for agents/engineers working on a specific part of the system, with enough detail for implementation planning without overwhelming complexity.
4. **Code level** — the lowest level of design: code structure, class diagrams, database schemas, specific implementation patterns. Provides the technical specification required for autonomous code generation, serving as direct input for both AI agents and human developers doing implementation work.

## Appendix B: AI Readiness Score Example

An example scoring worksheet ("AI Readiness Score") applied to a sample set of domains — illustrative only:

| # | Dimension | What Was Assessed | Score |
|---|---|---|---|
| 2 | Documentation Quality | Tech architecture documentation, readiness-review completion, dev mono-repo docs | *(value not fully legible in the original source)* |
| 3 | Architectural Clarity | Compliance with persona-driven architecture; removes monolithic services; workflow dependencies modeled | 16 |
| 4 | Contract Explicitness | Services with clear responsibility; microservice consolidation | 8 |
| 5 | Test Infrastructure | Leverage test infra for integration tests, UI testing framework, mobile app testing | 6 |
| 6 | Dependency Clarity | Dependency simplification across hubs | 9 |
| 7 | Quality Guardrails | Full CD, test coverage, end-to-end integration test coverage | 10 |

*(The row for dimension 1 and the composite total score were not fully legible in the original source photos — recorded here as a worked example of the scoring rubric from Part 2, not as current data.)*
