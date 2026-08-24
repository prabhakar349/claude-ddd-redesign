# Cloud Operational Excellence Reference

*A generic reference covering three related practices for running production services at scale: baseline operational best practices ("Ops 101"), an Operational Readiness Review (ORR) program, and a set of "inviolable rules" for high availability.*

## Operational Best Practices (Ops 101)

A baseline reference collecting operational best practices for service owners, intended to help new owners build context on how — and why — services are run at a high operational bar. Many of these lessons are hard-learned; reading them is meant to help new owners avoid recreating the same painful lessons themselves.

**Operational tenets:**
- Operational excellence and security are the most important things we do.
- Customer retention and customer acquisition are equally important — focus on both simultaneously.
- Measure twice, cut once — incident postmortems come at a real cost; it's cheaper to do it right the first time.
- Prevent fires instead of fighting them — stay ahead of customer-impacting issues through comprehensive monitoring, full automation, and relentless follow-up.
- Simple automation enables scale — be intolerant of repetitive manual tasks and build robust tooling.
- Don't let perfect be the enemy of good — ship solutions quickly and incrementally.
- Walk before you run — automation is required to operate at scale, but solid process is the enabler of automation.

### Service Lifecycle Milestones

**Design review** — Once a project is approved, teams produce a high-level technical design document covering how the service will be built: components, functionality, failure behavior, key dependencies/algorithms. These are typically reviewed by senior/principal engineers for design feedback. Engage security early — many operational issues are avoided by a design that focuses on failure isolation and compartmentalization from the start.

**Preparation for service launch** (non-exhaustive, highest-impact milestones):
- Set up production monitoring, alarming, dashboards, and throttling rules before launch.
- Performance and scalability testing — validate both steady-state performance and stress-test to failure; document results and mitigations as part of the operational review.
- Penetration testing — schedule third-party pen testing before launch through your security team.
- Operational readiness review — culminates in a document summarizing your production operational process: software release process, team engagement process, known failure points and mitigations. Reviewed by leadership before launch.

**Continual operational maintenance** (ongoing, non-exhaustive):
- Weekly operational review — audit high-severity events and operational dashboards.
- Game days — simulate high-severity events (power outages, dependency/system failures, security events) to confirm the team can diagnose and escalate correctly.
- Data backup and restore — establish and *test* a recoverable backup/restore process before it's needed.
- Testing and technical debt — don't let the pressure to ship new functionality push testing rigor and tech debt into the background; the cost of an incident that could have been prevented, plus the effort to backfill missing functionality afterward, far outweighs paying down the debt incrementally.

## Operational Readiness Review (ORR) Program

**Overview:** an ORR distills learnings from past operational-availability incidents into a self-assessment checklist, helping teams identify the highest-value observations and best practices while limiting review overhead. It recognizes that services are unique, respects the expertise of each team, and allows teams to resolve observations with alternate solutions where appropriate.

**Definitions:**
- **Best Practice** — an empirically proven approach to software development focused on availability/reliability/burden reduction; not proven by precise quantification, but by observed common use among successful organizations.
- **Observation** — something the ORR review surfaces as an opportunity to improve operational, availability, or resilience posture. Resolved via an Action Item or an approved Exception.
  - **Observations Requiring Attention** — critical, priority observations, tracked and reported on a regular cadence to the service team's leadership until resolved.
  - **Observations Regarding Best Practices** — helpful but not centrally tracked; resolution priority is owned by the individual service team (High/Medium/Low).
- **Exception** — when a service is unique or the team has a better approach than the recommended best practice, reviewers can request an exception from senior leadership. Can apply when an observation doesn't apply to the service, or has been addressed differently or on a different timeline.
- **Action Item** — tracks resolution of an observation. For high-priority observations, the team's leadership gets an automated periodic report to ensure it gets attention; only those linked action items get escalated to leadership.

### Methods to Conduct an ORR
- **A lifecycle-integrated review tool** — increasingly the recommended method, wired into the standard service-lifecycle process rather than run as a standalone exercise.
- **A dedicated readiness-review tool** — owned by a central reliability/incident-prevention team; proactively discovers potential availability concerns and recommends best practices/action items, regardless of a team's operational maturity.
- **Office hours** — some orgs offer scheduled office hours for ORR questions; check whether your org still runs these, or has moved to an async request channel instead.

