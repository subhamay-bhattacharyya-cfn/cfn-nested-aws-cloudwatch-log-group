# CloudFormation CloudWatch Log Group Template Repository

<!-- Row 1: Status - Most Important -->
[![Release](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group/actions/workflows/release.yaml/badge.svg)](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)&nbsp;[![GitHub Repo](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)&nbsp;[![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group/issues)&nbsp;[![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group/commits)

<!-- Row 2: Code Quality -->
[![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)&nbsp;[![Commits](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group/commits)

<!-- Row 3: Tech Stack -->
[![CloudFormation](https://img.shields.io/badge/CloudFormation-IaC-orange?logo=amazon&logoColor=white)](https://aws.amazon.com/cloudformation/)&nbsp;[![CloudWatch Logs](https://img.shields.io/badge/CloudWatch_Logs-Monitoring-blue?logo=amazon&logoColor=white)](https://aws.amazon.com/cloudwatch/)&nbsp;[![Built with Claude Code](https://img.shields.io/badge/Built_with-Claude_Code-D97757?logo=anthropic&logoColor=white)](https://claude.ai/)

<!-- Row 4: Repository Info -->
[![Files](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)&nbsp;[![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)&nbsp;[![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group)](https://github.com/subhamay-bhattacharyya-cfn/cfn-nested-aws-cloudwatch-log-group/releases)

<!-- Row 5: Custom Metrics -->
[![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/3226d577c398433b77e5e326acb1cc91/raw/cfn-nested-aws-cloudwatch-log-group.json)](https://gist.github.com/subhamay-bhattacharyya/3226d577c398433b77e5e326acb1cc91)

This repository contains a nested CloudFormation template for deploying CloudWatch Log Groups with flexible configuration, security best practices, and support for log retention and KMS encryption.

## Overview

This is a **nested stack template** designed to be invoked from a parent/root CloudFormation stack. The template is stored in this repository and should be uploaded to an S3 bucket for reference by parent stacks.

## Template Files

### CloudFormation Templates

- **`cloudformation/template.yaml`** — Nested template for CloudWatch Log Group creation with support for:
  - Configurable log retention periods
  - KMS encryption with alias or ARN support
  - Hierarchical path-based naming convention
  - Environment and region tagging
  - Optional CI/CD suffix for ephemeral deployments
  - Auto-tagging for resource management

### Parameter Files

- **`cloudformation/parameters.json`** — Parameter definitions for CloudWatch Log Group
- **`cloudformation/stack-config.json`** — Stack configuration metadata

## Template Features

### CloudWatch Log Group Template

- ✅ **Configurable Retention** — Set log retention from 1 to 3653 days
- ✅ **KMS Encryption** — Support for customer-managed or AWS-managed keys
- ✅ **Smart Naming** — Hierarchical path-based naming with project, base name, environment, and region
- ✅ **CI/CD Integration** — Optional suffix support for ephemeral test deployments
- ✅ **Auto-Tagging** — Automatic tags for environment, project name, and CloudFormation management
- ✅ **Flexible Configuration** — All parameters optional with sensible defaults
- ✅ **Validation** — CloudFormation Guard compliance for retention periods

## Parameters

### Log Group Naming & Environment

| Parameter | Type | Default | Description |
| ----------- | ------ | --------- | ------------- |
| `ProjectName` | String | `proj-ztc` | Project name prefix (lowercase, alphanumeric, hyphens only) |
| `LogGroupBaseName` | String | `application-logs` | Base name for the CloudWatch Log Group |
| `Environment` | String | `devl` | Deployment environment (devl, stag, prod, etc.) |
| `CiSuffix` | String | `""` | Optional CI suffix to append to log group path (e.g., pipeline ID) |

### Retention & Encryption

| Parameter | Type | Default | Description |
| ----------- | ------ | --------- | ------------- |
| `RetentionInDays` | Number | `7` | Log retention period (1, 3, 5, 7, 14, 30, 60, 90, 120, 150, 180, 365, 400, 545, 731, 1827, 3653) |
| `KmsKeyIdOrAlias` | String | `""` | KMS key for encryption (alias: `alias/my-key` or ARN: `arn:aws:kms:region:account:key/key-id`; empty for AWS-managed encryption) |

### KMS Key Format

The `KmsKeyIdOrAlias` parameter supports KMS key encryption for CloudWatch Log Groups.

**Required Format: KMS Key ARN Only**
```
arn:aws:kms:us-east-1:123456789012:key/12345678-1234-1234-1234-123456789012
```

⚠️ **Note:** CloudWatch Logs encryption context requires the full KMS key ARN. Key aliases (e.g., `alias/my-key`) are **not supported** for CloudWatch Log Group encryption.

**AWS-Managed Encryption (Default)**
Leave `KmsKeyIdOrAlias` empty or omit it to use AWS-managed encryption (no customer-managed key required).

**KMS Key Policy Requirements:**

Ensure your KMS key policy includes these statements:

1. **Encryption/Decryption Access:**
```json
{
  "Sid": "Allow CloudWatch Logs to use the key",
  "Effect": "Allow",
  "Principal": {
    "Service": "logs.amazonaws.com"
  },
  "Action": [
    "kms:Encrypt",
    "kms:Decrypt",
    "kms:ReEncrypt*",
    "kms:GenerateDataKey*",
    "kms:DescribeKey"
  ],
  "Resource": "*",
  "Condition": {
    "ArnLike": {
      "kms:EncryptionContext:aws:logs:arn": "arn:aws:logs:region:account:*"
    }
  }
}
```

2. **Grant Management:**
```json
{
  "Sid": "Allow CloudWatch Logs to create grants",
  "Effect": "Allow",
  "Principal": {
    "Service": "logs.amazonaws.com"
  },
  "Action": [
    "kms:CreateGrant",
    "kms:ListGrants",
    "kms:RevokeGrant"
  ],
  "Resource": "*",
  "Condition": {
    "Bool": {
      "kms:GrantIsForAWSResource": "true"
    }
  }
}
```

**Troubleshooting KMS Errors:**
- Use the **full KMS key ARN** (not alias)
- Ensure the KMS key exists in the **same region** as the log group
- Verify the KMS key policy includes CloudWatch Logs service principal permissions
- Check that encryption context conditions are properly configured

## Outputs

### CloudWatch Log Group Template Outputs

| Output | Type | Description |
| ----------- | ------ | ------------- |
| `LogGroupName` | String | Name of the created CloudWatch Log Group |
| `LogGroupArn` | String | ARN of the CloudWatch Log Group |
| `LogGroupPath` | String | Full path of the CloudWatch Log Group |
| `RetentionInDays` | Number | Configured retention period |

## Usage

### 1. Upload Template to S3

```bash
aws s3 cp cloudformation/template.yaml s3://your-cfn-bucket/templates/cw-log-group.yaml
```

### 2. Reference from Parent Stack

In your parent/root CloudFormation template:

```yaml
CloudWatchLogGroupNestedStack:
  Type: AWS::CloudFormation::Stack
  Properties:
    TemplateURL: https://s3.amazonaws.com/your-cfn-bucket/templates/cw-log-group.yaml
    Parameters:
      ProjectName: !Ref ProjectName
      LogGroupBaseName: application-logs
      Environment: !Ref Environment
      RetentionInDays: "7"
      KmsKeyIdOrAlias: ""
    Tags:
      - Key: Environment
        Value: !Ref Environment

Outputs:
  LogGroupName:
    Value: !GetAtt CloudWatchLogGroupNestedStack.Outputs.LogGroupName
  LogGroupArn:
    Value: !GetAtt CloudWatchLogGroupNestedStack.Outputs.LogGroupArn
```

### 3. Deploy Using AWS CLI

#### Example 1: Basic Log Group (7-day retention)

```bash
aws cloudformation create-stack \
  --stack-name myapp-logs-dev \
  --template-body file://cloudformation/template.yaml \
  --parameters \
    ParameterKey=ProjectName,ParameterValue=myapp \
    ParameterKey=LogGroupBaseName,ParameterValue=application-logs \
    ParameterKey=Environment,ParameterValue=devl \
    ParameterKey=RetentionInDays,ParameterValue=7
```

#### Example 2: Production Log Group (30-day retention, KMS encrypted)

```bash
aws cloudformation create-stack \
  --stack-name myapp-logs-prod \
  --template-body file://cloudformation/template.yaml \
  --parameters \
    ParameterKey=ProjectName,ParameterValue=myapp \
    ParameterKey=LogGroupBaseName,ParameterValue=production-logs \
    ParameterKey=Environment,ParameterValue=prod \
    ParameterKey=RetentionInDays,ParameterValue=30 \
    ParameterKey=KmsKeyIdOrAlias,ParameterValue=alias/my-logging-key
```

#### Example 3: Application-Specific Log Group (14-day retention)

```bash
aws cloudformation create-stack \
  --stack-name myapp-logs-app \
  --template-body file://cloudformation/template.yaml \
  --parameters \
    ParameterKey=ProjectName,ParameterValue=myapp \
    ParameterKey=LogGroupBaseName,ParameterValue=api-server \
    ParameterKey=Environment,ParameterValue=devl \
    ParameterKey=RetentionInDays,ParameterValue=14
```

#### Example 4: With Custom KMS Encryption (ARN)

```bash
aws cloudformation create-stack \
  --stack-name myapp-logs-secure \
  --template-body file://cloudformation/template.yaml \
  --parameters \
    ParameterKey=ProjectName,ParameterValue=myapp \
    ParameterKey=LogGroupBaseName,ParameterValue=secure-logs \
    ParameterKey=Environment,ParameterValue=prod \
    ParameterKey=RetentionInDays,ParameterValue=90 \
    ParameterKey=KmsKeyIdOrAlias,ParameterValue=arn:aws:kms:us-east-1:123456789012:key/12345678-1234-1234-1234-123456789012
```

#### Example 5: With CI Suffix for Ephemeral Deployments

```bash
aws cloudformation create-stack \
  --stack-name myapp-logs-ci \
  --template-body file://cloudformation/template.yaml \
  --parameters \
    ParameterKey=ProjectName,ParameterValue=myapp \
    ParameterKey=LogGroupBaseName,ParameterValue=test-logs \
    ParameterKey=Environment,ParameterValue=test \
    ParameterKey=CiSuffix,ParameterValue=$CI_PIPELINE_ID \
    ParameterKey=RetentionInDays,ParameterValue=1
```

#### Monitoring Stack Creation

```bash
# Wait for stack creation to complete
aws cloudformation wait stack-create-complete --stack-name myapp-logs-dev

# Get stack outputs
aws cloudformation describe-stacks \
  --stack-name myapp-logs-dev \
  --query 'Stacks[0].Outputs' \
  --output table

# Get log group details
LOG_GROUP_NAME=$(aws cloudformation describe-stacks \
  --stack-name myapp-logs-dev \
  --query 'Stacks[0].Outputs[?OutputKey==`LogGroupName`].OutputValue' \
  --output text)

aws logs describe-log-groups --log-group-name-prefix "$LOG_GROUP_NAME"
```

## Log Group Naming Convention

The template generates log group paths using the following pattern:

**Without CI Suffix:**

```bash
/{ProjectName}/{LogGroupBaseName}/{Environment}/{Region}
```

Example: `/myapp/application-logs/devl/us-east-1`

**With CI Suffix:**

```bash
/{ProjectName}/{LogGroupBaseName}/{Environment}/{Region}/{CiSuffix}
```

Example: `/myapp/application-logs/test/us-east-1/pipeline-12345`

## Best Practices Implemented

- ✅ KMS encryption support for sensitive logs
- ✅ Hierarchical naming convention for log organization
- ✅ Configurable retention policies for cost optimization
- ✅ Project-based isolation and tagging
- ✅ Environment-aware configuration
- ✅ Optional CI/CD suffix support for ephemeral deployments
- ✅ Auto-tagging for resource management and billing allocation
- ✅ CloudFormation Guard compliance for retention periods
- ✅ Support for AWS-managed and customer-managed encryption keys

## Supported Retention Periods

AWS CloudWatch Logs only supports specific retention periods:

```text
1, 3, 5, 7, 14, 30, 60, 90, 120, 150, 180, 365, 400, 545, 731, 1827, 3653 days
```

The template validates that the provided retention period is one of these supported values.

## License

MIT
