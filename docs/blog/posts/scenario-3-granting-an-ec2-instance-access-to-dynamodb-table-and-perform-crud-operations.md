---
slug: "scenario-3-granting-an-ec2-instance-access-to-dynamodb-table-and-perform-crud-operations"
title: |
  Scenario 3 - Granting an EC2 Instance access to DynamoDB Table and perform CRUD Operations
date: 2023-02-07
tags: ['iam', 'dynamodb', 'aws', 'cloud']
---

### Scenario:

<!-- more -->




Creating an IAM policy that grants access to an Amazon DynamoDB table for an Amazon Elastic Compute Cloud (EC2) and also performs CRUD operations using AWS-CLI.


### Solution:


You can use an IAM role to grant the EC2 instance the necessary permissions to access the DynamoDB table.


To set this up, you would create an IAM role with permissions to access the Amazon DynamoDB table and then attach the role to the Amazon Elastic Compute Cloud (EC2). The EC2 instance can then use the role's temporary security credentials to the DynamoDB table.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674060656152/5d3831fb-619a-444c-8d0c-dcfb2c109197.png)


First of all, please make sure that the AWS CLI configuration is done successfully.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674060686818/d1b04414-9b4a-4945-b4d3-860ed9c7337e.png)


Below is the DynamoDB table with some items already created on which we are going to perform CRUD operations. [Create an Item to a table using the console or AWS CLI](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/getting-started-step-2.html).


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674060743916/e39c2637-874a-4800-871b-722cb7cad234.png)


Once the CLI is configured, you can create an IAM policy using the `create-policy` command. For example, to create a policy called `dynamodb-access-policy.json with permission` to publish messages to specific topic, you might use a command like this:



```
aws iam create-policy --policy-name DynamoDBAccessPolicy --policy-document file://dynamodb-access-policy.json

```

The `dynamodb-access-policy.json` file in this example would contain a JSON document describing the permissions that you want to grant to the role. You can find more information on creating policy documents at [Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)


Here's an example policy document that would allow the policy to access a specific DynamoDB table called `my-dynamodb-table`. This policy allows the user to perform various operations on the specified DynamoDB table, including retrieving, adding, updating, and deleting items (CRUD) from the specific table.



```
{
    "Version": "2012-10-17",
    "Statement": [{
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem"
            ],
            "Resource": "arn:aws:dynamodb:<region>:<account-id>:table/my-dynamodb-table"
    }]
}

```

This IAM policy allows the following actions:


1. **GetItem**: Allows the user to retrieve a single item from the specified DynamoDB table.


2. **PutItem**: Allows the user to add a new item to the specified DynamoDB table.


3. **UpdateItem**: Allows the user to update an existing item in the specified DynamoDB table.


4. **DeleteItem**: Allows the user to delete an item from the specified DynamoDB table.




The policy applies to the specified DynamoDB table (denoted by `"arn:aws:dynamodb:<region>:<account-id>:table/my-dynamodb-table"`).


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674060826517/0728344e-f760-4bad-a593-0f10ce4c4c18.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674060842041/3fb08ff2-770d-495b-9892-66e60eadb9f2.png)


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

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061067092/7eaef4c1-c39d-48bb-8911-2486bd68af7a.png)


Once the trust policy has been created, you can create an IAM role using the `create-role` command. you can create an IAM Role using the '**create-role**' command from [aws-cli-create-role-command-docs](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-role.html)



```
aws iam create-role --role-name EC2RoleToAccessDynamoDBTable --assume-role-policy-document file://ec2-assume-policy.json

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061117133/d1d6f7e6-13b7-475a-8eeb-af839fc24551.png)


Once the role has been created, you can attach it to an EC2 Instance using the `attach-role-policy` command as seen below.



```
aws iam attach-role-policy --role-name EC2RoleToAccessDynamoDBTable --policy-arn "arn:aws:iam::642434777320:policy/DynamoDBAccessPolicy"

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061162741/87213d03-d64a-4f87-a8dc-33c3786fbbb3.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061171621/5a29f4dd-ad03-4101-8ecd-c951d49095ac.png)


call the `create-instance-profile` command, followed by `add-role-to-instance-profile` command to create the IAM Instance profile `EC2RoleToAccessSNSInstProfile`



