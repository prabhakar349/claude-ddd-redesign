# Metrics Development: Tenets and Best Practices

*A generic guide to developing, launching, and maintaining business metrics — from initial design through org-wide rollout.*

## 1. Metric Tenets

Unless you know better ones:

- **Think like a customer.** We are the primary customers of our own metrics — walk the customer path and think like business owners while building metrics.
- **High standards.** Every metric aims for Readiness Phase 3+, passing all phase checks before advancing.
- **Ownership.** The metric developer owns all components of the metric, from the requirements document to the conflucene.
- **Best practices.** Build metrics using end-to-end best practices — these should guide, not block, the work.
- **Trust.** Accuracy and testing are critical to building customer trust.
- **Design for flexibility and scalability.** Build metrics that adapt fast to new business requirements and are easy to maintain.
- **Self service.** Build self-service tools (tables, reports, SOPs, business logic) so others can answer their own business questions.
- **Business critical.** All tenets apply during business-as-usual operations. In exceptional times (e.g., a major disruption), the core tenets — think like a customer, ownership, and trust — still apply, while others can flex in favor of speed.

## 2. Metric Readiness Phases

### Phase 0 — Design
- Metric Requirements Document (MRD) completed (use your org's MRD template).
- Mock-up shared and reviewed with the team.
- Data exploration completed to validate MRD assumptions about what's feasible.

### Phase 1 — Development
- Data pipeline jobs created and documented (see SQL best practices below).
- Data-quality and testing best practices applied.
- Code review completed.
- Working reporting prototype built (see reporting best practices).
- Metric available for one market/region, for the longest feasible time period (e.g., up to 5 quarters) — long enough to sanity-check the metric across different periods of the year. (A longer history becomes available once the persistent table is created.)
- Review session held with the team/stakeholders to present the metric and gather critical feedback before production.

### Phase 2 — Production Ready
- Persistent table created to store the metric at the most granular level that makes sense (e.g., item level) — see table-creation best practices.
- Metric table backfilled with at least 2 years of history.
- At least 1 level of attribution.
- Report has enough attributes/views to be usable in production.
- Data-quality and testing best practices applied.
- Tier-1 on-call troubleshooting guide created and added to the metric's conflucene.
- Data pipeline jobs have alarms for data delay/failure that cut a ticket for on-call, referencing the troubleshooting steps.
- Metric conflucene page created.
- A dedicated ticket folder created for metric feedback/requests.
- Internal launch announcement sent to the team.

### Phase 3 — Wider Expansion and Attribution
- Metric available across all markets/regions.
- Second level of attribution created for the most important buckets (optional, metric-dependent).
- Slide added to the team's standard reporting deck.
- Quarterly feedback process in place for grooming/prioritizing feedback and requests.
- Internal launch announcement sent to the broader stakeholder org.

### Phase 4 — Monitoring, Alarms, SLA & Callouts
- Tolerance model created, with alarms for out-of-tolerance behavior.
- Metric available on a fixed weekly schedule (e.g., by a set time each Monday) for the prior reporting week.
- Metric pipelines validated for data anomalies/inconsistencies (see data-quality/testing best practices).
- Automated callouts highlighting the main weekly/daily drivers.

## 3. Metric Component Best Practices

### 3.1 SQL Query Best Practices
- Use significant, consistent naming for data pipeline jobs: `TeamPrefix_MetricName_otherIdentifier` (e.g., a job populating a tolerance-bands table should carry that table's name in the job name). If a job populates a table, use the table's name after the metric name.
- Document assumptions and specific query logic directly in the code.
- Only declare the dependencies you actually use in your SQL query.
- Unschedule deprecated jobs immediately to avoid wasting cluster processing power.
- Follow query-optimization and general SQL-writing best-practice references your org maintains.
- No pipeline job should run longer than one hour — if it does, revisit optimization.
- Document important logic changes directly in the code.
- Always have a code review during development.

### 3.2 Metric Table Best Practices
- The goal of a metric's table is to save cluster resources, accelerate deep dives, and support scalability — compute once at the most granular sensible level, store it persistently, and retrieve/aggregate at any level quickly rather than recomputing each time.
- The metric developer has the best context to design the table.
- Use a consistent schema/namespace convention for metric tables across your org.
- Use significant names, e.g. `teamprefix_metricname_other_identifiers`.
- Always set distribution keys and sort keys.
- Follow other table-creation best practices.
- No metric table should exceed a reasonable size ceiling (e.g., 500GB) — if it does, consider redesigning (normalization), dropping unused columns, or applying a data-retention policy.
- The table should sit at the lowest granularity that makes sense for the metric (e.g., item level, view level, plan level, order level) to accelerate root-cause deep dives.

### 3.3 Metric Reporting Best Practices
- All slide reports in the standard deck follow a consistent visualization template.
- Pick one default reporting tool (e.g., a BI/dashboarding platform) org-wide. For major requirements pointing elsewhere, share the use case with the team before falling back to a secondary tool; be frugal issuing viewer licenses for a secondary tool (check whether the user already has one first).
- Follow data-visualization best practices in reports.
- Manage report access via access-control groups in your BI tool.
- Don't over-build reporting features — be clear about the question each new feature answers, and gather feedback via the quarterly feedback process before adding.
- Track report usage; if usage drops to zero, consider re-promoting or deprecating the report.
- Be frugal with report data volume — especially for in-memory/cached datasets in your BI tool; only include data that's actually used, and add granularity on a need-to-have basis (large datasets slow response times).
- Always test a report after any change (see data-quality/testing best practices).
- Always include a "top movers/drivers" view (e.g., top items by attribution bucket, and top categories driving the metric).
- Always include: a data dictionary for new/logic-bearing columns; links to all pipeline jobs; diagrams/explanations for specific business logic; a step-by-step on-call troubleshooting guide for the metric; a pointer to the feedback ticket queue; and the MRD, embedded in the conflucene.

### Data Quality / Testing Checks (referenced throughout)
- Before implementing a query-logic change, snapshot the current output and compare pre/post — verify only the intended columns changed.
- Validate business rules reflected in the output (e.g., if a metric computes a bounded ratio, test that the output never falls outside the expected range).
- Avoid duplication — identify the primary key columns of the query output and confirm no duplicates against that key.
- Check that NULLs are handled correctly, especially for numeric values; remember to cast to decimal before division to avoid truncation, and account for possible divide-by-zero cases.
- Phase 4 readiness expects explicit validations for SLA breaches, inconsistencies, and anomalies.

## 4. Metric Quarterly Feedback Process

- Goal: keep the feedback loop open with customers and prioritize new feature requests for the metric.
- New feedback/requests are logged to the metric's dedicated ticket folder (created during Phase 2).
- After a metric reaches Phase 3, a quarterly session is scheduled to groom and prioritize feedback/requests — audience is the metric's developer/owner plus customers who provided feedback.
- With many requests from different customers, use business judgment to prioritize the most impactful ones and hold a session with that subset.
- If there's little/no feedback, consider proactively soliciting it from top metric users ahead of the session.
- If usage is close to zero, consider deprecating or re-purposing the metric.
