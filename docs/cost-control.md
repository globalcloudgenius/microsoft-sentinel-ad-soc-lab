# Cost-Control Strategy

The implementation was designed around a very small lab budget.

## What was intentionally enabled

- Azure Arc server connection
- Azure Monitor Agent
- filtered Windows Security event ingestion
- Microsoft Sentinel workspace
- small Sentinel watchlists

## What was intentionally not enabled

- Defender for Servers paid plan
- Defender extension on AD-DC-01
- Azure Update Manager for the Arc server
- full Windows Security event collection
- high-volume Kerberos/NTLM telemetry
- verbose process creation telemetry
- broad directory-object access auditing

## Why

The goal was to retain meaningful SOC visibility without turning the domain controller into a large telemetry source.

The lab did not have a qualifying Windows Server subscription/Software Assurance entitlement for Arc Update Manager, so patching remained an on-premises responsibility rather than adding a recurring Azure service charge.

## Ingestion control

The DCR is the primary ingestion control.

Instead of sending every Security event, only a defined high-value list is collected. This makes the cost model easier to understand and tune.

The existing Log Analytics workspace also has a small daily ingestion cap as an additional guardrail.

## Production perspective

A real enterprise may intentionally ingest substantially more telemetry and purchase Defender for Servers or other security services.

The portfolio design demonstrates the engineering decision process: start with security value, understand data volume, measure actual ingestion, and add telemetry only when there is a detection requirement.