```
aws iam create-instance-profile --instance-profile-name EC2RoleToAccessDynamoDBTableInstProfile

aws iam add-role-to-instance-profile --role-name EC2RoleToAccessDynamoDBTable --instance-profile-name EC2RoleToAccessDynamoDBTableInstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061223861/acc3c3bd-4755-4879-8cff-8eeb4586c87e.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061203584/40cc9068-a988-4dbf-a323-c483127aa724.png)


Finally, attach the IAM role to an existing EC2 instance that was originally launched without an IAM role using the `associate-iam-instance-profile` command to attach the instance profile `EC2RoleToAccessSNSInstProfile` for the newly created IAM Role, `EC2RoleToAccessDynamoDBTable`.



```
aws ec2 associate-iam-instance-profile --instance-id i-04c85291954fe6e5f --iam-instance-profile Name=EC2RoleToAccessDynamoDBTableInstProfile

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061230168/9bdce9fa-7200-4956-9d73-9e6951a6cec7.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061306557/e31d6bb8-1b21-452b-88af-85d0f2636eec.png)


After performing all these 6 steps, an EC2 instance will be associated using the recently created IAM Role, and now the EC2 instance will have the necessary permissions to access the DynamoDB table through the IAM role.


Let's now perform the CRUD operation on a specific table in DynamoDB.


1. ***GetItem:*** This operation allows you to retrieve an item or multiple items from a DynamoDB table.



```
# Get Item (retrieving existing items from the table) 

aws dynamodb get-item --table-name my-dynamodb-table --region us-east-2 --key '{"Id": {"N": "104"}, "Name": {"S": "Andrew"}}'

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061374186/661d3147-a85d-417c-b273-b84b57c87a03.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061673505/0a4ecd94-f2f6-4eff-ad91-9c5a6b06006e.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061402899/efd520e2-8de2-4f04-b9dc-9de8da235a7c.png)


1. ***PutItem:*** This operation allows you to add a new item to a DynamoDB table.



```
# Put-item [ Adding extra attributes into existing items into a table (Ex- adding Email attribute to Raj)] 

aws dynamodb get-item --table-name my-dynamodb-table --region us-east-2 --key '{"Id": {"N": "101"}, "Name": {"S": "Raj"}}'

aws dynamodb put-item --table-name my-dynamodb-table --region us-east-2 --item '{"Id": {"N": "101"}, "Name": {"S": "Raj"}, "Email": {"S": "Raj@gmail.com"}}'


# put-item (adding new items into table)

aws dynamodb put-item --table-name my-dynamodb-table --region us-east-2 --item '{"Id": {"N": "105"}, "Name": {"S": "Joseph"}, "Email": {"S": "Joseph@gmail.com"}}'

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061732699/6f45d98a-b42c-4611-87a9-ecafccfdc7b8.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061455824/7b0bbb63-579e-4a96-b104-62aafff1d6f9.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061748846/cb401c66-24a0-46e7-bcc8-098c93c9bbe2.png)


1. ***DeleteItem:*** This operation allows you to delete an item from a DynamoDB table.



```
# delete item from the table

aws dynamodb delete-item --table-name my-dynamodb-table --region us-east-2 --key '{"Id": {"N": "105"}, "Name": {"S": "Joseph"}}'

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061455824/7b0bbb63-579e-4a96-b104-62aafff1d6f9.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061789751/e51c9227-0bdb-4d7b-a849-b852c407434d.png)


1. ***UpdateItem:*** This operation allows you to modify an existing item in a DynamoDB table.



```
# update item on the table (here, we are updating Email attribute of existing item)

aws dynamodb update-item --table-name my-dynamodb-table --region us-east-2 --key '{"Id": {"N": "104"}, "Name": {"S": "Andrew"}}' --update-expression "SET #Y = :y" --expression-attribute-names '{"#Y":"Email"}' --expression-attribute-values '{":y":{"S": "andrewClarke@gmail.com"}}'

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061837266/3de1589d-dfb0-4c92-8f86-38ed592d7af2.png)


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674061859141/f683806b-c179-4091-9c0d-fcd98854f45a.png)


References:


[aws-cli-iam-docs](https://docs.aws.amazon.com/cli/latest/reference/iam/)


[DynamoDB-AWS-CLI-Docs](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/index.html)


