# CSV Output Format

Each row in the output CSV represents a public resource found in your AWS environment.

| Column        | Description                                           |
|---------------|-------------------------------------------------------|
| Account ID    | AWS account where the resource is located             |
| Account Name  | Friendly name (if available)                          |
| Region        | AWS region of the resource                            |
| Service       | AWS service name (e.g., EC2, S3, Lambda)              |
| Resource ID   | ID or name of the resource                            |
| DNS Name      | DNS hostname (if applicable)                          |
| Public IP     | IP address (if applicable)                            |
| Extra         | Any additional notes (e.g., public, alias)            |
| Scan Target   | The actual host/IP to use for scanning/vuln checks    |
| URL           | AWS Console or direct URL to the resource             |


---

## 🔎 About Scan Target

The `Scan Target` field is the **primary value you can use for further analysis**, such as:

- Security scans
- Reachability tests
- Inventory linking

It may be an IP address or a hostname, depending on the resource.
