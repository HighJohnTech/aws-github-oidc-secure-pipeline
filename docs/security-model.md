# Security Model

## Authentication
GitHub Actions authenticates to AWS using OpenID Connect.

## Trust Boundary
AWS trusts tokens issued by GitHub's OIDC provider.

## Repository Restriction
The IAM role trust policy limits role assumption to the approved
HighJohnTech repository and main branch.

## Credential Model
No long-lived AWS access keys are stored in GitHub.
AWS STS provides short-lived credentials during each workflow run.

## Authorization
The GitHub deployment role uses a scoped deployment policy rather than
AdministratorAccess.

## Infrastructure
Terraform deploys the AWS networking, storage, IAM, and EC2 resources
required by the lab.

## Auditability
AWS CloudTrail provides evidence of STS and API activity associated with
the deployment.