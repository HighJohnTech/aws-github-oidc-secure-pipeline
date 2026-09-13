# Troubleshooting

## Issue 1 — Invalid IAM Role ARN

### Error
Could not assume role with OIDC: Request ARN is invalid

### Expected Behavior
GitHub Actions should assume the GitHubActionsOIDCDeployer IAM role.

### Root Cause
AWS_OIDC_ROLE_ARN was configured with an invalid ARN value rather than
the assumable IAM role ARN.

### Fix
Updated the GitHub repository variable with the ARN of
GitHubActionsOIDCDeployer.

### Security Lesson
Authentication configuration should distinguish between IAM policy ARNs,
OIDC provider ARNs, and assumable IAM role ARNs.

---

## Issue 2 — EC2 Instance Not Free Tier Eligible

### Error
The specified instance type is not eligible for Free Tier.

### Expected Behavior
Terraform should create the lab EC2 instance.

### Root Cause
The course configuration used t2.micro, but the current AWS account's
Free Tier eligibility required a supported current instance type.

### Fix
Changed the Terraform instance_type default from t2.micro to t3.micro.

### Security / Operations Lesson
Cloud examples age. Infrastructure-as-code should be validated against
current cloud-account constraints instead of copied blindly.