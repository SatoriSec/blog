---
slug: scenario-2-ec2-access-to-sns-topic-to-publish-messages
title: Scenario 2 - EC2 Access to SNS topic to publish messages
date: 2023-01-19
tags: ['iam', 'aws', 'cloud']
---

### Scenario:

<!-- more -->




You want to set up an application running on Amazon Elastic Compute Cloud (EC2) to be able to publish messages to Amazon Simple Notification Service (SNS) topic without hardcoding AWS access key & secret access key.


### Solution:


You can use an IAM role to grant the EC2 instance necessary permissions to publish messages to SNS topic.


To set this up, you would create an IAM role with permissions to publish to Amazon Simple Notification Service (SNS) topic and then attach the role to Amazon Elastic Compute Cloud (EC2), EC2 instance can then use the role's temporary security credentials to publish messages to the SNS topic.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673711640090/e91a32e5-cfd0-42bb-b0f9-4f11b9906cf4.png)


Once the CLI is configured, you can create an IAM policy using `create-policy` command. For example, to create a policy called `sns-publish-policy.json with permission` to publish messages to a specific topic, you might use a command like this:



```
aws iam create-policy --policy-name sns-publish-msg-policy --policy-document file://sns-publish-policy.json

```

The `sns-publish-policy.json` file in this example would contain a JSON document describing the permissions that you want to grant to role. You can find more information on creating policy documents at [**Policies and permissions in IAM**](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)**.** Here's an example policy document that would allow the policy to publish messages in an SNS topic called `my-sns-topic`:



```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sns:Publish",
            "Resource": "arn:aws:sns:us-east-2:642434777320:my-sns-topic"
        }
    ]
}

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673770766415/759509c0-c992-4f14-8b3e-82d25f2426b1.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673770775811/45fb20ad-525d-4d7e-8648-9ee88edd2b2d.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673770783891/c2b99420-20b2-4eab-b21d-da35dce9d7f5.png)


Create a policy which is a JSON file that defines the trust relationship of the IAM role here we are creating an `ec2-assume-policy.json` file which would contain a JSON document describing the permissions that you want to grant to the role.


Here is an example policy document that would allow the role to be assumed by EC2 instances:



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

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673770955786/a858040c-c258-43c2-b602-5b2621b3c336.png)


Once the trust policy has been created, you can create an IAM role using the `create-role` command. you can create an IAM Role using the '*create-role*' command from [AWS-CLI-create-role-command-docs](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-role.html)



```
aws iam create-role --role-name EC2RoleToPublishMessage --assume-role-policy-document file://ec2-assume-policy.json

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673771071889/e74f528f-547e-4b41-82c0-5b469c5b0a84.png)


Once role has been created, you can attach it to an EC2 Instance using `attach-role-policy` command as seen below.



```
aws iam attach-role-policy --role-name EC2RoleToPublishMessage --policy-arn "arn:aws:iam::642434777320:policy/sns-publish-msg-policy"

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673771215847/40b7a102-ff4d-4578-9703-8625990685e0.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673771320717/65c54371-680a-4e83-bf7b-511b1457773e.png)


firstly, call `create-instance-profile` cli-command followed by `add-role-to-instance-profile` command to create IAM Instance profile **EC2RoleToAccessSNSInstProfile**



```
aws iam create-instance-profile --instance-profile-name EC2RoleToAccessSNSInstProfile

aws iam add-role-to-instance-profile --role-name EC2RoleToPublishMessage --instance-profile-name EC2RoleToAccessSNSInstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673771404352/163e8794-bab2-4ec4-b429-2abc47d2b81a.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673771453445/8377a27e-a951-4fc9-9430-57ee867a608b.png)


Finally, attach the IAM role to an existing EC2 instance that was originally launched without an IAM role using the `associate-iam-instance-profile` command to attach the instance profile `EC2RoleToAccessSNSInstProfile` for the newly created IAM Role, `EC2RoleToPublishMessage`. For example:



```
aws ec2 associate-iam-instance-profile --instance-id i-04c85291954fe6e5f --iam-instance-profile Name=EC2RoleToAccessSNSInstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673771527348/48f78d62-c03f-4c18-b064-ae4268b67017.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673771532698/6882a345-5679-43c7-9b69-a26b52e5c434.png)


After performing all these steps, EC2 instance will be associated using given IAM Role, and now EC2 instance will have necessary permissions to publish messages to the SNS topic through IAM role.


Below is the SNS topic from which messages will be published.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673771564849/864bc538-455f-4f7e-b698-0375904b02ea.png)


Remove access and secret access keys from EC2 Instance by removing contents from ./aws/config and /.aws/credentials files, and then check the error that we will be getting for now & please remember that while using the AWS-CLI commands use the **--region** parameter and mention the region where resources are deployed.



```
aws sns publish --region us-east-2 --topic-arn "arn:aws:sns:us-east-2:642434777320:my-sns-topic" --message "Hello World, Let's Learn IAM"

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673771831698/4b956ca7-737f-4de0-9670-798e25873fd9.png)


After running above command, we are getting MessageId as response which means message is published from the SNS topic successfully.


References:


[aws-cli-iam-docs](https://docs.aws.amazon.com/cli/latest/reference/iam/) [aws-cli-sns-publish-docs](https://docs.aws.amazon.com/cli/latest/reference/sns/publish.html)


