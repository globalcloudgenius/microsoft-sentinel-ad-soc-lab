# Onboarding an On-Premises Domain Controller to Azure Arc

## Objective

The goal was to bring an existing on-premises Active Directory domain controller under Azure management without migrating the VM into Azure.

The machine remained on-premises and continued to operate as a normal Windows Server domain controller. Azure Arc was used only to establish a management/control-plane relationship with Azure.

## Source system

- Hostname: `AD-DC-01`
- OS: Windows Server 2025 Standard Evaluation
- Workload: Active Directory Domain Services
- Deployment location: on-premises virtual infrastructure
- Azure destination resource group: dedicated Arc server resource group
- Azure region: Canada Central

## Arc onboarding flow

The Azure portal was used to generate the Azure Connected Machine onboarding script.

The script:

1. checked Azure connectivity;
2. downloaded the latest Azure Connected Machine Agent MSI;
3. validated the MSI signature;
4. installed `azcmagent`;
5. prompted for Microsoft Entra authentication;
6. created the `Microsoft.HybridCompute/machines` resource in Azure;
7. retrieved the machine certificate;
8. completed the Arc connection.

The important successful output was:

```text
Installation of azcmagent completed successfully
INFO    Connecting machine to Azure...
INFO    Creating resource in Azure...
INFO    Retrieving certificate...
INFO    Connected machine to Azure
```

The Arc portal subsequently showed the machine with status **Connected**.

## Authentication and RBAC

The normal user identity initially failed interactive authentication with an Entra password error. RBAC was checked at the target resource-group scope.

Two user identities inherited sufficient permissions from the Dev subscription. A functioning administrative Entra identity was used for onboarding.

For a mature deployment, the preferred approach is to assign the dedicated **Azure Connected Machine Onboarding** role at the narrowest required scope rather than relying on Owner.

## Resource organization

A general resource group was created for Arc-enabled servers:

```text
rg-cloudgenius-arc-servers
```

This separates hybrid server inventory from workload-specific Azure resources and provides a clean scope for RBAC, policy, and future lifecycle management.

## What Azure Arc did not do

Azure Arc did **not**:

- migrate the VM into Azure;
- replace Active Directory;
- convert the server into an Azure VM;
- automatically license Windows Server;
- automatically enable Defender for Servers;
- automatically enable Azure Update Manager.

Those services remain separate decisions.

## Windows Server licensing position

The lab server was running **Windows Server 2025 Standard Evaluation** and was not backed by a qualifying Windows Server subscription or Software Assurance entitlement for paid Azure Arc add-ons.

For that reason, this lab intentionally avoided enabling services such as Azure Update Manager where they could introduce recurring Arc-server charges.

The Arc connection itself was used for inventory, management integration, and AMA extension deployment.
