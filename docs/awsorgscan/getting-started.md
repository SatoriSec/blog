# Getting Started

## Prerequisites

### 1. Install AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws configure
```

### 2. Install Go (1.20+ recommended)

```bash
wget https://golang.org/dl/go1.20.12.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.20.12.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
go version
```

### 3. IAM Permissions

Ensure the profile you run this tool with has permissions to:

- List AWS accounts (`organizations:ListAccounts`)
- Assume roles (`sts:AssumeRole`)
- Describe/List resources for services (EC2, S3, etc.)

---

## Running the Scanner

### Scan All Accounts

```bash
./scanner --org
```

### Scan a Single Account

```bash
./scanner --account 123456789012
```

### Scan Specific Services Only

```bash
./scanner --org --services ec2,elb,s3
```

### Custom Output File

```bash
./scanner --org --output myscan.csv
```
