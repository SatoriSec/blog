---
title: AWS Steampipe SQL Queries
description: A collection of basic Steampipe SQL queries for AWS resources.
date: 2025-07-18
tags: [steampipe]
---

# AWS Steampipe SQL Queries

This page contains a collection of **working Steampipe SQL queries** for AWS.
These are helpful to understand the basics of querying AWS infrastructure using SQL syntax via Steampipe.
Each query targets a specific AWS resource like IAM, EC2, S3, VPCs, and more.

These queries help you inspect AWS resources using Steampipe.

<!-- more -->

## List IAM Users

```sql
SELECT
  name,
  arn,
  create_date,
  path
FROM aws_iam_user;
```

## List IAM Roles

```sql
SELECT
  name,
  arn,
  assume_role_policy,
  create_date
FROM aws_iam_role;
```

## List VPCs

```sql
SELECT
  vpc_id,
  cidr_block,
  is_default,
  state,
  region
FROM aws_vpc;
```

## List All VPC Subnets

```sql
SELECT
  subnet_id,
  vpc_id,
  availability_zone,
  cidr_block,
  map_public_ip_on_launch
FROM aws_vpc_subnet;
```

## List Security Groups

```sql
SELECT
  group_id,
  group_name,
  description,
  vpc_id,
  region
FROM aws_vpc_security_group;
```

## List S3 Buckets

```sql
SELECT
  name,
  region,
  creation_date
FROM aws_s3_bucket;
```

## List Lambda Functions

```sql
SELECT
  name,
  runtime,
  handler,
  last_modified,
  region
FROM aws_lambda_function;
```

## API Gateway REST APIs

```sql
SELECT
  stage.rest_api_id,
  api.name AS api_name,
  stage.name AS stage_name,
  stage.region,
  'https://' || stage.rest_api_id || '.execute-api.' || stage.region || '.amazonaws.com/' || stage.name AS invoke_url
FROM
  aws_api_gateway_stage AS stage
JOIN
  aws_api_gateway_rest_api AS api
    ON stage.rest_api_id = api.api_id;
```

## Application Load Balancers

```sql
SELECT
  name,
  arn,
  dns_name,
  scheme,
  region
FROM aws_ec2_application_load_balancer;
```

## Network Load Balancers

```sql
SELECT
  name,
  arn,
  dns_name,
  scheme,
  region
FROM aws_ec2_network_load_balancer;
```

## List managed policies

```sql
SELECT
  name AS policy_name,
  arn AS policy_arn,
  description,
  path,
  create_date
FROM aws_iam_policy
WHERE is_aws_managed = false;
```

## LIST EC2 columns

```sql
SELECT
  column_name,
  data_type
FROM information_schema.columns
WHERE table_name = 'aws_ec2_instance';
```

## EC2 and Security Groups

```sql
SELECT
  i.instance_id,
  i.instance_type,
  i.placement_availability_zone as availability_zone,
  i.vpc_id,
  sg_obj ->> 'GroupId' as security_group_id,
  sg_obj ->> 'GroupName' as security_group_name
FROM
  aws_ec2_instance i,
  lateral jsonb_array_elements(i.security_groups) as sg_obj
ORDER BY
  i.instance_id;
```

## Basic EC2 Instance Info

```sql
SELECT
  instance_id,
  instance_type,
  placement_availability_zone as availability_zone,
  instance_state,
  public_ip_address,
  private_ip_address,
  vpc_id,
  region
FROM
  aws_ec2_instance
LIMIT 10;
```

## List VPCs Ordered by Region

```sql
SELECT
  vpc_id,
  cidr_block,
  is_default,
  state,
  region
FROM aws_vpc
ORDER BY
  region, vpc_id;
```

## EC2 Instances with Security Groups

```sql
SELECT
  i.instance_id,
  sg_obj ->> 'GroupId' AS group_id,
  sg_obj ->> 'GroupName' AS group_name
FROM
  aws_ec2_instance i,
  LATERAL jsonb_array_elements(i.security_groups) AS sg_obj;
```

## EC2 Instances with Type and Security Groups

```sql
SELECT
  i.instance_id,
  i.instance_type,
  i.placement_availability_zone AS availability_zone,
  i.vpc_id,
  sg_obj ->> 'GroupId' AS security_group_id,
  sg_obj ->> 'GroupName' AS security_group_name
FROM
  aws_ec2_instance i,
  LATERAL jsonb_array_elements(i.security_groups) AS sg_obj
ORDER BY
  i.instance_id;
```

