# Services Scanned and Why

This scanner identifies public exposure risks across a wide range of AWS services.

## EC2

**What it is:** Virtual machine instances in your AWS environment.

**Why it matters:** Public IPs on EC2 instances may expose SSH, RDP, or web services to the internet.

**What we scan:** EC2 instances with associated public IPs.

---

## Elastic Load Balancer (ELB)

**What it is:** A public-facing load balancer that routes traffic to your applications.

**Why it matters:** Public DNS names can expose backend services unintentionally.

**What we scan:** Classic, Application, and Network Load Balancers with public schemes.

---

## RDS (Relational Database Service)

**What it is:** Managed databases like MySQL, PostgreSQL, etc.

**Why it matters:** Public RDS instances may expose your data to unauthorized access.

**What we scan:** RDS databases that are publicly accessible.

---

## Lambda Function URLs

**What it is:** HTTPS endpoints for invoking Lambda functions directly.

**Why it matters:** These URLs can be publicly accessible without auth if misconfigured.

**What we scan:** Lambda functions with Function URLs.

---

## AppRunner

**What it is:** A fully managed service for web applications and APIs.

**Why it matters:** AppRunner services have public HTTPS endpoints.

**What we scan:** All AppRunner services with valid URLs.

---

## S3

**What it is:** Object storage for files and backups.

**Why it matters:** Public buckets can leak sensitive data.

**What we scan:** Buckets marked as publicly accessible.

---

## CloudFront

**What it is:** Content Delivery Network (CDN) for static and dynamic content.

**Why it matters:** May serve content from exposed S3 or backend resources.

**What we scan:** All distributions and aliases (custom domains).

---

## OpenSearch

**What it is:** A search and analytics engine (based on Elasticsearch).

**Why it matters:** Public OpenSearch domains can leak logs and internal metrics.

**What we scan:** Domains that are not VPC-isolated.

---

## Redshift

**What it is:** Data warehouse service for big data analytics.

**Why it matters:** Public clusters can expose large datasets.

**What we scan:** Clusters that are publicly accessible.

---

## Route53

**What it is:** Managed DNS service.

**Why it matters:** DNS records can direct traffic to public infrastructure.

**What we scan:** Public hosted zones and records.

---

## Amplify

**What it is:** Hosting for frontend web apps.

**Why it matters:** Public apps may expose outdated or test content.

**What we scan:** Amplify apps with public domains.

---

## Global Accelerator

**What it is:** Edge networking for improving availability and latency.

**Why it matters:** Maps to public-facing resources globally.

**What we scan:** Accelerators with associated public IP sets.

---

## AppSync

**What it is:** Managed GraphQL APIs.

**Why it matters:** Public GraphQL endpoints can expose sensitive queries or data.

**What we scan:** All URIs (GRAPHQL, REALTIME) exposed via AppSync.


---

## Elastic IP (EIP)

**What it is:** A static, public IPv4 address in AWS.

**Why it matters:** These IPs can be attached to critical services and remain exposed even after instance termination.

**What we scan:** All allocated Elastic IPs in each region.
