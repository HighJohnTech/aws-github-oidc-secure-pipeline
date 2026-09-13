# Build Secure AWS Pipelines with GitHub Actions and OIDC

A hands-on DevSecOps project implementing passwordless AWS authentication for GitHub Actions using OpenID Connect (OIDC), AWS STS, scoped IAM permissions, Terraform, and CloudTrail auditing.

The pipeline deploys AWS infrastructure without storing long-lived AWS access keys in GitHub and includes automated deployment, validation, troubleshooting documentation, and secure infrastructure teardown.

## Project Highlights

- Replaced long-lived AWS credentials with GitHub Actions OIDC federation
- Configured AWS STS temporary role assumption
- Restricted IAM trust to the approved GitHub repository and `main` branch
- Used scoped deployment permissions instead of `AdministratorAccess`
- Deployed infrastructure through Terraform and GitHub Actions
- Stored Terraform state in a private, encrypted, versioned S3 bucket
- Verified the assumed AWS identity with `aws sts get-caller-identity`
- Validated AWS-side authentication activity through CloudTrail
- Troubleshot real IAM ARN and EC2 Free Tier deployment failures
- Implemented automated infrastructure teardown and cleanup
- Documented the security model, troubleshooting process, and deployment validation

## Course Attribution

This project began as a fork of LinkedIn Learning's
"Build Secure AWS Pipelines with GitHub Actions and OIDC"
lab by Damien Burks.

The course repository supplied the starter Terraform infrastructure and
reference workflows. We used that environment to implement, update,
test, harden, troubleshoot, audit, and document a current GitHub Actions
OIDC integration with AWS.

## What We Implemented

- GitHub Actions OIDC federation with AWS
- No long-lived AWS access keys stored in GitHub
- Repository and branch-restricted IAM trust
- Temporary AWS STS credentials
- Scoped deployment permissions instead of AdministratorAccess
- Private encrypted/versioned Terraform remote state
- AWS CloudTrail audit verification
- Terraform deployment and automated teardown
- Updated EC2 configuration for current AWS Free Tier eligibility


## Architecture

```text
GitHub Actions
      |
      | OIDC token
      v
GitHub OIDC Provider
      |
      v
AWS STS
      |
      | temporary credentials
      v
GitHubActionsOIDCDeployer
      |
      | scoped IAM permissions
      v
Terraform
      |
      v
AWS Infrastructure
      |
      v
CloudTrail Audit Evidence

```

## Troubleshooting Highlights

During implementation, we resolved:
- An invalid AWS OIDC role ARN configuration
- A Terraform deployment failure caused by an outdated t2.micro Free Tier assumption
- Updated the deployment to use t3.micro for the current AWS account

## Project Documentation

- [Security Model](docs/security-model.md)
- [Troubleshooting](docs/troubleshooting.md)
- [Deployment Validation](docs/deployment-validation.md)


![lil-thumbnail-url]

## Course Description

In this course, join senior cloud security engineer, speaker, instructor, and career advisor Damien Burks  as he shows you how to modernize and secure CI/CD delivery by replacing long‑lived AWS credentials with identity federation through OpenID Connect (OIDC). In today’s cloud environments, traditional secrets‑based authentication increases risk, adds operational overhead, and makes pipelines difficult to audit. Damien covers the essentials of how OIDC works, why AWS advocates for it, and how to implement it step‑by‑step inside GitHub Actions workflows.</p><p>This course is an ideal fit for DevSecOps and DevOps engineers, cloud practitioners, software developers, security engineers, and site reliability engineers.

## Prerequisites

