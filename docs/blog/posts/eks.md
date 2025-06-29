---
title: EKS Blog Post
date: 2025-01-03
tags: [eks, blog]
author: You
slug: eks-first-post
---

## EKS End-to-End Scenario: Pod Identity with AWS Services

This scenario demonstrates deploying a web application on Amazon EKS that interacts with AWS S3 and DynamoDB using EKS Pod Identity for secure, fine-grained IAM access.

<!-- more -->

**Use Case Overview**

- Deploy a Python Flask app in EKS.
- App stores/retrieves files in S3 and reads/writes data in DynamoDB.
- Access to AWS services is managed using EKS Pod Identity—no static AWS credentials in pods.


## **Step 1: Prerequisites**

- AWS CLI and kubectl installed and configured.
- eksctl installed.
- An AWS account with permissions to create EKS clusters, IAM roles, S3, and DynamoDB.


## **Step 2: Create the EKS Cluster**

```bash
eksctl create cluster \
  --name eks-pod-identity-demo \
  --region us-west-2 \
  --nodes 2 \
  --node-type t3.medium
```


## **Step 3: Enable EKS Pod Identity**

1. **Associate Pod Identity with your cluster:**
```bash
eksctl utils associate-iam-oidc-provider \
  --region us-west-2 \
  --cluster eks-pod-identity-demo \
  --approve
```

2. **Install the EKS Pod Identity Agent:**
```bash
kubectl apply -f https://github.com/aws/amazon-eks-pod-identity-webhook/releases/latest/download/deployment.yaml
```


## **Step 4: Create AWS Resources**

- **S3 Bucket:**
    - Create a bucket (e.g., `eks-pod-identity-demo-bucket`).
- **DynamoDB Table:**
    - Create a table (e.g., `EKSAppTable`) with a primary key (`id`).


## **Step 5: Create IAM Role for Service Account (IRSA)**

1. **Create an IAM policy:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::eks-pod-identity-demo-bucket/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem",
        "dynamodb:GetItem"
      ],
      "Resource": "arn:aws:dynamodb:us-west-2:YOUR_ACCOUNT_ID:table/EKSAppTable"
    }
  ]
}
```

2. **Create the IAM role and associate with Kubernetes service account:**
```bash
eksctl create iamserviceaccount \
  --name eks-app-sa \
  --namespace default \
  --cluster eks-pod-identity-demo \
  --attach-policy-arn arn:aws:iam::YOUR_ACCOUNT_ID:policy/eks-app-policy \
  --approve
```


## **Step 6: Deploy the Application**

1. **Create a Kubernetes deployment using the service account:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      serviceAccountName: eks-app-sa
      containers:
      - name: flask-app
        image: YOUR_ECR_REPO/flask-app:latest
        env:
        - name: S3_BUCKET
          value: eks-pod-identity-demo-bucket
        - name: DDB_TABLE
          value: EKSAppTable
        ports:
        - containerPort: 5000
```

2. **Expose the app via a LoadBalancer:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: flask-app-service
spec:
  type: LoadBalancer
  selector:
    app: flask-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
```


## **Step 7: Application Logic (Python Example)**

```python
import boto3
import os
from flask import Flask, request

app = Flask(__name__)
s3 = boto3.client('s3')
ddb = boto3.resource('dynamodb')
bucket = os.environ['S3_BUCKET']
table = ddb.Table(os.environ['DDB_TABLE'])

@app.route('/upload', methods=['POST'])
def upload():
    file = request.files['file']
    s3.upload_fileobj(file, bucket, file.filename)
    table.put_item(Item={'id': file.filename, 'status': 'uploaded'})
    return 'Uploaded', 200

@app.route('/download/<filename>', methods=['GET'])
def download(filename):
    s3.download_file(bucket, filename, f'/tmp/{filename}')
    return open(f'/tmp/{filename}', 'rb').read(), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0')
