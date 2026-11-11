# WAF Quick Review Architecture

A lightweight AWS Well-Architected Framework (WAF) quick-review assessment across the six pillars: Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, and Sustainability.

The assessment is intended as a **starting point for discussion**, not a formal AWS Well-Architected Review, compliance audit, penetration test, or substitute for professional advice. Results are account- and workload-dependent; interpret findings in the context of business requirements, architecture, risk tolerance, and applicable regulations.

## What it does

The CloudFormation solution deploys an assessment Lambda function and supporting resources. The function calls AWS APIs to inspect selected account and regional settings, creates a JSON report in its dedicated S3 bucket, and can optionally send a summary through SNS email. It can run after deployment, on a monthly EventBridge schedule, or through a manual asynchronous Lambda invocation.

Most assessment API calls are read-only. **Deploying the stack does create and manage AWS resources**, including IAM roles and policies, Lambda functions, CloudWatch log groups, an S3 report bucket, an EventBridge rule, and (optionally) an SNS topic/subscription. Review the template and permissions before deployment. The report bucket is retained on stack deletion by default.

## Repository contents

- `01 - Framework - WAF.yaml` — CloudFormation template for the assessment.
- `02 - Results JSON.json` — sanitized example report; account identifiers and resource-specific values have been removed or generalized.
- `docs/interpreting-results.md` — general guidance for reading the report.

## Prerequisites

- An AWS account and permission to create the resources defined in the template.
- CloudFormation deployment permissions, including IAM capability acknowledgement (`CAPABILITY_IAM`).
- AWS CLI configured for the target account if deploying via CLI.

## Deploy

Review the CloudFormation template, then deploy it from the AWS Console or with the AWS CLI. Example:

```bash
aws cloudformation deploy \
  --template-file "01 - Framework - WAF.yaml" \
  --stack-name waf-quick-review \
  --capabilities CAPABILITY_IAM \
  --parameter-overrides RunOnDeploy=true
```

Set `RunOnDeploy=false` to skip the initial assessment. `AssessmentRegions` accepts a comma-separated region list without spaces; when empty, the stack region is assessed. `NotificationEmail` is optional and requires confirming the SNS email subscription before notifications can be received.

The stack outputs include the report bucket and a command for manually invoking the assessment. Reports are written under `assessments/` in the bucket. Invocation and report-generation are asynchronous; allow a few minutes for a report to appear.

## Important limitations

- The checks are deliberately limited, heuristic indicators. A passing result does not establish that a workload is secure, resilient, compliant, or well-architected.
- Some checks infer maturity from resource presence or counts and may not reflect configuration quality, coverage, or workload criticality.
- Regional checks run only in the regions configured for the assessment. Some services and checks are global or use a designated API region.
- Runtime deprecation and low-carbon region lists are configurable inputs and should be checked against current AWS information.
- API permission failures may produce `UNKNOWN` findings. Review the Lambda logs and role permissions; do not treat unknown as pass.
- The example report is illustrative and does not describe any particular customer environment.

## Security and privacy

The assessment report can contain account and resource metadata. Restrict access to the report bucket and logs, and handle generated reports according to your organization's data policies. The sample JSON in this repository has been sanitized. Do not commit real customer reports, account IDs, public IP addresses, resource names, or other sensitive information.

See [the MIT License](LICENSE). Contributions and issues are welcome.
