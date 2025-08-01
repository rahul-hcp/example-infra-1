# Example Infrastructure 1 - Terraform Stack

This Terraform Stack demonstrates a multi-account deployment pattern using HCP Terraform with AWS IAM roles for security and financial operations scanning.

## Overview

This stack deploys IAM roles across multiple AWS accounts to enable:
- **Security Scanner Role**: Provides read-only access for security scanning tools
- **FinOps Scanner Role**: Provides billing read-only access for financial operations analysis

## Architecture

The stack uses a simple deployment pattern where each target account gets its own deployment configuration with specific role ARNs and tags.

### Components

- **IAM Role Component** (`./iam-role`): Creates conditional IAM roles based on feature flags
  - Security scanner role with `ReadOnlyAccess` managed policy
  - FinOps scanner role with `AWSBillingReadOnlyAccess` managed policy

## File Structure

```
example-infra-1/
├── components.tfstack.hcl      # Component definitions
├── deployments.tfdeploy.hcl    # Deployment configurations
├── providers.tfstack.hcl       # Provider configurations
├── variables.tfstack.hcl       # Stack variables
├── outputs.tfstack.hcl         # Stack outputs
├── iam-role/                   # IAM role module
│   ├── main.tf                 # IAM role resources
│   ├── variables.tf            # Module variables
│   └── outputs.tf              # Module outputs
└── .terraform-version          # Terraform version constraint
```

## Configuration

### Variables

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `region` | string | - | Target AWS region |
| `identity_token` | string | - | JWT identity token for AWS provider (ephemeral) |
| `role_arn` | string | - | Role ARN for AWS provider assumption |
| `enable_security_scanner` | bool | `true` | Enable security scanner IAM role |
| `enable_finops_scanner` | bool | `true` | Enable FinOps scanner IAM role |
| `default_tags` | map(string) | `{}` | Default tags for all AWS resources |

### Deployments

The stack defines two deployments:

1. **Account_Demo1**: Deploys to account `0123456789012`
2. **Account_Demo2**: Deploys to account `987654321234`

Both deployments:
- Target `us-east-1` region
- Use the same role name pattern: `HCPTerraform-Role-StackSet`
- Enable both security and FinOps scanners
- Apply environment-specific tags

### Orchestration

- **Auto-approval**: All plan operations are automatically approved
- **Manual approval**: Apply operations require manual approval (commented example for specific account auto-approval)

## Usage

### Prerequisites

1. HCP Terraform workspace configured
2. AWS accounts with appropriate IAM roles for cross-account access
3. Terraform CLI with Stacks support

### Deployment

1. **Initialize the stack**:
   ```bash
   terraform init
   ```

2. **Plan the deployment**:
   ```bash
   terraform plan
   ```

3. **Apply the changes**:
   ```bash
   terraform apply
   ```

### Customization

To modify the deployment:

1. **Add new accounts**: Update `deployments.tfdeploy.hcl` with additional deployment blocks
2. **Change regions**: Modify the `region` input in deployment configurations
3. **Disable features**: Set `enable_security_scanner` or `enable_finops_scanner` to `false`
4. **Update tags**: Modify `default_tags` in deployment inputs

## Outputs

| Output | Type | Description |
|--------|------|-------------|
| `security_scanner_arn` | string | ARN of the security scanner IAM role |
| `finops_scanner_arn` | string | ARN of the FinOps scanner IAM role |

## Security Considerations

- IAM roles use specific trusted principals for assume role policies
- Security scanner role: `397352175627`
- FinOps scanner role: `912606485646`
- Roles follow least privilege principle with AWS managed policies
- Random suffixes prevent naming conflicts

## Terraform Version

This stack requires Terraform version as specified in `.terraform-version` file.

## Provider Versions

- AWS Provider: `~> 5.7.0`
- Random Provider: `~> 3.5.1`
- Local Provider: `~> 2.4.0`

## Contributing

When making changes:
1. Update variable descriptions if adding new variables
2. Update this README if changing architecture or usage patterns
3. Test deployments in non-production accounts first
4. Follow Terraform best practices for naming and organization
