# Lessons Learned

## Arc is management-plane integration, not migration

Connecting AD-DC-01 to Azure Arc did not move the VM to Azure. The domain controller stayed on-premises while Azure gained a managed representation of the machine.

## Licensing and add-ons are separate decisions

The Arc connection itself did not automatically provide a Windows Server production license, Defender for Servers, or free Azure Update Manager entitlement.

That distinction mattered because the lab was deliberately operated under a very small Azure budget.

## AMA alone does not collect useful logs

Installing Azure Monitor Agent is only one part of the pipeline. The DCR determines what data is collected and where it is sent.

## Narrow collection can still produce useful SOC telemetry

A small event set was enough to validate failed authentication and privileged-logon visibility without collecting the entire Windows Security log.

## Validate with a controlled event

Generating a deliberate failed logon provided a clean end-to-end test of:

```text
AD-DC-01 -> AMA -> DCR -> Log Analytics -> Sentinel
```

## Watchlists add context without increasing event ingestion

The Tier-0 and privileged-account watchlists make KQL more useful without requiring additional Windows telemetry.

## Public portfolio material must be sanitized

Cloud subscription identifiers, tenant identifiers, object IDs, credentials, and unnecessary internal details were excluded from the public repository.
