# Deployment and security notes

Review the CloudFormation template and adapt it to your organization's governance before deploying it. The template creates AWS resources; it is not a no-change assessment deployment.

- The assessment function uses read-oriented APIs for inventory checks, writes its report to the dedicated S3 bucket, and can publish an optional SNS summary.
- The template creates IAM roles and policies, Lambda functions, CloudWatch log groups, an S3 bucket and bucket policy, an EventBridge schedule, and optionally an SNS topic/email subscription.
- The report bucket is configured for encryption, versioning, public-access blocking, and TLS-only access, and is retained when the stack is deleted. Review retention and lifecycle requirements for your environment.
- Protect Lambda logs and reports; they may contain account metadata, resource names, and identifiers.
- Inspect the IAM policy and requested CloudFormation capabilities before deployment. Use the least privileges and deployment approvals appropriate to your environment.
- The assessment code uses configurable runtime and low-carbon-region lists. Keep them current using authoritative AWS documentation.
- Validate the template in a test account and review generated findings before using it operationally.