```


## **Step 8: Test the Setup**

- Upload and download files via the app endpoint.
- Check S3 and DynamoDB for data.
- Confirm no AWS credentials are present in the pod—access is via Pod Identity.


## **Summary Table**

| Component      | AWS Service | Purpose                 | Access Managed By   |
| :------------- | :---------- | :---------------------- | :------------------ |
| EKS Cluster    | EKS         | Orchestrates containers | eksctl              |
| Storage        | S3          | File storage            | Pod Identity (IRSA) |
| Database       | DynamoDB    | Key-value data store    | Pod Identity (IRSA) |
| App Deployment | EKS         | Runs Flask app in pods  | K8s Service Account |

This workflow demonstrates a secure, production-grade pattern for cloud-native applications on AWS using EKS, Pod Identity, and tightly scoped IAM permissions—no hardcoded credentials, all access is managed by Kubernetes-native identities.

<div style="text-align: center">⁂</div>

[^1_1]: https://www.sec.gov/Archives/edgar/data/1346287/000168316825002575/cluster_i10k-123124.htm

[^1_2]: https://www.sec.gov/Archives/edgar/data/1816017/000095017025047695/spir-20241231.htm

[^1_3]: https://www.sec.gov/Archives/edgar/data/1699838/000095017025022369/cflt-20241231.htm

[^1_4]: https://www.sec.gov/Archives/edgar/data/1707753/000170775325000021/estc-20250430.htm

[^1_5]: https://www.sec.gov/Archives/edgar/data/1580560/000095017025065606/flyw-20250331.htm

[^1_6]: https://www.sec.gov/Archives/edgar/data/1769628/000119312525067651/d899798d424b4.htm

[^1_7]: https://www.sec.gov/Archives/edgar/data/1603652/000095017025039411/uly-20241231.htm

[^1_8]: https://docs.aws.amazon.com/eks/latest/userguide/common-use-cases.html

[^1_9]: https://aws.amazon.com/eks/

[^1_10]: https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html

[^1_11]: https://www.cloudzero.com/blog/aws-eks/

[^1_12]: https://zesty.co/finops-glossary/amazon-kubernetes-eks/

[^1_13]: https://docs.aws.amazon.com/eks/latest/userguide/pod-identities.html

[^1_14]: https://docs.aws.amazon.com/eks/latest/userguide/eks-integrations.html

[^1_15]: https://dev.to/aws-builders/simplify-aws-resource-access-with-eks-pod-identity-agent-and-python-sdk-540k

[^1_16]: https://github.com/aws-samples/amazon-eks-pod-identity-demo

[^1_17]: https://dev.to/aws-builders/eks-pod-identities-4j56

[^1_18]: https://journalwjarr.com/node/1232

[^1_19]: https://www.onlinescientificresearch.com/articles/deploying-and-managing-containerized-data-workloads-on-amazon-eks.pdf

[^1_20]: https://www.ijsat.org/research-paper.php?id=2809


---

# can you use cdk to set uthi up

Yes, you can use AWS CDK (Cloud Development Kit) to set up the entire EKS scenario—including the cluster, Pod Identity, IAM roles, S3 bucket, DynamoDB table, and Kubernetes resources. CDK enables you to define your infrastructure as code in Python, TypeScript, or other supported languages.

Below is a high-level outline of how you would implement this scenario using CDK (Python example):

## **1. Initialize Your CDK Project**

```bash
cdk init app --language python
pip install aws-cdk-lib constructs
```


## **2. Define the Stack in CDK**

**a. Create the EKS Cluster**

```python
from aws_cdk import (
    Stack,
    aws_eks as eks,
    aws_iam as iam,
    aws_s3 as s3,
    aws_dynamodb as dynamodb,
)
from constructs import Construct

class EksPodIdentityStack(Stack):
    def __init__(self, scope: Construct, construct_id: str, **kwargs):
        super().__init__(scope, construct_id, **kwargs)

        # S3 bucket
        bucket = s3.Bucket(self, "EksAppBucket")

        # DynamoDB table
        table = dynamodb.Table(
            self, "EksAppTable",
            partition_key=dynamodb.Attribute(name="id", type=dynamodb.AttributeType.STRING)
        )

        # EKS cluster
        cluster = eks.Cluster(
            self, "EksCluster",
            version=eks.KubernetesVersion.V1_29,
            default_capacity=2,
        )

        # IAM policy for pod access
        policy = iam.PolicyStatement(
            actions=[
                "s3:GetObject", "s3:PutObject",
                "dynamodb:PutItem", "dynamodb:GetItem"
            ],
            resources=[
                bucket.bucket_arn + "/*",
                table.table_arn
            ]
        )

        # Service account with IAM role (Pod Identity)
        sa = cluster.add_service_account("EksAppServiceAccount")
        sa.add_to_principal_policy(policy)

        # Output values
        self.bucket = bucket
        self.table = table
        self.cluster = cluster
        self.sa = sa
