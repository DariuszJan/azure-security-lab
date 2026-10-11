
# Azure-Security-Lab
Azure security detection pipeline - KQL, Log Analytics, Azure Monitor
# Azure Logging Pipeline — Detection & Alerting

## What this project demonstrates

A complete, end-to-end detection pipeline built in Microsoft Azure:

```
Azure Resource (Storage Account)
        ↓
Diagnostic Settings (StorageBlobLogs)
        ↓
Log Analytics Workspace (law-security-lab)
        ↓
KQL Query (detection logic)
        ↓
Azure Monitor Alert Rule (Count > 0)
        ↓
Action Group → Email notification
```

## Why this matters

By default, Azure logs almost nothing. Diagnostic Settings must be enabled manually on every resource. Without this pipeline, a deletion of critical files or resources goes completely unnoticed — no alert, no email, no record in a queryable format.

This project shows how to close that gap: configure logging, write detection logic in KQL, and wire up automated alerting.

## What is detected

### 1. Blob DELETE operations (`detection-delete-blob.kql`)
Detects file deletions inside Azure Blob Storage containers using `StorageBlobLogs`.  
Returns: timestamp, caller IP address, file URI, HTTP status code.

### 2. Control plane DELETE operations (`detection-azure-activity-delete.kql`)
Detects deletion of Azure resources (containers, VMs, resource groups, etc.) using `AzureActivity`.  
Returns: timestamp, caller identity (email/Service Principal), operation name, resource group.

## Alert Rule configuration

| Setting | Value |
|---|---|
| Signal type | Custom log search |
| Source | AzureActivity |
| Condition | Count > 0 |
| Aggregation granularity | 5 minutes |
| Severity | 2 – Warning |
| Action | Email via Action Group |

## Environment

| Resource | Name |
|---|---|
| Log Analytics Workspace | law-security-lab |
| Storage Account | storseclab2410 |
| Resource Group | rg-security-lab |
| Region | West Europe |

## Key concepts demonstrated

- **Diagnostic Settings** — must be enabled per resource; not on by default
- **StorageBlobLogs vs AzureActivity** — data plane vs control plane logging
- **KQL** — `where`, `project`, `summarize`, `endswith`, `ago()`, `in~`
- **Alert Rule threshold** — `Count > 0` as a starting baseline
- **Action Group** — decouples notification logic from detection logic
