# AWS Config S3 ResourceAccount Condition Rule

A custom AWS Config rule that checks if IAM policies with S3 actions have the `s3:ResourceAccount` condition key to help defend against S3 bucket sniping-type attacks.
This code is intended to provide a starting point for implementing compliance checks appropriate to your own organisation's context.

The contents of this repo were developed using Amazon Q Developer.

## Overview

This rule evaluates IAM policies, roles, and users to ensure that any policy statements that allow S3 actions include the `s3:ResourceAccount` condition key with a value matching or containing the AWS account ID of the same AWS account as the IAM policy, role, or user. This is a security best practice to guard against scenarios where an entity in your account might be tricked into accessing S3 resources controlled by an attacker.

## Features

- Evaluates managed IAM policies, IAM roles, and IAM users
- Checks both inline policies and attached customer-managed policies
- Ignores AWS-managed policies
- Runs on configuration changes and on a scheduled basis (every 24 hours)

## Deployment

The solution is deployed as a CloudFormation stack:

```bash
aws cloudformation deploy \
  --template-file deploy.yaml \
  --stack-name s3-resource-account-rule \
  --capabilities CAPABILITY_IAM
```

## Architecture

The solution consists of:

1. **Lambda Function** - Evaluates IAM policies for compliance
2. **IAM Role** - Provides permissions for the Lambda function
3. **AWS Config Rule** - Triggers the Lambda function and reports compliance status

## How It Works

The rule checks:

1. If an IAM policy contains S3 actions (actions that start with "s3:")
2. If those actions have a `s3:ResourceAccount` condition that restricts access to the current account
3. Marks resources as:
   - `COMPLIANT` - All S3 actions have proper `s3:ResourceAccount` condition
   - `NON_COMPLIANT` - S3 actions exist without proper `s3:ResourceAccount` condition
   - `NOT_APPLICABLE` - Resource doesn't contain S3 actions or is not an IAM policy/role/user

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License][cc-by-sa].
[![CC BY-SA 4.0][cc-by-sa-image]][cc-by-sa]

[cc-by-sa]: http://creativecommons.org/licenses/by-sa/4.0/
[cc-by-sa-image]: https://licensebuttons.net/l/by-sa/4.0/88x31.png
[cc-by-sa-shield]: https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg

Full license details can be found here: https://creativecommons.org/licenses/by-sa/4.0/legalcode