---
slug: scenario-5-iam-policy-to-access-s3-bucket-objects-iff-mfa-authentication-is-provided
title: Scenario 5 - IAM policy to access S3 bucket objects iff MFA Authentication is provided
date: 2021-04-29
tags: ['iam', 's3', 'dynamodb', 'aws']
---

### Scenario:

<!-- more -->




Create an IAM policy to access Amazon Simple Storage Service (S3) bucket objects iff AWS IAM-Multi-Factor Authentication (MFA) is provided.


### Solution:


MFA (Multi-Factor Authentication) is an extra layer of security used to further protect user accounts in Amazon Web Services (AWS). AWS IAM allows users to set up MFA Authentication policies, which require users to provide an additional form of authentication in addition to their username and password. This additional form of authentication can include a variety of methods such as a one-time password (OTP) or a biometric authentication device. MFA policies can be configured to require MFA authentication for certain actions, such as logging in to the AWS console or accessing sensitive resources. This helps to ensure that only authorized users are able to access and make changes to your AWS infrastructure.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063581348/72535279-48ee-4e30-9ef0-0fe602d3fabd.png)


This policy requires users to authenticate with an MFA device before accessing specific resources.


First of all, please make sure that the AWS CLI configuration is done successfully.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063648972/597c8da5-f60e-4330-b792-d9a8a7c5cf14.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063657064/3b6d6e81-271e-48ec-916d-5e224e6b46b6.png)


Once the CLI is configured, you can create an IAM policy using the `create-policy` command. For example, to create a policy called `s3-mfa-access-policy.json` with permissions to access specific bucket but there is only condition that MFA should be setup and configured. To create a policy, you might use a command like this:



```
aws iam create-policy --policy-name S3MFAPolicy --policy-document file://s3-mfa-access-policy.json

```

The `s3-mfa-access-policy.json` file in this example would contain a JSON document describing the permissions that you want to grant to the role. You can find more information on creating policy documents at [Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)


Here's an example policy document that would allow the policy to access a specific Amazon Simple Storage Service (S3) bucket called `my-s3-bucket-09-01-2023` iff MFA is configured.



```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": "*",
        "Resource": "arn:aws:s3:::my-s3-bucket-08-01-2023/*",
        "Condition": {
            "Bool": {
                "aws:MultiFactorAuthPresent": true
            }
        }
    }]
}

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063778815/377932f8-aec5-4718-8038-e6a718ff703b.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063784172/6093321a-5307-4f4f-aedd-0866ce81e058.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063790974/306b74c5-a7e7-48ce-b050-28217500f554.png)


Create a policy which is a JSON file that defines **the trust relationship of the** IAM role like here we are creating `ec2-assume-policy.json` file which would contain a JSON document describing the permissions that you want to grant to the role.


Here's an example policy document that would allow the role to be assumed by EC2 instances:



```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Principal": {
            "Service": "ec2.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
    }]
}

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063813680/6fe476f3-e76e-4d7f-93d1-cb030692001b.png)


Once the trust policy has been created, you can create an IAM role using the `create-role` command. you can create an IAM Role using the '**create-role**' command from [aws-cli-create-role-command-docs](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-role.html)



```
aws iam create-role --role-name EC2RoleToAccessS3ThroughMFA --assume-role-policy-document file://ec2-assume-policy.json

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063846293/520b032a-72fe-4d81-8135-acc61836bd57.png)


Once the role has been created, you can attach it to an EC2 Instance using the `attach-role-policy` command as seen below.



```
aws iam attach-role-policy --role-name EC2RoleToAccessS3ThroughMFA --policy-arn "arn:aws:iam::642434777320:policy/S3MFAPolicy"

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063864932/c094cfaa-c80c-4058-a0ee-f540f0101f4a.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063874260/e10f3f6a-b198-472a-bb8d-57072baeca9e.png)


call the `create-instance-profile` command, followed by `add-role-to-instance-profile` command to create the IAM Instance profile `EC2RoleToAccessS3ThroughMFAInstProfile`



```
aws iam create-instance-profile --instance-profile-name EC2RoleToAccessS3ThroughMFAInstProfile

aws iam add-role-to-instance-profile --role-name EC2RoleToAccessS3ThroughMFA --instance-profile-name EC2RoleToAccessS3ThroughMFAInstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063893752/cd912119-9cbe-411f-b506-b43340ae7d7b.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063903181/bba22be2-8a63-4d21-8a15-7a8cf3e05337.png)


Finally, attach the IAM role to an existing EC2 instance that was originally launched without an IAM role using the `associate-iam-instance-profile` command to attach the instance profile `EC2RoleToAccessSNSInstProfile` for the newly created IAM Role, `EC2RoleToAccessDynamoDBTable`.



```
aws ec2 associate-iam-instance-profile --instance-id i-04c85291954fe6e5f --iam-instance-profile Name=EC2RoleToAccessS3ThroughMFAInstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063971915/1c329c10-1854-4d5b-93ef-33f84e3096ef.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674064013474/4efed99f-7774-4cc0-8d60-aaa1459b12b7.png)


After performing all these 6 steps, an EC2 instance will be associated using a recently created IAM Role and we can see that the bucket is not shown as it is secured by MFA.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674064003728/7219f532-2f0c-4495-bde0-b72303e71aec.png)