- An [AWS account](https://aws.amazon.com/free/) with admin or sufficient IAM permissions
- A [GitHub](https://github.com) account
- Basic familiarity with Git, Terraform, and AWS concepts

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/build-secure-aws-pipelines-with-github-actions-and-oidc.git
cd build-secure-aws-pipelines-with-github-actions-and-oidc
```

### 2. Development Environment

This repo includes a [Dev Container](.devcontainer/devcontainer.json) configuration that automatically provisions Terraform, TFLint, and Terragrunt. You can use it with:

- **GitHub Codespaces** — click "Code > Codespaces > New codespace" on the repo page.
- **VS Code / Kiro** — install the [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension, then open the repo and select "Reopen in Container."

If you prefer a local setup, install the following manually:

| Tool | Version | Install Guide |
|------|---------|---------------|
| [Terraform](https://developer.hashicorp.com/terraform/install) | >= 1.0 | `brew install terraform` or [download](https://developer.hashicorp.com/terraform/install) |
| [AWS CLI v2](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) | latest | `brew install awscli` or [download](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) |
| [Git](https://git-scm.com/) | latest | `brew install git` or [download](https://git-scm.com/downloads) |

## Project Structure

```
.
├── .devcontainer/          # Dev Container configuration
├── .github/workflows/      # GitHub Actions CI/CD pipelines
│   ├── deploy.yml          # Deploy infrastructure via OIDC
│   └── destroy.yml         # Tear down infrastructure
├── chapters/               # Per-chapter reference files
│   ├── 01_01/deploy.yml    #   Ch1 — deploy with IAM access keys
│   ├── 03_03/deploy.yml    #   Ch3 — deploy with OIDC
│   └── 04_04/policy.json   #   Ch4 — least-privilege IAM policy
├── terraform/
│   ├── main.tf             # VPC, subnet, SG, S3, IAM, EC2 resources
│   ├── variables.tf        # Input variables (region, instance type)
│   ├── provider.tf         # AWS provider & Terraform version constraints
│   ├── backend.tf          # S3 remote state backend config
│   ├── data.tf             # Data sources (AMI, AZs, caller identity)
│   ├── locals.tf           # Computed locals (deployment method detection)
│   ├── outputs.tf          # Outputs (IP, URL, VPC ID, bucket name)
│   ├── user-data.sh        # EC2 bootstrap script (Apache + S3 sync)
│   └── vervium_ui/         # Static website files uploaded to S3
└── README.md
```

## Infrastructure Overview

The Terraform configuration provisions:

- A VPC with a public subnet, internet gateway, and route table
- A security group allowing inbound HTTP (port 80)
- A private S3 bucket containing the Vervium UI static assets
- An IAM role granting the EC2 instance read access to the S3 bucket
- An EC2 instance (Amazon Linux 2023) running Apache, which syncs the UI from S3 on boot

## Recommended Extensions

If you're using VS Code or Kiro, the following extensions are helpful:

- [HashiCorp Terraform](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform) — syntax highlighting, IntelliSense, and formatting for `.tf` files
- [GitHub Actions](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-github-actions) — syntax highlighting and validation for workflow YAML files
- [YAML](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml) — general YAML language support
- [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) — open the repo inside the provided Dev Container

## Cleanup

To destroy all AWS resources created by this project:

```bash
cd terraform
terraform destroy
```

Or trigger the **Destroy Infrastructure** workflow from the GitHub Actions tab.

## Instructor

Damien Burks
Sr. Cloud Security Engineer & Founder of The DevSec Blueprint

Check out my other courses on [LinkedIn Learning](https://www.linkedin.com/learning/instructors/damien-burks).

[0]: # "Replace these placeholder URLs with actual course URLs"
[lil-course-url]: https://www.linkedin.com/learning/build-secure-aws-pipelines-with-github-actions-and-oidc
[lil-thumbnail-url]: https://media.licdn.com/dms/image/v2/D4E0DAQFxa0iR2HnxSA/learning-public-crop_675_1200/B4EZ7wYv2rIAAY-/0/1782149480540?e=2147483647&v=beta&t=T51OoLDiCE5U0QbjvyxRRzo8EJj0zlgSmjvOUQwKelo
