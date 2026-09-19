# Validation

## Arc connectivity

Azure Arc showed AD-DC-01 as **Connected** after onboarding.

## AMA installation

The AzureMonitorWindowsAgent extension returned:

```text
ExtensionOperation: enable
Status: Success
ProvisioningState: Succeeded
```

## DCR association

The dedicated AD security DCR association was created successfully and verified against the Arc machine resource.

## SecurityEvent ingestion

The following KQL was used to verify event ingestion:

```kusto
SecurityEvent
| where Computer contains "AD-DC-01"
| where TimeGenerated > ago(60m)
| summarize Count=count() by EventID
| sort by Count desc
```

During validation, the workspace returned real domain-controller events, including:

```text
4672   Special privileges assigned to new logon
4625   Failed logon
```

A controlled failed-logon attempt was performed to create Event ID 4625 and validate the full pipeline.

## Watchlist validation

Tier-0 assets:

```kusto
_GetWatchlist('CG-Tier0-Assets')
```

Privileged accounts:

```kusto
_GetWatchlist('CG-Privileged-Accounts')
```

Both returned the expected records.

## End-to-end validated path

```text
Windows Security Log
        ↓
Azure Monitor Agent
        ↓
Data Collection Rule
        ↓
Log Analytics
        ↓
Microsoft Sentinel
        ↓
KQL / Watchlist correlation
```
