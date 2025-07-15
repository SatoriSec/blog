---
slug: "understanding-iam"
title: |
  Understanding IAM
date: 2022-12-16
tags: ['iam', 'aws']
---

Let's understand about AWS IAM service in a high level and what can we do with this service and as we will move step by step will head toward much greater detail.

<!-- more -->




Identity and Access Management (IAM) is a way to control who can access different parts of a computer system or website. It helps to keep your information and resources safe by only allowing the right people to access them.


IAM works in a way like suppose are the owner of your computer system or website, and you can decide who can access different parts of it. You can create "users" for the people who need access, and you can give each user a "permission" to do certain things.  
For example, you might create a user for your friend and give them permission to read and write (add, change, or delete) files on your computer. But you might not give them permission to change your computer's settings or delete important files.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673811418235/0ed550cd-cdab-4686-bbd3-331bc85ff931.png)


IAM helps you to keep your information and resources safe by making sure that only the people you trust can access them. It's an important part of keeping your computer system or website secure.


From technical point of view, AWS Identity and Access Management (IAM) is a service that helps you securely control access to AWS resources. It enables you to create and manage AWS users and groups, and use permissions to allow and deny their access to AWS resources.


Here is a simple example of how IAM works:


1. You create an IAM user for each person who needs access to your AWS resources.


2. You create an IAM group and add the users to the group.


3. You create an IAM policy that specifies what permissions the group has for the AWS resources.


4. You attach the policy to the group.




Now, when the users in the group sign in to the AWS Management Console, they can only perform the actions allowed by the policy. This helps you secure your resources and ensure that only authorized users have access.


IAM is an important part of managing your AWS resources, and it's important to understand how it works to ensure that you are using it effectively.


