# Microsoft Sentinel AD SOC Lab

A practical hybrid SOC implementation built in the CloudGenius lab to monitor an on-premises Active Directory domain controller with Microsoft Sentinel while keeping Azure ingestion and add-on costs intentionally low.

This repository documents the actual implementation completed in the lab: onboarding an on-prem Windows Server 2025 domain controller to Azure Arc, installing Azure Monitor Agent (AMA), creating a tightly filtered Data Collection Rule (DCR), validating SecurityEvent ingestion in Microsoft Sentinel, and enriching detections with Tier-0 and privileged-account watchlists.

## Environment

The lab uses an on-premises Active Directory environment running on virtual infrastructure. The primary monitored system is:

- **AD-DC-01** — Windows Server 2025 Standard Evaluation
- Role: Active Directory Domain Services / Domain Controller
- Azure management plane: Azure Arc-enabled Servers
- Monitoring agent: Azure Monitor Agent
- SIEM: Microsoft Sentinel
- Log Analytics workspace: sanitized in this public repository
- Defender for Servers extension: intentionally not enabled
- Azure Update Manager: intentionally not enabled for this Arc server because the lab does not currently use a qualifying Windows Server subscription/Software Assurance entitlement and the goal is to keep Azure costs near zero

The server remains on-premises. Azure Arc creates a management representation of the server in Azure; it does not move the VM into Azure.

## What was implemented

1. Onboarded the on-prem domain controller to Azure Arc using the Azure Connected Machine Agent.
2. Verified the Arc machine resource was created and reported **Connected**.
3. Installed the Azure Monitor Windows Agent extension on the Arc-enabled server.
4. Created a dedicated DCR for Active Directory security telemetry.
5. Associated the DCR only with the domain controller.
6. Sent only selected high-value Windows Security events to Log Analytics/Sentinel.
7. Validated live ingestion from the domain controller.
8. Created and validated Sentinel watchlists for Tier-0 assets and privileged identities.
9. Built the foundation for KQL analytics rules and incident generation.
10. Kept Defender for Servers and other paid Arc add-ons disabled to preserve the low-cost design.

## Architecture

```text
On-Premises / Proxmox
        |
        | Windows Server 2025
        v
     AD-DC-01
        |
        | Azure Connected Machine Agent
        v
 Azure Arc-enabled Server
        |
        | Azure Monitor Agent
        v
 dcr-cloudgenius-ad-security
        |
        | Selected SecurityEvent IDs only
        v
 Log Analytics Workspace
        |
        v
 Microsoft Sentinel
        |
        +--> KQL
        +--> Watchlists
        +--> Analytics Rules
        +--> Alerts / Incidents
```

## High-value event strategy

The DCR does **not** ingest the entire Windows Security event log. It collects a curated set of events related to:

- failed authentication;
- explicit credential use;
- privileged logon;
- audit-log tampering;
- audit-policy changes;
- service installation;
- user lifecycle changes;
- privileged/security group changes;
- account lockout/unlock;
- domain policy changes;
- computer-account changes;
- Active Directory object creation/modification/move/deletion.

High-volume events such as successful logons, process creation, Kerberos ticket requests, NTLM validation, and broad directory-object access were intentionally excluded from the initial baseline.

## Validated results

The Sentinel pipeline was validated with live SecurityEvent data from AD-DC-01.

Observed during testing:

- Event ID **4672** — Special privileges assigned to new logon
- Event ID **4625** — Failed logon

A controlled failed-logon test generated Event ID 4625 and the event was visible in Sentinel.

Watchlists were also validated with KQL using `_GetWatchlist()`.

## Sentinel watchlists

Two foundational watchlists were created and validated:

- `CG-Tier0-Assets`
- `CG-Privileged-Accounts`

The Tier-0 list contains the domain controller, enterprise CA, and Entra Connect server. The privileged-account watchlist contains the actual administrative identity used in the lab.

Public samples in this repository are sanitized.

## Cost-conscious design

The objective was not to maximize telemetry volume. It was to build a realistic SOC pipeline while maintaining a near-zero Azure operating footprint.

Controls include:

- filtered DCR instead of full Security log ingestion;
- no Defender for Servers paid plan;
- no Azure Update Manager enrollment for the Arc server;
- no high-volume Windows event categories unless a detection requires them;
- existing Log Analytics daily cap;
- watchlists used for context instead of ingesting more telemetry.

## Repository structure

```text
.
├── README.md
├── architecture/
│   └── solution-overview.md
├── docs/
│   ├── arc-onboarding.md
│   ├── security-design.md
│   ├── validation.md
│   ├── cost-control.md
│   └── lessons-learned.md
├── dcr/
│   └── ad-high-value-security-events.md
├── kql/
│   ├── validate-ad-ingestion.kql
│   ├── privileged-account-failed-logon.kql
│   └── validate-watchlists.kql
├── watchlists/
│   ├── CG-Tier0-Assets.sample.csv
│   └── CG-Privileged-Accounts.sample.csv
└── .gitignore
```

## Skills demonstrated

- Azure Arc-enabled Servers
- Azure Connected Machine Agent
- Azure Monitor Agent
- Azure Monitor Data Collection Rules
- Microsoft Sentinel
- Log Analytics
- KQL
- Windows Security auditing
- Active Directory security monitoring
- Sentinel watchlists
- hybrid cloud security architecture
- cost-aware SIEM engineering
- validation and troubleshooting

## Security note

This is a sanitized portfolio repository. Subscription IDs, tenant IDs, object IDs, credentials, tokens, and unnecessary internal details are excluded.

---

## Consulting relevance

This implementation demonstrates a repeatable approach for organizations that need to onboard hybrid Windows/Active Directory telemetry into Microsoft Sentinel without defaulting to uncontrolled log volume.

Typical consulting use cases include:

- Microsoft Sentinel onboarding and architecture;
- Azure Arc / AMA / DCR deployment;
- Active Directory security-event collection strategy;
- KQL detection engineering;
- privileged-identity and Tier-0 monitoring;
- ingestion-cost review and optimization;
- SOC validation, runbooks, and operational handoff.

**Consulting inquiries:** advisory@cloudgenius.ca · https://cloudgenius.ca

