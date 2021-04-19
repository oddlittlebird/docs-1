# Tenant Quotas and Limits

This page describes the rate limits for all PaaS GDN customers. Each quota applies on a per-Region basis unless otherwise specified.

!!! note
    Please reach out to support@macrometa.com if you would like to request higher limits.

## All Tenants

| Name | Quota |
|-----|--------|
| Max Request Per Minute Per Region | 60 |
| Max Document Size | 400 KB |
| Max Documents per Query | 1000 |
| Max Query Execution Time In Ms | 10000 |
| Max Memory per Query | 256 MB |
| Max Fabrics Per Tenant | 2 |
| Max Collections per Fabric | 25 |
| Max Query Workers per Fabric | 10 |
| Max Graphs Per Fabric | 10 |
| Max Indexes per Fabric | 10 | 
| Max Query Workers Calls per Fabric | 1000 |
| Max Query Workers Calls Per Day | 1000 |

## Free Tier Tenants

All free developer accounts have the following additional rate limits. **Please note**, these are **hard** limits and API calls will be denied once these limits are hit. Quota's are reset every 24 hours.

| Name | Quota |
|-----|--------|
| Max Requests per Day | 20,000  |
| Max Storage per Day | 200 MB |
