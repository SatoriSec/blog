# FAQ

## Why are some resources missing?

- The resource may not be publicly accessible
- Your IAM credentials may lack access
- The service may not exist in that region

## How fast is it?

- Scans are concurrent across accounts, regions, and services
- A full org-wide scan with 10+ accounts finishes in seconds

## Can I add more services?

Yes! Just add a new `Scan<Service>()` function in `internal/scan/`, and register it in `scan.go`.