```

**b. Deploy the Stack**

```bash
cdk deploy
```


## **3. Deploy Your Application to EKS**

- Build and push your Docker image to ECR.
- Use CDK to apply a Kubernetes manifest (or use `kubectl`):

```python
app_manifest = {
    "apiVersion": "apps/v1",
    "kind": "Deployment",
    "metadata": {"name": "flask-app"},
    "spec": {
        "replicas": 2,
        "selector": {"matchLabels": {"app": "flask-app"}},
        "template": {
            "metadata": {"labels": {"app": "flask-app"}},
            "spec": {
                "serviceAccountName": sa.service_account_name,
                "containers": [{
                    "name": "flask-app",
                    "image": "YOUR_ECR_REPO/flask-app:latest",
                    "env": [
                        {"name": "S3_BUCKET", "value": bucket.bucket_name},
                        {"name": "DDB_TABLE", "value": table.table_name}
                    ],
                    "ports": [{"containerPort": 5000}]
                }]
            }
        }
    }
}

cluster.add_manifest("FlaskAppDeployment", app_manifest)
```

- Similarly, add a manifest for the LoadBalancer service.


## **4. Summary**

- **All AWS resources (EKS, S3, DynamoDB, IAM roles) are provisioned via CDK.**
- **Pod Identity (IAM role for service account) is set up with least-privilege permissions.**
- **Kubernetes manifests can be deployed via CDK or post-deployment with `kubectl`.**

This approach gives you a fully automated, repeatable infrastructure deployment using modern infrastructure-as-code best practices.

<div style="text-align: center">⁂</div>

[^2_1]: https://www.sec.gov/Archives/edgar/data/1346287/000168316825002575/cluster_i10k-123124.htm

[^2_2]: https://www.sec.gov/Archives/edgar/data/1769628/000119312525058309/d899798ds1a.htm

[^2_3]: https://www.sec.gov/Archives/edgar/data/1640147/000164014725000052/snow-20250131.htm

[^2_4]: https://www.sec.gov/Archives/edgar/data/1769628/000119312525067651/d899798d424b4.htm

[^2_5]: https://www.sec.gov/Archives/edgar/data/1582961/000158296125000035/docn-20241231.htm

[^2_6]: https://www.sec.gov/Archives/edgar/data/1769628/000119312525052207/d899798ds1a.htm

[^2_7]: https://www.sec.gov/Archives/edgar/data/1699838/000095017025022369/cflt-20241231.htm

[^2_8]: https://ijpds.org/article/view/1044

[^2_9]: https://www.mdpi.com/1424-8220/21/5/1910/pdf

[^2_10]: https://pmc.ncbi.nlm.nih.gov/articles/PMC7967216/

[^2_11]: http://arxiv.org/pdf/2407.01620.pdf

[^2_12]: https://aws.plainenglish.io/deploying-an-eks-cluster-with-aws-cloud-development-kit-cdk-afdb96ce3a83

[^2_13]: https://aws.amazon.com/blogs/containers/continuous-delivery-of-amazon-eks-clusters-using-aws-cdk-and-cdk-pipelines/

[^2_14]: https://github.com/aws-samples/amazon-eks-using-cdk-typescript

[^2_15]: https://docs.aws.amazon.com/cdk/api/v2/python/aws_cdk.aws_eks/README.html

[^2_16]: https://docs.aws.amazon.com/eks/latest/userguide/create-cluster.html

[^2_17]: https://www.youtube.com/watch?v=Y0tgevYN-Wo

[^2_18]: https://stackoverflow.com/questions/71770266/how-do-i-create-an-eks-cluster-with-nodes-via-cdk

[^2_19]: https://aws.amazon.com/blogs/infrastructure-and-automation/simplify-integration-of-your-amazon-eks-cluster-with-amazon-eks-blueprints-for-cdk/

[^2_20]: https://www.reddit.com/r/aws/comments/1dtiqfq/stepbystep_tutorial_to_set_up_build_a_cicd/


---

# can you use python

Yes, you can use Python with AWS CDK to set up your EKS cluster, configure Pod Identity, and provision all supporting AWS resources. The AWS CDK natively supports Python as one of its primary languages, allowing you to define and automate your infrastructure using Python code[^3_1][^3_2][^3_4][^3_5][^3_7].

### Key Points

- **AWS CDK supports Python**: You can define EKS clusters, IAM roles, S3 buckets, DynamoDB tables, and Kubernetes manifests directly in Python code[^3_1][^3_2][^3_4][^3_5][^3_7].
- **Full automation**: Using Python with CDK, you can automate the entire lifecycle of your infrastructure, including cluster creation, IAM role association for Pod Identity, and application deployment[^3_1][^3_4][^3_5][^3_7].
- **Example Workflow**:
    - Initialize your CDK project with `cdk init app --language python`[^3_5][^3_7].
    - Use the `aws_cdk.aws_eks` module to define your EKS cluster and add Kubernetes resources[^3_4][^3_7].
    - Define IAM roles and policies in Python and associate them with Kubernetes service accounts for Pod Identity.
    - Use CDK to deploy S3, DynamoDB, and other AWS resources as needed.
    - Deploy Kubernetes manifests (such as Deployments and Services) using `cluster.add_manifest()` in your Python CDK code[^3_4][^3_7].


### Example CDK Project Structure (Python)

```bash
mkdir eks
cd eks
mkdir cluster cdk8s
cd cluster
cdk init app --language=python
```

This sets up your Python-based CDK project for EKS provisioning[^3_5][^3_7].

### Documentation and Guides

- The [AWS EKS Construct Library documentation](https://docs.aws.amazon.com/cdk/api/v2/python/aws_cdk.aws_eks/README.html) provides Python examples for defining clusters and applying Kubernetes manifests[^3_4].
- Tutorials and guides, such as those on AWS and developer blogs, demonstrate end-to-end EKS automation using Python and CDK, including integration with Helm and other tools[^3_5][^3_7].

**In summary:** Python is a fully supported and commonly used language for AWS CDK, enabling you to automate EKS and related AWS resource setup, including Pod Identity, entirely in Python code[^3_1][^3_2][^3_4][^3_5][^3_7].

<div style="text-align: center">⁂</div>

[^3_1]: https://isjem.com/download/from-code-to-cloud-automating-continuous-deployment-with-aws-cloud-development-kit-cdk-and-infrastructure-as-code-iac/

[^3_2]: https://ieeexplore.ieee.org/document/10516612/

[^3_3]: https://ijpds.org/article/view/1044

[^3_4]: https://docs.aws.amazon.com/cdk/api/v2/python/aws_cdk.aws_eks/README.html

[^3_5]: https://aws.amazon.com/tutorials/deploy-webapp-eks/module-one/

[^3_6]: https://aws.plainenglish.io/deploying-an-eks-cluster-with-aws-cloud-development-kit-cdk-afdb96ce3a83

[^3_7]: https://dev.to/marocz/automating-eks-deployment-and-nginx-setup-using-helm-with-aws-cdk-in-python-27mn

[^3_8]: https://rtfm.co.ua/en/aws-cdk-and-python-building-an-eks-cluster-and-general-impressions-of-cdk/

[^3_9]: https://github.com/aws-samples/eks-configrules-with-cdk

[^3_10]: https://stackoverflow.com/questions/71770266/how-do-i-create-an-eks-cluster-with-nodes-via-cdk

[^3_11]: https://docs.aws.amazon.com/cdk/api/v2/python/aws_cdk.aws_eks.html

[^3_12]: https://www.sec.gov/Archives/edgar/data/1938046/000164117225000085/form10-k.htm

[^3_13]: https://www.sec.gov/Archives/edgar/data/1819574/000181957425000024/bark-20250331.htm

[^3_14]: https://www.sec.gov/Archives/edgar/data/1883984/000121390025052242/ea0244740-10k_alternus.htm

[^3_15]: https://www.sec.gov/Archives/edgar/data/2064319/0002064319-25-000001-index.htm

[^3_16]: https://www.sec.gov/Archives/edgar/data/2062378/0002062378-25-000001-index.htm

[^3_17]: https://www.sec.gov/Archives/edgar/data/1219601/000121960125000009/cck-20241231.htm

[^3_18]: https://www.sec.gov/Archives/edgar/data/91576/000009157625000038/key-20241231.htm

[^3_19]: https://www.reddit.com/r/aws/comments/11wpp2r/cdk_and_python_noob_help_me_understand_kubectl/


---

# where is the python code

Here is a concise Python AWS CDK example that provisions:

- An EKS cluster
- An S3 bucket
- A DynamoDB table
- An IAM role for Pod Identity (IRSA) and a Kubernetes service account
- Deploys a simple app using that service account

This is a minimal, illustrative example. You can expand it for production use.

```python
from aws_cdk import (
    core,
    aws_eks as eks,
    aws_iam as iam,
    aws_s3 as s3,
    aws_dynamodb as dynamodb,
)

