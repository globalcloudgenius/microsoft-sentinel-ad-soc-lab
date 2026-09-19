# Case Study — Microsoft Sentinel Hybrid AD SOC

## Executive summary

This project demonstrates a practical hybrid SOC implementation for collecting high-value on-premises Active Directory security telemetry in Microsoft Sentinel without defaulting to full-volume Windows Security log ingestion.

## Business problem

Organizations onboarding hybrid Windows infrastructure to a SIEM need security visibility without creating unnecessary ingestion cost, noise, or operational complexity.

The design therefore focused on selective collection, identity context, and validation.

## Implemented architecture

The implementation uses:

- an on-premises Windows Server 2025 domain controller;
- Azure Arc-enabled Servers;
- Azure Monitor Agent;
- a dedicated Data Collection Rule;
- Log Analytics;
- Microsoft Sentinel;
- KQL validation;
- watchlists for Tier-0 assets and privileged identities.

## Validated result

The pipeline was tested with live SecurityEvent data.

Validated examples include:

- Event ID 4672 — special privileges assigned to new logon;
- Event ID 4625 — failed logon.

A controlled failed-logon test generated Event ID 4625 and the event was visible in Sentinel.

Watchlists were also validated using KQL.

## Design decisions

The initial baseline intentionally excludes broad high-volume categories that are not required for the targeted detection objectives.

The goal is to demonstrate that SIEM engineering should begin with detection requirements and cost-awareness rather than indiscriminate log collection.

## Business value

This type of implementation supports:

- identity-focused threat detection;
- hybrid Windows monitoring;
- privileged-account visibility;
- Tier-0 asset monitoring;
- cost-conscious SIEM onboarding;
- audit and investigation readiness.

## Consulting outcome

A client engagement based on this design could include:

- telemetry discovery;
- Arc / AMA onboarding;
- DCR architecture;
- event-selection strategy;
- KQL development;
- Sentinel watchlists;
- analytics-rule design;
- validation testing;
- cost review;
- operational handoff.

## Evidence

See the main [README](./README.md) for the implemented architecture, event strategy, and validation record.

## Engagement fit

Relevant for:

- Microsoft Sentinel onboarding;
- hybrid SOC engineering;
- Active Directory monitoring;
- Azure Arc monitoring;
- KQL and detection engineering;
- SIEM cost optimization.

**Consulting inquiries:** advisory@cloudgenius.ca
