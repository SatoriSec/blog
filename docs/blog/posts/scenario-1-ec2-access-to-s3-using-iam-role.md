---
slug: scenario-1-ec2-access-to-s3-using-iam-role
title: Scenario 1 - EC2 Access to S3 using IAM Role
date: 2023-10-04
tags: ['iam', 's3', 'aws', 'cloud']
---

### Scenario:

<!-- more -->




An application running on an Amazon EC2 instance needs to access an Amazon S3 bucket to read and write files. However, you do not want to hardcode the AWS access key and secret access key in the EC2 instance.


### Solution:


You can use an IAM role to grant the EC2 instance the necessary permissions to access the S3 bucket.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541269428/6c66c016-c081-4a45-aaca-a4646322844f.png)


To set this up, you would create an IAM role with permissions to access the S3 bucket and then attach the role to the EC2 instance. The EC2 instance can then use the role's temporary security credentials to access the S3 bucket.


To create an IAM role and attach it to an Amazon Elastic Compute Cloud (EC2), and then allow the EC2 instance to access an Amazon Simple Storage Service (S3)bucket using the command line. Here's an example of how you might do this:


First of all, please make sure to configure the AWS CLI.


#### Step 1:


Once the CLI is configured, you can create an IAM policy using the `create-policy` command. For example, to create a policy called "s3-access-policy.json" with permissions to access S3, you might use a command like this:



```
aws iam create-policy --policy-name s3-access-policy --policy-document file://s3-access-policy.json

```

The `s3-access-policy.json` file in this example would contain a JSON document describing the permissions that you want to grant to the role. You can find more information on creating policy documents at [Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)


Here's an example policy document that would allow the policy to read and write objects in an S3 bucket called `my-s3-bucket1232023`:



```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": [
            "s3:ListBucket",
            "s3:GetObject",
            "s3:PutObject"
        ],
        "Resource": [
            "arn:aws:s3:::my-s3-bucket1232023",
            "arn:aws:s3:::my-s3-bucket1232023/*"
        ]
    }]
}

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541551664/e31fe828-96aa-4893-899b-178b4e70c42b.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541567347/fb4eb06c-6130-4852-836c-566b0f2fb9b8.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541585492/36e08bf4-50e5-4fd4-83cf-6982a16770e4.png)


#### Step 2:


Create an policy which is an JSON file that defines trust relationship of IAM role like here we are creating `ec2-assume-policy.json` file which would contain a JSON document describing the permissions that you want to grant to the role.


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

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541704503/c67379cf-8979-4cea-b8bc-47db0382d481.png)


#### Step 3:


Once the trust policy has been created, you can create an IAM role using the `create-role` command. you can create an IAM Role using '**create-role**' command from [aws-cli-create-role-command-docs](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-role.html)



```
aws iam create-role --role-name MyEC2RoleToAccessS3 --assume-role-policy-document file://ec2-assume-policy.json

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541756055/ea9422ed-3649-4fc8-a60f-2f09006b1410.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541770541/605f11dc-3eb1-4d2b-94b7-8473cdf42a0b.png)


#### Step 4:


Once the role has been created, you can attach it to an EC2 Instance using the `attach-role-policy` command as seen below.



```
`aws iam attach-role-policy --role-name MyEC2RoleToAccessS3 --policy-arn "arn:aws:iam::642434777320:policy/s3-access-policy"`

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541879302/ec2a8973-1020-4bd9-a22e-1a77e299dece.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541896302/7c6f4173-0275-4fc3-a1e9-7d5a8f76fb8a.png)


#### Step 5:


Call the `create-instance-profile` command, followed by `add-role-to-instance-profile` command to create the IAM Instance profile `MyEC2RoleToAccessS3`



```
aws iam create-instance-profile --instance-profile-name MyEC2RoleToAccessS3InstProfile
aws iam add-role-to-instance-profile --role-name MyEC2RoleToAccessS3 --instance-profile-name MyEC2RoleToAccessS3InstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541931493/edbb3250-319a-4f16-8fda-02cebd8a766f.png)


#### Step 6:


Finally, attach the IAM role to an existing EC2 instance that was originally launched without an IAM role using the `associate-iam-instance-profile` command to attach the instance profile `MyEC2RoleToAccessS3` for the newly created IAM Role, `MyEC2RoleToAccessS3`. For example:



```
aws ec2 associate-iam-instance-profile --instance-id i-0963e29cdd7ce325f --iam-instance-profile Name=MyEC2RoleToAccessS3InstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541972957/60114dd5-6129-4c20-a1f8-c3dca2776841.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673541989704/f3450f9e-c066-47a7-aa7b-7a61d16ef3a4.png)


After performing all these 6 steps, an EC2 instance will be associated using given IAM Role `MyEC2RoleToAccessS3`, and Now, the EC2 instance will have the necessary permissions to access the S3 bucket through the IAM role.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673542010826/76cd342f-49aa-4afc-8ec3-89f73e151546.png)


As we have given permission for only accessing the specific bucket (**my-s3-bucket1232023**), that's why we are only available to access that and not able to access any other bucket except this.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673542033154/96986ae6-0040-4ee7-9e51-ecc9d431c899.png)


