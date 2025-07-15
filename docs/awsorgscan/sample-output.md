# Sample Output

Here's what a typical output row looks like in the CSV.

| Account ID | Account Name | Region     | Service     | Resource ID    | DNS Name                         | Public IP      | Extra            | Scan Target                    | URL                                  |
|------------|---------------|------------|--------------|----------------|----------------------------------|----------------|------------------|-------------------------------|---------------------------------------|
| 1234567890 | DevAccount    | us-east-1  | ec2          | i-0123456789abc |                                  | 3.85.200.14     | public instance  | 3.85.200.14                    |                                       |
| 1234567890 | DevAccount    | us-east-1  | s3           | public-bucket  | public-bucket.s3.amazonaws.com   |                | public-read      | public-bucket.s3.amazonaws.com | https://s3.console.aws.amazon.com/... |
| 1234567890 | DevAccount    | global     | cloudfront   | EDF123ABCXYZ   | d1abcd.cloudfront.net            |                | default domain   | d1abcd.cloudfront.net          | https://d1abcd.cloudfront.net         |
| 1234567890 | DevAccount    | us-east-1  | lambda       | my-function    |                                  |                | function URL     | https://abc123.lambda-url...   | https://abc123.lambda-url...          |