class EksPodIdentityStack(core.Stack):
    def __init__(self, scope: core.Construct, id: str, **kwargs):
        super().__init__(scope, id, **kwargs)

        # S3 bucket
        bucket = s3.Bucket(self, "EksAppBucket")

        # DynamoDB table
        table = dynamodb.Table(
            self, "EksAppTable",
            partition_key=dynamodb.Attribute(name="id", type=dynamodb.AttributeType.STRING)
        )

        # EKS Cluster
        cluster = eks.Cluster(
            self, "EksCluster",
            version=eks.KubernetesVersion.V1_29,
            default_capacity=2,
        )

        # IAM policy for pod access
        policy = iam.PolicyStatement(
            actions=[
                "s3:GetObject", "s3:PutObject",
                "dynamodb:PutItem", "dynamodb:GetItem"
            ],
            resources=[
                bucket.bucket_arn + "/*",
                table.table_arn
            ]
        )

        # Service account with IAM role (Pod Identity)
        sa = cluster.add_service_account("EksAppServiceAccount")
        sa.add_to_principal_policy(policy)

        # Deploy your app (placeholder manifest)
        app_manifest = {
            "apiVersion": "apps/v1",
            "kind": "Deployment",
            "metadata": {"name": "flask-app"},
            "spec": {
                "replicas": 2,
                "selector": {"matchLabels": {"app": "flask-app"}},
                "template": {
                    "metadata": {"labels": {"app": "flask-app"}},
                    "spec": {
                        "serviceAccountName": sa.service_account_name,
                        "containers": [{
                            "name": "flask-app",
                            "image": "YOUR_ECR_REPO/flask-app:latest",
                            "env": [
                                {"name": "S3_BUCKET", "value": bucket.bucket_name},
                                {"name": "DDB_TABLE", "value": table.table_name}
                            ],
                            "ports": [{"containerPort": 5000}]
                        }]
                    }
                }
            }
        }
        cluster.add_manifest("FlaskAppDeployment", app_manifest)

        # Expose the app (placeholder manifest)
        service_manifest = {
            "apiVersion": "v1",
            "kind": "Service",
            "metadata": {"name": "flask-app-service"},
            "spec": {
                "type": "LoadBalancer",
                "selector": {"app": "flask-app"},
                "ports": [{"protocol": "TCP", "port": 80, "targetPort": 5000}]
            }
        }
        cluster.add_manifest("FlaskAppService", service_manifest)
```

**Usage:**

- Place this in your CDK app (e.g., `app.py` and `eks_pod_identity_stack.py`).
- Replace `"YOUR_ECR_REPO/flask-app:latest"` with your actual container image.
- Run `cdk deploy`.

**References:**

- [CDK EKS Python documentation][^4_1]
- [Example of IRSA with CDK and Python][^4_6]

