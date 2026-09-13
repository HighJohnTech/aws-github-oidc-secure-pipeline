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
```

This confirmed that the workflow assumed the intended IAM role.

## Terraform Validation

The GitHub Actions workflow successfully completed:

- Terraform Init
- Terraform Plan
- Terraform Apply

The deployment completed successfully after updating the EC2 instance type to `t3.micro`.

## Infrastructure Validation

The deployment created and verified:

- EC2 web server
- VPC
- Public subnet
- Internet gateway
- Route table
- Security group
- IAM instance profile
- S3 application bucket
- Terraform remote state in S3

## Application Validation

The Terraform output returned the public web-server URL.

The deployed website was opened in a browser to verify that the EC2 instance and deployment configuration were functioning correctly.

## Credential Validation

The GitHub repository does not store permanent AWS credentials such as:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`

Instead, the workflow receives short-lived credentials from AWS STS through OIDC.

## Audit Validation

AWS CloudTrail provides AWS-side audit evidence for the role assumption and deployment activity.

The relevant authentication event is:

`AssumeRoleWithWebIdentity`

## Teardown Validation

After project evidence is collected, the Terraform destroy workflow is used to remove the lab infrastructure so unnecessary cloud resources are not left running.