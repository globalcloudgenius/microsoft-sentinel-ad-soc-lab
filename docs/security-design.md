# Security Design

## Azure Arc

The domain controller is represented in Azure as an Arc-enabled server. The Azure Connected Machine Agent maintains the connection to Azure.

The server itself remains on-premises.

## Azure Monitor Agent

The Azure Monitor Windows Agent extension was installed on AD-DC-01 and reached:

```text
provisioningState: Succeeded
status: Success
```

Automatic extension upgrade was enabled.

## Dedicated DCR

A dedicated rule was created:

```text
dcr-cloudgenius-ad-security
```

The DCR is associated only with AD-DC-01.

This prevents unrelated Windows servers from automatically inheriting the domain-controller event set.

## Security event selection

The initial collection focuses on events with strong security value:

- 1102 — Security audit log cleared
- 4625 — Failed logon
- 4648 — Explicit credentials used
- 4672 — Special privileges assigned
- 4697 — Service installed
- 4719 — Audit policy changed
- 4720/4722/4723/4724/4725/4726 — User lifecycle/password events
- 4727/4728/4729 — Global security group activity
- 4732/4733/4735/4737 — Local/global group changes
- 4739 — Domain policy changed
- 4740 — Account locked out
- 4742 — Computer account changed
- 4754/4755/4756/4757 — Universal security group activity
- 4767 — Account unlocked
- 4781 — Account renamed
- 4799 — Local group membership enumeration
- 5136/5137/5139/5141 — AD object modify/create/move/delete

## Intentionally excluded initially

The following high-volume events were not part of the initial baseline:

- 4624 — Successful logon
- 4688 — Process creation
- 4662 — Directory object access
- 4768 — Kerberos TGT request
- 4769 — Kerberos service ticket request
- 4776 — NTLM credential validation

These can be added later when a specific detection requirement justifies the increased ingestion.

## Defender

The Defender extension was intentionally left disabled on the Arc server.

This was a cost decision, not a statement that Defender is unnecessary in production. A licensed production environment should make that decision based on the organization's security controls, licensing, and risk model.

## Watchlists

Two initial watchlists provide context:

- `CG-Tier0-Assets`
- `CG-Privileged-Accounts`

These are used for KQL enrichment and will support analytics rules for high-risk events involving critical systems or privileged identities.
