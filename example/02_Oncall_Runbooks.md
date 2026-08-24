# Oncall Runbook — General Best Practices

*A generic on-call runbook template, combining common practices seen across two team-specific runbooks: rotation setup, roles and responsibilities, ticket management standards, and incident (Sev-2) handling — plus a sample weekly report format.*

## Rotation & Prerequisites

**Typical rotation structure:** a weekly rotation with a fixed weekly handover time (e.g., every Thursday morning), tracked in a shared on-call scheduling tool.

**Prerequisites for going on-call:**
- Set up the paging app on your mobile device.
- Ensure access to all team-owned test/staging accounts.
- Ensure access to relevant risk dashboards (security/compliance risk tools, pipeline health dashboards).
- Ensure access to (and membership in) the team's ticket resolver groups.

## Roles & Responsibilities

Some teams split the rotation into a **primary** and **secondary** on-call; others run a single on-call role. Combined responsibilities:

- **Daily ticket updates** — continuously monitor the ticket queue; keep status and correspondence current with the most recent findings.
- **Work prioritization** — follow the prioritization order below; ensures customer escalations and high-severity tickets get handled first.
- **Standup updates** — report what you're working on and add tickets to the current sprint board.
- **Escalation handling** — if a ticket traces to a recent change by a team member, work with them to resolve it; for an urgent high-severity issue, page the team member directly for help.
- **Customer escalations** — work customer/client escalations from chat/email; for multiple simultaneous escalations, communicate in the team channel and ask for help.
- **Pipeline monitoring** — check the team's pipelines regularly (e.g., every 1–2 days) and ensure they're healthy; resolve failing integration tests or loop in the owning team.
- **Risk/compliance review** — review security and compliance risk dashboards on a regular cadence (e.g., every 2 days); either fix flagged risks or file tickets with the proper owners.
- **Backlog ticket creation** — file tickets for any operational-excellence backlog items found during on-call.
- **Runbook updates** — capture new information/procedures learned during on-call.
- **External team support** — respond to other teams' questions about your service (a secondary on-call can help if load is high).
- **Change review** — review change-management requests drafted by feature owners and shadow their deploys.
- **Reporting** — create/update the weekly on-call report (template below); update team dashboards and any goal-tracking numbers.
- **Handover** — outgoing on-call prepares and presents the weekly operational review; incoming on-call reviews high-priority action items from the previous rotation and ensures continuity on ongoing issues.
- **General prioritization** — on-call work always takes priority over project work; if project work can't be completed during on-call, communicate that to your manager.

## Work Prioritization (Highest to Lowest)

1. **High** — critical-severity tickets, production outages, security incidents, urgent customer requests, high-priority action items from handover.
2. **Medium** — unblocking external customer/prod-traffic pipelines; monitoring team service dashboards; medium-severity tickets; routine risk/compliance review.
3. **Low** — everything else in the queue, backlog items, low-priority tasks from the operational review.

## Managing the Ticket Queue

Keep the technical detail in a **worklog** field; use a **correspondence** field for general status updates meant for a broader audience (often auto-distributed to a mailing list).

**Ticket documentation standards:**
- Within a business day of creation, comment with findings so far and next steps.
- Within a business day of a requester asking for a status update, comment with progress and next steps ("still looking" is acceptable, but more precise is preferred).
- Comments must summarize any side communication (verbal, chat, email) for full context and key decisions.
- On handover, the outgoing on-call must summarize background, findings, and next steps — thorough enough that the next on-call doesn't have to re-derive ticket history.
- Worklog should point to (and ideally include snippets of) the logs used to determine root cause.
- Once root-caused, include an ETA for the production fix.
- See "High-Severity Ticket Standards" below for elevated documentation expectations.

**Ticket priority (highest to lowest):**
1. Highest severity trumps everything else.
2. Active escalations.
3. Higher severity outranks lower severity, all else equal.
4. Work that prevents a future high-severity incident (even if the current severity is lower).
5. Number of impacted requests/users — more impact, higher priority.
6. Users blocked from completing a core workflow.
7. Any compliance-driven request with a hard deadline (e.g., a data-subject access request) — these often take multiple days, so start early.
8. Tickets progressing toward a committed SLA due date — priority rises as the due date nears.
9. Older tickets take precedence, all else equal.
10. Longer average requester response time lowers priority.
11. Judgment calls can override the above for unique/extreme situations.

**Cross-team issues:** On-call should dive deep to find root cause even when it's suspected to sit in another team's domain — loop in someone from the owning team to help diagnose.

**De-duplication:** Configure ticket de-dup rules so one root cause doesn't cut a flood of near-identical tickets — e.g., all UI-fatal errors de-dupe into one ticket, all gateway 5xx errors de-dupe into one (regardless of endpoint), all elevated-latency alerts de-dupe into one, all queue-backlog alerts de-dupe into one. De-dup keys typically live alongside the alarm definitions that trigger the tickets.

**When can a ticket be closed?** Only once a root cause is identified **and** a fix has shipped to production. If root-caused but the fix isn't yet live, don't close — prioritize shipping the fix to reduce ticket turnaround.

**Suggested root-cause categories:**

| Root Cause | When to Use |
|---|---|
| Timeout — external dependency | An external/third-party service is slow to respond |
| Latency — internal | An internal service call is slow — e.g., cross-region calls |

