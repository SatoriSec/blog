---
slug: "building-blocks-of-aws-iam"
title: |
  Building Blocks of AWS IAM
date: 2021-02-16
tags: ['iam', 'aws']
---

The building blocks of AWS Identity and Access Management (IAM) are:

<!-- more -->




1. ***Principals***: is an entity that can perform actions on AWS resources. Here are some examples of principals in IAM:


	1. *IAM users*: These are the people who need access to your AWS resources. You can create an IAM user for each person who needs access, and give them a unique user name and password.
	
	
	2. *Roles*: These are similar to users, but they are intended to be assumed by AWS resources such as EC2 instances, Lambda functions, and so on. You can create roles and give them permissions to access AWS resources.
	
	
	3. *AWS accounts*: An AWS account is a principal that represents the owner of the AWS resources. The AWS root account is the main account for an AWS account owner, and it has full access to all AWS resources in the account.
	
	
	4. *Federated users*: These are users who are authenticated by an external identity provider, such as a company's Active Directory or a social media site like Facebook. You can use federation to grant these users access to your AWS resources without creating IAM users for them.
	
	
	 By using these different types of principals, you can create a secure and organized system for controlling access to your AWS resources. You can give different permissions to different principals, depending on their role and the resources they need to access.
2. ***Users***: These are the people who need access to your AWS resources. You can create an IAM user for each person who needs access, and give them a unique user name and password.


3. ***Groups***: You can create groups in IAM and add users to them. This makes it easier to manage permissions for multiple users at once, because you can give permissions to the group instead of each individual user.


4. ***Policies***: These are the rules that define what actions a user or group is allowed to perform on your AWS resources. You can create policies using JSON (a programming language) or use predefined policies that AWS provides.


5. ***Roles***: These are similar to users, but they are intended to be assumed by other principals or AWS resources such as EC2 instances, Lambda functions, and so on. You can create roles and give them permissions to access AWS resources.




By using these building blocks, you can create a secure and organized system for controlling access to your AWS resources.


