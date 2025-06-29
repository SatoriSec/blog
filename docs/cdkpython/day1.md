### Background

Welcome to your AWS CDK with Python journey!
AWS CDK lets you define cloud infrastructure using Python code, making your AWS environments reliable, repeatable, and secure.

Today, you’ll set up your development environment so you’re ready to build and deploy AWS infrastructure with confidence.

**Tip:**
Consider using a virtual machine (VM) locally (like VirtualBox) or in the cloud (like AWS EC2) for a clean, isolated environment. Mac and Windows users can adapt these instructions as needed.

### What You’ll Do

- **Install and verify Python and pip**
- **Install AWS CLI**
- **Install Node.js and npm**
- **Install AWS CDK**
- **Create an AWS account (if needed)**
- **Verify your setup**


## Step-by-Step Guide (Ubuntu)

### 1. Install and Verify Python and pip

Update your package list and install Python 3 and pip:

```bash
sudo apt update
sudo apt install python3 python3-pip
```

Check the installed versions:

```bash
python3 --version
pip3 --version
```

You should see version numbers for both.

### 2. Install AWS CLI

Install the AWS CLI using the official bundle:

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Check the installation:

```bash
aws --version
```


### 3. Install Node.js and npm

Install Node.js and npm using the NodeSource repository:

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

Check the installation:

```bash
node -v
npm -v
```

You should see version numbers for both.

### 4. Install AWS CDK

Install the AWS CDK globally:

```bash
sudo npm install -g aws-cdk
```

Check the installation:

```bash
cdk --version
```

You should see the CDK version number.

### 5. Create an AWS Account (if needed)

If you don’t have an AWS account, go to the [AWS website](https://aws.amazon.com/) and sign up.

### 6. Verify Your Setup

Check that everything is installed and ready:

```bash
python3 --version
pip3 --version
aws --version
node -v
npm -v
cdk --version
```

All commands should show version numbers.

## Next Step: Add AWS Credentials

**Before you can use the AWS CLI or CDK, you must configure your AWS credentials.**
This can be done using either long-term credentials (IAM user) or temporary credentials (from AWS STS or IAM Identity Center).

**Follow the official AWS documentation to set up your credentials:**
[Configuration and credential file settings in the AWS CLI](https://docs.aws.amazon.com/cli/v1/userguide/cli-configure-files.html)
[Setting up the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html)
[Using temporary credentials with AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html)

**You’re now ready to start your AWS CDK with Python journey!**
Tomorrow, you’ll create your first CDK project.

