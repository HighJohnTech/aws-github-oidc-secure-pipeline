# Deployment Validation

This document records the validation steps used to confirm that the AWS deployment pipeline successfully authenticated, deployed infrastructure, and operated without long-lived AWS credentials.

## OIDC Authentication

The GitHub Actions deployment workflow successfully authenticated to AWS using OpenID Connect.

The workflow used:

- GitHub OIDC federation
- AWS STS temporary credentials
- `GitHubActionsOIDCDeployer`
- Repository and branch-restricted IAM trust

The workflow verified its AWS identity using:

```bash
aws sts get-caller-identity