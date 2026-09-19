# Solution Overview

## Hybrid security architecture

```text
                         Azure
        +--------------------------------------+
        | Azure Arc                           |
        |   AD-DC-01                          |
        |      |                               |
        |      +-- AzureMonitorWindowsAgent    |
        |      |                               |
        |      v                               |
        | dcr-cloudgenius-ad-security          |
        |      |                               |
        |      v                               |
        | Log Analytics                       |
        |      |                               |
        |      v                               |
        | Microsoft Sentinel                  |
        |   + KQL                             |
        |   + Watchlists                      |
        |   + Analytics Rules                 |
        |   + Incidents                       |
        +------------------^-------------------+
                           |
                           |
                    outbound HTTPS
                           |
        +------------------+-------------------+
        | On-Premises                          |
        |                                      |
        | AD-DC-01                             |
        | Windows Server 2025                  |
        | Active Directory Domain Services     |
        +--------------------------------------+
```

## Design principles

### Hybrid, not migrated

The domain controller stays on-premises. Azure Arc provides management-plane integration only.

### Selective telemetry

Only security events with a clear identity, privilege, persistence, or tampering use case are sent to Sentinel.

### Separate DCR

The domain controller has its own DCR rather than sharing a generic server collection policy. This allows independent tuning of Active Directory telemetry.

### Context enrichment

Watchlists provide Tier-0 and privileged-account context without adding high-volume log sources.

### Cost control

Paid Defender and patch-management add-ons are not enabled. Ingestion is intentionally narrow and can be expanded only when a defined detection use case justifies it.
