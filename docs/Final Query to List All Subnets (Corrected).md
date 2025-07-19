 ## Final Query to List All Subnets (Corrected)



SELECT
  subnet_id,
  vpc_id,
  availability_zone,
  cidr_block,
  map_public_ip_on_launch
FROM aws_vpc_subnet;



---

## Get column names



SELECT column_name
FROM information_schema.columns
WHERE table_name = 'aws_iam_policy_attachment';



---

##  (Customer-Managed Policies Only)

```
sqlCopyEditSELECT
  name AS policy_name,
  arn AS policy_arn,
  description,
  path,
  create_date
FROM aws_iam_policy
WHERE is_aws_managed = false;
```





---

## API Gateway Rest apis

```
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

---

## Loadbalancer 

> SELECT
> name,
> arn,
> dns_name,
> scheme,
> region
> FROM aws_ec2_application_load_balancer;

+------+-----+----------+--------+--------+
| name | arn | dns_name | scheme | region |
+------+-----+----------+--------+--------+
+------+-----+----------+--------+--------+
> SELECT
> name,
> arn,
> dns_name,
> scheme,
> region
> FROM aws_ec2_application_load_balancer;

+------+-----+----------+--------+--------+
| name | arn | dns_name | scheme | region |
+------+-----+----------+--------+--------+
+------+-----+----------+--------+--------+
> SELECT
> name,
> arn,
> dns_name,
> scheme,
> region
> FROM aws_ec2_network_load_balancer;

+------+-----+----------+--------+--------+
| name | arn | dns_name | scheme | region |
+------+-----+----------+--------+--------+
+------+-----+----------+--------+--------+
---