### ORR Best Practices (General)
- Public-facing, tier-1 launches should complete a "New Public Service" ORR per your org's exception policy.
- Start the review early — questions can surface additional work, so plan for it. A reasonable lead time is several months before first public launch.
- Keep the review's target due date current, aligned with the first public launch date.
- **Completion timeline:** a common pattern is completing the checklist (all questions answered, priority observations resolved) about a month before first public preview (or a couple of months before general availability if there's no preview stage), and holding a review meeting with your review group to discuss discoveries, priority, and closure roadmap.
- All priority observations must be resolved (via action item or exception request) before the due date and before launch — marking the review "complete" does **not** by itself resolve them.
- Public-facing teams should complete at least one **recurring ORR per year** to stay current. A recurring ORR isn't required for every internal service — teams should assess their delivery plan and decide whether breaking out specific components/services helps simplify operations and dependency planning.

### ORR Exception Policy (New Service/Feature Launches)
A central ORR program tracks and follows up on new public-facing launches, ensuring the relevant "new launch" ORR is completed with all priority observations resolved before launch. A team may skip this ORR if, e.g., they've completed a compatible ORR under another template/program, don't fit the public-launch profile, or are launching as a component of a larger launch without significant changes since their original launch.

**Process:**
1. **Leadership approval** — the team documents the exception request and supporting evidence via your org's approval workflow tool; senior-leadership-level approval is typically required.
2. **Exception ticket** — once approved, the team creates a tracking ticket for the exception; the ORR program reviews and approves or follows up.

**Resolving priority observations (general mechanics):** two options — create an **action item** (if work is needed before launch — describe the work in the notes; mark it complete once done, which flips the observation to resolved) or an **exception request** (if the observation applies but can't be resolved before launch — document the reason in the request notes and route for approval). Exception approvers are typically notified **sequentially** — the next approver isn't notified until the previous one approves — up the chain to the highest-level approver, with email notifications at each step.

## The Ten Commandments of Cloud Availability

A set of "inviolable rules" some cloud-service organizations adopt for high availability — of such criticality that breaking them sits outside normal engineering judgment; exceptions require senior review and deliberation, and nobody self-authorizes an exception.

A service that takes this seriously does not take planned downtime, and works hard to avoid unplanned downtime too — availability is a feature customers expect.

1. **Don't bridge multiple regions at the same time.** Regions should operate independently as a core customer promise — if one region has an outage, all others must be unaffected. Customers build architectures that depend on this isolation, so avoid anything that could cause correlated cross-region failures (cross-region runtime dependencies, operational changes that touch multiple regions concurrently).

2. **Deploy frequently and gradually.** Prefer small, frequent batches of change over infrequent large ones — this both aids diagnosis and builds confidence that software can be updated quickly to mitigate emergent security/availability risk. Roll out gradually (small initial slices, cellular architectures, zone-by-zone sequencing) to catch problems before broad impact, with sufficient pause time between steps for issues to surface.

3. **Roll back liberally.** If a problem follows a deployment, roll back first and ask questions later — don't ponder, don't diagnose, just roll back. Use auto-rollback monitors tied to key business metrics (availability, latency) so the system rolls back for you. Watch for anything that could break the ability to roll back.

4. **Own your dependencies.** Every team owns the availability of its service **including the dependencies it takes**. If a dependency has a legitimate problem, the owning team drives engaging that dependency and pushing for resolution — don't shrug and blame the dependency and do nothing. If the problem stems from using a dependency in a way that violates its intent or known best practices, the team is responsible for re-architecting to remove the mismatch. Vet dependencies rigorously up front: if you can't debug it, patch it, or change it yourself, think hard before taking a hard dependency on it.

5. **Do not touch production without a change request.** Most changes ship through automated pipelines, but out-of-band production changes sometimes happen outside normal deployment — these require a formal change-management (CM) request, approved by an experienced reviewer, with many tools additionally requiring a second-person review. A common mistake: treating a not-yet-launched system as "non-production" even though it runs in a shared production environment — blast radius can leak from careless handling of these environments (e.g., a load test that saturates shared production capacity, or a simple mix-up between the live and non-live system). Production is production; every change needs a change request.

6. **Change requests should be rare.** They exist for risky, unusual activity, so the process around them is rigorous. Frequent change requests signal a team frequently doing risky things — an anti-pattern reflecting insufficient investment in safe tooling. The healthiest teams might file one every month or every quarter; a team filing dozens per month is taking on unnecessary risk and will likely cause a customer-impacting event. Using the change-request tool for non-critical operations is also an anti-pattern — it muddies what's actually important, numbing teams and increasing the odds that a truly dangerous request doesn't get sufficient attention.

7. **Own your ops roadmap.** Top priorities, in order, are security, durability, availability, and features — but conversations often focus disproportionately on feature delivery. That's not because leadership secretly only cares about features; it's because the rest is *presumed* to already be happening — the same way you're expected to clean up broken glass without being told. Push back when necessary to protect space to operate successfully; when presented with the real tradeoffs, senior leadership consistently chooses security, durability, and availability first (any exception should be made under full awareness, not in secret). Risks sitting in your backlog for months, or being purely reactive instead of proactively hunting for impending risk (e.g., scaling limits), both indicate you're failing to own your ops roadmap.

8. **Call for help.** Other teams are usually glad to help — everyone shares the same customers and has been through a tough operational event themselves. If you see a lurking operational risk not getting traction, escalate; if you're not comfortable escalating directly, look for a private channel to share risk with a small group of senior engineers who can escalate on your behalf.

9. **Be transparent when a customer-impacting event occurs.** Don't hide incidents hoping to avoid attention — it backfires, costing trust and inviting more oversight instead of less. Every customer-impacting event gets a formal postmortem, completed as soon as possible (1–2 weeks is a common target), with roadmap adjustments to prevent recurrence. The postmortem process is not punitive — everyone reviewing one has been on the other side of writing one before. Transparency is how trust and learning happen, and how you get help.

*(Some orgs print these as a physical poster for team spaces — a good practice for keeping the rules top-of-mind.)*

## External Reference: The  Builders' Library (pointer)

For teams looking for a public, well-maintained collection of this kind of guidance, the ** Builders' Library**  is a good starting point — a set of freely available articles on running services at scale, written by engineers who've operated large-scale systems. Useful topics include:

- Assessing risk to metastable failures ("Metastability: Your Favorite Resilience Mechanism Is Not Enough").
- Preparing a service for zone-level failure modes.
- Designing a service to be testable at full scale via cell-based partitioning.
- Reducing blast radius from a slow dependency by controlling concurrency.
- Giving clients a first-class experience via client libraries.
- Avoiding common pitfalls when deleting data.
- Best practices for a primary service ops dashboard.
