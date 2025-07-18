---
slug: steampipe
title: Getting Started with Steampipe + AWS on Ubuntu
date: 2025-07-15
tags: [steampipe]
---

# Getting Started with Steampipe + AWS on Ubuntu

## What is Steampipe?

Steampipe is an open-source tool that lets you **query cloud infrastructure, SaaS services, and files using SQL**. It's fast, scriptable, and works in the terminal.

Instead of writing custom scripts to gather data from cloud services like AWS, you can use SQL queries directly—treating your cloud accounts like a database.

---

## Why Use Steampipe for AWS?

- Instant visibility across your AWS accounts
- Easy to write and share SQL queries
- Supports compliance, inventory, and security checks
- Ideal for quick audits and continuous assessment

---

## AWS Plugin Coverage

As of now, Steampipe's AWS plugin supports **300+ AWS resource types** (called "tables"). Examples include:

- `aws_ec2_instance`
- `aws_s3_bucket`
- `aws_iam_user`
- `aws_vpc`
- `aws_security_group`

You can see the full list here: https://hub.steampipe.io/plugins/turbot/aws

---

## What Can You Do for Compliance?

Steampipe supports compliance benchmarks like:

- **CIS AWS Foundations**
- **PCI DSS**
- **SOC 2**
- **HIPAA**
- **NIST 800-53**

You can run compliance checks and generate reports using mods:

```bash
steampipe mod install steampipe-mod-aws-compliance
steampipe check all
```

---

## Prerequisites

- Ubuntu 20.04 or later
- AWS account with access keys

---

## 1. Install AWS CLI

```bash
sudo apt update
sudo apt install unzip curl -y

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
rm -rf awscliv2.zip aws

aws --version
```

---

## 2. Install Steampipe CLI

```bash
sudo /bin/sh -c "$(curl -fsSL https://steampipe.io/install/steampipe.sh)"
steampipe --version
```

---

## 3. Install AWS Plugin for Steampipe

```bash
steampipe plugin install aws
```

---

## 4. Configure AWS CLI Profile

```bash
aws configure --profile aws_dev
```

- Enter your AWS credentials
- Set your default region (e.g. `us-east-1`)
- Choose output format (`json`, `yaml`, etc.)

---

## 5. Configure Steampipe AWS Connection

Steampipe connection settings are stored in:

```bash
~/.steampipe/config/aws.spc
```

This is where you define your profile, region, and other connection settings.  
If the file already exists, edit it to point to your AWS CLI profile.

Example contents:

```hcl
connection "aws_dev" {
  plugin         = "aws"
  profile        = "aws_dev"
  default_region = "us-east-1"
}
```

---

## 6. Run Basic Steampipe Queries

Launch Steampipe shell:

```bash
steampipe query
```

Try:

```sql
-- List EC2 instances
select instance_id, instance_type, state
from aws_ec2_instance;

-- List publicly accessible S3 buckets
select bucket_name, acl
from aws_s3_bucket
where acl like '%PUBLIC%';
```

---

## 7. Run Compliance Checks

Install and run compliance benchmarks:

```bash
steampipe mod install steampipe-mod-aws-compliance
steampipe check all
```

---

