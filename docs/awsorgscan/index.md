# AWS Org Public IP Scanner

A fast, concurrent scanner for discovering publicly accessible resources across AWS accounts and regions.

The **AWS Org Public IP Scanner** is built in **Go (Golang)**, leveraging its lightweight concurrency primitives — **goroutines** — to achieve blazing-fast performance.

### 🧵 Full Parallel Execution Flow

This scanner employs **three layers of concurrency**:

1. **Account-level:** A separate goroutine is launched for each AWS account (org scope or single)  
2. **Region-level:** Within each account goroutine, all enabled AWS regions are scanned in parallel  
3. **Service-level:** Inside each region, every service (EC2, EIP, ELB, S3, CloudFront, Route 53, etc.) is scanned concurrently

This tool scans:
- EC2 instances with public IPs
- Load Balancers
- S3 buckets
- CloudFront distributions
- AppRunner services
- Lambda Function URLs
- Route53 zones
- Redshift clusters
- and many more...

## 🔍 Use Case

Identify and enumerate potential public attack surfaces across your AWS organization or a single account.

Ideal for security reviews, posture assessments, and continuous cloud hygiene monitoring.


---

## 🔐 Security Note

When `--org` mode is used, the tool uses the default `OrganizationAccountAccessRole` to assume roles into member accounts.

For better security and least privilege:
- Use a **delegated admin account**
- Create a dedicated IAM role for scanning (e.g., `OrgScannerRole`)
- Restrict access to only required services

⚠️ This role delegation feature is not yet implemented but is planned for future releases.