**Close-and-reopen:** When a high-severity ticket goes back into alarm and needs to stay in the queue, close and reopen it rather than leaving it open — many alerting systems attach a de-dupe key to the ticket that won't re-trigger correctly on the next alarm unless the ticket was closed first. Typical steps: (1) resolve the ticket, (2) downgrade severity, (3) prepend "[RCA]" to the ticket name, (4) set status back to "assigned."

**Reporting a system outage:** Announce via your team's incident and operational-excellence chat channels using an agreed template.

## High-Severity Ticket Standards

**Responsibilities:**
- The on-call engineer drives timely response, mitigation, resolution, and documentation, engaging required parties.
- The on-call engineer works with their manager to prioritize any follow-up work that couldn't be completed during the incident.
- The on-call engineer's manager verifies the ticket meets documentation standards.
- An **operations lead** for the week after the incident researches and documents a summary for leadership review, and presents it (or delegates).

**Documentation guidance:**
- Use **worklog** for technical/internal detail (e.g., log snippets); use **correspondence** for a public summary aimed at other interested parties.
- Provide regular updates — while customers are impacted or the incident is unmitigated, update at least every **15 minutes**, even just to note you're waiting on another team (pause the cadence once an ETA is known, resume once that ETA arrives). An incident cut off-hours can be treated as one severity level lower until business hours.
- Once impact is mitigated, downgrade severity; updates twice daily (start and near end of the working day) until full resolution.
- Provide a concise announcement early, filled in as details become available, covering: **customer impact** (time window, number of requests/users, when recovery occurred, additional user actions required, data damaged/restored), **root cause** (which component misbehaved, what change triggered it), and **action to prevent** (both immediate mitigation and longer-term prevention).

**Leadership review documentation** (prepared by the operations lead the week after the incident):
- Each incident entry should cover: how the customer was impacted (time window, numbers, additional user actions); root cause (told either as a forward timeline — "we deployed a change that caused X which caused Y" — or a backward "headline" story — "mismatched data because of concurrent-update mishandling caused by a missed edge case" — pick one style, don't mix); action to prevent (both near-term mitigation and longer-term follow-ups, with target timing); how the ticket was detected (automated alert with trigger parameters, or manually reported — note who raised it).
- Writing style: say enough to convey the idea, without inviting needless follow-up questions. Avoid over-emphasizing "we did everything we could" — focus on "this is what we learned."

**Timeline expectations:**
- On-call checks in the moment paging occurs; provides regular updates while researching root cause, engaging other parties, and deciding/implementing rollback or fixes.
- On-call moves the ticket to "pending" before the escalation-timeout point (commonly ~8 hours).
- The manager verifies progress at least once daily (near end of day).
- On-call downgrades severity once mitigation is complete, adds the final summary, and closes the ticket once resolution is complete.
- Be proactive — don't wait for a weekly review to make decisions; that meeting is for retrospective learning, not for decisions that can be made sooner.
- On-call adds ticket details to the weekly review report and walks through their on-call week there.
- The operations lead uses the weekly review to gather clarity for the leadership-level review, and may follow up separately as needed.
- Leadership may hold a quick call to prepare for the review. The operations lead gains consensus on who presents (usually themself, or a delegate) and that person answers follow-up questions live.

**Operations lead selection:** commonly rotates alphabetically by last name among a team's senior engineers/managers; the rotation typically only advances after a week that actually had a qualifying incident — a quiet week just carries the same lead into the following week.

## Pipeline Failure Alerting (Example Pattern)

Configure your CD system to email an operations alias whenever a named pipeline fails to deploy — e.g., a subject line like "[CD] Failed approval workflow on ServiceName," sent from your deployment-notification address to your operations distribution list. Repeat this pattern for every business-critical pipeline.

---

## Sample Weekly On-Call Report (Template)

*A filled-in example structure, generalized from an actual weekly report — useful as a starting template.*

**Summary section:**
- Link to the on-call ticket/SIM for the week.
- Starting ticket count → ending ticket count.
- Incoming tickets by severity (e.g., "Incoming Sev1/2: 0, Incoming Sev3: 1").
- Tickets closed during the week.
- Open security/compliance tickets; open maintenance ("keep the lights on") tickets.
- Highlights and lowlights for the week.

**Report sections to include:**
- **High-severity ticket summaries** — table of create date / ticket / description / assignee / status / notes-fix / impact / root cause / action items.
- **Changes completed** — links to each change/deployment with a one-line summary of what shipped and who reviewed it.
- **Pipeline metrics dashboard** — per-pipeline "days since last merge to prod" vs. "days until policy" (i.e., how close each pipeline is to breaching your org's freshness policy, commonly ~28 days).
- **Lower-severity ticket summaries** — all open lower-severity tickets left at week's end (closed ones can be covered by an automated weekly digest instead, if you have one).
- **Automated status links** — links to any auto-generated per-service health/ops reports your org has, plus dashboards for dependency health.
- **Security assurance** — long-standing/known/intentional security-policy exceptions with notes/fix context and status.
- **Policy/compliance risk table** — by risk category (availability, infrastructure, operations, security, best practices, internationalization, etc.), showing high/medium/low risk counts and acknowledgments.
- **Deprecation alerts** — tracking of any deprecated runtime/library versions nearing end-of-life across your accounts, with an owner and remediation status per alert.
- **External issue tracking** — long-open issues blocked on external teams get logged to a dedicated tracking doc.
- **Tech backlog items** — items raised during the weekly report-review meeting get filed to a backlog folder.
- **Latency tracking** — a weekly table of the top endpoints by average P99 latency, compared against the prior week.
- Consider automating report generation with an internal script so the weekly report is consistent and low-effort to produce.
