# Interpreting assessment results

This tool is a quick review, not a formal AWS Well-Architected Review. Treat every finding as a prompt to validate the underlying configuration and discuss it with the workload owner. The right decision depends on workload requirements, threat model, recovery objectives, cost model, data residency, and other constraints.

## Report structure

- `assessment_date`, `account_id`, and `regions` identify the scope and time of the run. Protect real report files as sensitive operational data.
- `overall_score` is an aggregate indicator. The assessment averages available pillar scores and caps the result when a critical failure is present. It is not a compliance rating or probability of an incident.
- `score_capped_by_critical_failure` and `critical_failures` flag critical failures that limit the overall score.
- `pillars` contains one summary per Well-Architected pillar. Each has a score, the count of applicable checks, unverified-check count, and detailed findings.
- Each finding includes a check name, status, severity, region, explanatory detail, and sometimes a short list of resource identifiers.

## Status meanings

- **OK** — the check's specific condition was met. This does not prove that the broader design is best-practice or that all resources are covered.
- **WARN** — a potentially relevant gap or partial result was detected. Validate scope and applicability.
- **FAIL** — the check's configured condition was not met. Prioritize review according to actual business impact and risk, rather than status alone.
- **INFO** — informational or not applicable in the observed environment. INFO findings are excluded from the score.
- **UNKNOWN** — the check could not be completed, often because of missing permissions or an API error. UNKNOWN is not a pass; review Lambda logs, scope, and IAM permissions.

Severity (CRITICAL, HIGH, MEDIUM, LOW) is the assessment's weighting for that check. It is a prioritization hint, not a complete risk analysis. Scores are computed from the implemented checks and their configured weights, so compare scores only with awareness of the assessment version and its limitations.

## A practical review workflow

1. Confirm the report covers the intended account and all relevant regions.
2. Investigate UNKNOWN results and validate API access before interpreting scores.
3. For each WARN or FAIL, verify the underlying service configuration directly and determine whether the check is applicable to the workload.
4. Discuss potential tradeoffs and remediation with the workload owner; do not apply changes solely because a finding exists.
5. Record accepted risks, exceptions, and follow-up actions in the team's normal process.
6. Re-run after approved changes and compare reports with the same assessment version and scope.

## Known limitations

Checks are based on a limited inventory of AWS APIs and simplified heuristics. Presence/count-based checks do not necessarily measure coverage, configuration quality, effectiveness, or workload criticality. For example, the presence of alarms does not prove that meaningful metrics or alerting processes exist. An absence of a resource can be entirely appropriate for a workload. Some regional checks may not cover global service behavior, and certain findings depend on configurable runtime or region lists. Review the template and the current AWS documentation before relying on any result.

The report can include resource names and identifiers. Keep real reports private and remove identifying values before sharing examples publicly.
