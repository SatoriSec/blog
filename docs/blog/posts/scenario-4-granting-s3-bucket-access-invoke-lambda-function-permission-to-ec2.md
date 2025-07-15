---
slug: "scenario-4-granting-s3-bucket-access-invoke-lambda-function-permission-to-ec2"
title: |
  Scenario 4 - Granting S3 bucket access & Invoke Lambda function permission to EC2
date: 2022-12-20
tags: ['cloud', 'dynamodb', 'iam', 's3', 'aws']
---

### Scenario:

<!-- more -->




Create an IAM policy that grants read-only access to an Amazon S3 bucket and also invoke a Lambda function from EC2 Instance.


### Solution:


You can use an IAM role to grant S3 bucket access and Invoke Lambda functions permission to the EC2 instance so that the instance can perform the necessary operations.


To set this up, you would create an IAM role with permissions to Amazon Simple Storage Service (S3) bucket and Amazon Lambda Function and then attach the role to the Amazon Elastic Compute Cloud (EC2). The EC2 instance can then use the necessary permission to access these resources.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674062469320/54b7bf7e-af6a-4b8b-83e0-9fe4971c5dd4.png)


First of all, please make sure that the AWS CLI configuration is done successfully.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674062494798/3a1564a0-886c-483b-a5ca-6ecb79f7c16a.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674062523610/641cca34-d72d-45fa-81af-201f4d945d5b.png)


Create a simple Amazon Lambda Function as seen below.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674062636159/09dac6ce-495c-4c4a-9110-30f23f3d92ed.png)


Once the CLI is configured, you can create an IAM policy using the `create-policy` command. For example, to create a policy called `bucket-lambda-access-policy.json` with permissions to access specific bucket and invoke lambda function, you might use a command like this:



```
aws iam create-policy --policy-name S3LambdaAccessPolicy --policy-document file://bucket-lambda-access-policy.json

```

The `bucket-lambda-access-policy.json` file in this example would contain a JSON document describing the permissions that you want to grant to the role. You can find more information on creating policy documents at [Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)


Here's an example policy document that would allow the policy to access a specific Amazon Simple Storage Service (S3) bucket called `my-s3-bucket-08-01-2023`. This policy allows the user to perform various operations on the specified S3 bucket and Invoke a lambda function.


This below policy allows the user to retrieve objects from the specified S3 bucket and list their contents, as well as invoke any Lambda function from EC2 Instance.



```
{
    "Version": "2012-10-17",
    "Statement": [{
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::<bucket-name>/*",
                "arn:aws:s3:::<bucket-name>"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "lambda:InvokeFunction"
            ],
            "Resource": "*"
        }
    ]
}

```

This IAM policy allows the following actions:


1. **GetObject**: Allows the user to retrieve objects from the specified S3 bucket.


2. **ListBucket**: Allows the user to list the contents of the specified S3 bucket.


3. **InvokeFunction**: Allows the user to invoke Lambda functions.




The policy applies to the specified Amazon Simple Storage Service (S3) bucket and all of its contents (denoted by `"arn:aws:s3:::<bucket-name>/*"`), as well as the bucket itself (denoted by `"arn:aws:s3:::<bucket-name>"`). The `"*"` resource under the `"lambda:InvokeFunction"` the action allows the user to invoke any Lambda function.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674062758960/77d8e732-eb20-4db1-b583-c9190e559c6e.png)


Create a policy which is a JSON file that defines the trust relationship of the IAM role like here we are creating `ec2-assume-policy.json` file which would contain a JSON document describing the permissions that you want to grant to the role.


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

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674062828671/ccbbcc09-07b8-43fa-af6a-ef3d736947a3.png)


Once the trust policy has been created, you can create an IAM role using the `create-role` command. you can create an IAM Role using '**create-role**' command from [aws-cli-create-role-command-docs](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-role.html)



```
aws iam create-role --role-name EC2RoleToAcessS3andInvokeLambda --assume-role-policy-document file://ec2-assume-policy.json

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674062971346/dc77094d-791b-4cd4-b4fa-d68b9fd89729.png)


Once the role has been created, you can attach it to an EC2 Instance using the `attach-role-policy` command as seen below.



```
aws iam attach-role-policy --role-name EC2RoleToAcessS3andInvokeLambda --policy-arn "arn:aws:iam::642434777320:policy/S3LambdaAccessPolicy"

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063059938/0f4424af-e160-4ed6-84ab-adb6f611c5ec.png)


call the `create-instance-profile` command, followed by `add-role-to-instance-profile` command to create the IAM Instance profile `EC2RoleToAccessS3bucketLambdaInstProfile`



```
aws iam create-instance-profile --instance-profile-name EC2RoleToAccessS3bucketLambdaInstProfile

aws iam add-role-to-instance-profile --role-name EC2RoleToAcessS3andInvokeLambda --instance-profile-name EC2RoleToAccessS3bucketLambdaInstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063147532/bb2f8466-b73a-429c-932d-bce5598aada7.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063265792/204cfb10-58a4-4d55-948e-e80edb8929e5.png)


Finally, attach the IAM role to an existing EC2 instance that was originally launched without an IAM role using the `associate-iam-instance-profile` command to attach the instance profile `EC2RoleToAccessS3bucketLambdaInstProfile` for the newly created IAM Role, `EC2RoleToAcessS3andInvokeLambda`. For example:



```
aws ec2 associate-iam-instance-profile --instance-id i-04c85291954fe6e5f --iam-instance-profile Name=EC2RoleToAccessS3bucketLambdaInstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063243685/e764bc18-c2d3-42f2-9cb9-93c24907c37f.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063252669/320984d4-9eba-4c5a-b8e2-c3d18e2bd96d.png)


After performing all these 6 steps, an EC2 instance will be associated using the recently created IAM Role, and now the EC2 instance will have the necessary permissions to access the DynamoDB table through the IAM role.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063323819/59dcc565-364b-4d13-b6a1-9d21797d64c6.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063328854/17feb1de-6eef-4212-a281-90e14e52928f.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674063335040/7d06f862-7e89-451e-985c-225ba0f5809c.png)


References:


[aws-lambda-invoke-cli-docs](https://docs.aws.amazon.com/cli/latest/reference/lambda/invoke.html)


