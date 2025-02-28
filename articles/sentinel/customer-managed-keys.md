---
title: Set up customer-managed keys in Microsoft Sentinel| Microsoft Docs
description: Learn how to set up customer-managed keys (CMK) in Microsoft Sentinel.
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021, ignite-2022
---

# Set up Microsoft Sentinel customer-managed key

This article provides background information and steps to configure a [customer-managed key (CMK)](../azure-monitor/logs/customer-managed-keys.md) for Microsoft Sentinel. CMK allows you to give all data stored in Microsoft Sentinel - already encrypted by Microsoft in all relevant storage resources - an extra layer of protection with an encryption key created and owned by you and stored in your [Azure Key Vault](../key-vault/general/overview.md).

## Prerequisites

- The CMK capability requires a Log Analytics dedicated cluster with at least a 500 GB/day commitment tier. Multiple workspaces can be linked to the same dedicated cluster, and they will share the same customer-managed key.

- You must contact the [Microsoft Sentinel Product Group](mailto:azuresentinelCMK@microsoft.com) for onboarding confirmation as part of completing the steps in this guide and before you use the workspace.

- Learn about [Log Analytics Dedicated Cluster Pricing](../azure-monitor/logs/logs-dedicated-clusters.md#cluster-pricing-model).

## Considerations

- Onboarding a CMK workspace to Sentinel is supported only via REST API, and not via the Azure portal. Azure Resource Manager templates (ARM templates) currently aren't supported for CMK onboarding.

- The Microsoft Sentinel CMK capability is provided only to *workspaces in Log Analytics dedicated clusters* that have *not already been onboarded to Microsoft Sentinel*.

- The following CMK-related changes *are not supported* because they are ineffective (Microsoft Sentinel data continues to be encrypted only by the Microsoft-managed key, and not by the CMK):

  - Enabling CMK on a workspace that's *already onboarded* to Microsoft Sentinel.
  - Enabling CMK on a cluster that contains Sentinel-onboarded workspaces.
  - Linking a Sentinel-onboarded non-CMK workspace to a CMK-enabled cluster.

- The following CMK-related changes *are not supported* because they may lead to undefined and problematic behavior:

  - Disabling CMK on a workspace already onboarded to Microsoft Sentinel.
  - Setting a Sentinel-onboarded, CMK-enabled workspace as a non-CMK workspace by de-linking it from its CMK-enabled dedicated cluster.
  - Disabling CMK on a CMK-enabled Log Analytics dedicated cluster.

- Microsoft Sentinel supports System Assigned Identities in CMK configuration. Therefore, the dedicated Log Analytics cluster's identity should be of **System Assigned** type. We recommend that you use the identity that's automatically assigned to the Log Analytics cluster when it's created.

- Changing the customer-managed key to another key (with another URI) currently *isn't supported*. You should change the key by [rotating it](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

- Before you make any CMK changes to a production workspace or to a Log Analytics cluster, contact the [Microsoft Sentinel Product Group](mailto:azuresentinelCMK@microsoft.com).
- CMK enabled workspaces don't support [search jobs](investigate-large-datasets.md).

## How CMK works 

The Microsoft Sentinel solution uses several storage resources for log collection and features, including a Log Analytics dedicated cluster. As part of the Microsoft Sentinel CMK configuration, you must configure the CMK settings on the related Log Analytics dedicated cluster. Data saved by Microsoft Sentinel in storage resources other than Log Analytics is also encrypted using the customer-managed key configured for the dedicated Log Analytics cluster.

For more information, see:
- [Azure Monitor customer-managed keys (CMK)](../azure-monitor/logs/customer-managed-keys.md).
- [Azure Key Vault](../key-vault/general/overview.md).
- [Log Analytics dedicated clusters](../azure-monitor/logs/logs-dedicated-clusters.md).

> [!NOTE]
> If you enable CMK on Microsoft Sentinel, any Public Preview feature that does not support CMK aren't enabled.

## Enable CMK 

To provision CMK, follow these steps: 

1.  Create an Azure Key Vault and generate or import a key.

2.  Enable CMK on your Log Analytics workspace.

3.  Register to the Azure Cosmos DB Resource Provider.

4.  Add an access policy to your Azure Key Vault instance.
5.  Contact the Microsoft Sentinel Product group to confirm onboarding 
6.  Onboard the workspace to Microsoft Sentinel via the [Onboarding API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx).

### STEP 1: Create an Azure Key Vault and generate or import a key

1. [Create Azure Key Vault resource](/azure-stack/user/azure-stack-key-vault-manage-portal), then generate or import a key to be used for data encryption.

    > [!NOTE]
    >  Azure Key Vault must be configured as recoverable to protect your key and the access.

1.  [Turn on recovery options:](../key-vault/general/key-vault-recovery.md)

    -   Make sure [Soft Delete](../key-vault/general/soft-delete-overview.md) is turned on.

    -   Turn on [Purge protection](../key-vault/general/soft-delete-overview.md#purge-protection) to guard against forced deletion of the secret/vault even after soft delete.

### STEP 2: Enable CMK on your Log Analytics workspace

Follow the instructions in [Azure Monitor customer-managed key configuration](../azure-monitor/logs/customer-managed-keys.md) in order to create a CMK workspace that is used as the Microsoft Sentinel workspace in the following steps.

### STEP 3: Register to the Azure Cosmos DB Resource Provider

Microsoft Sentinel works with Azure Cosmos DB as an additional storage resource. Make sure to register to the Azure Cosmos DB Resource Provider.

Follow the instructions to [Register the Azure Cosmos DB Resource Provider](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) for your Azure subscription.

### STEP 4: Add an access policy to your Azure Key Vault instance

Make sure to add access from Azure Cosmos DB to your Azure Key Vault instance. Follow the Azure Cosmos DB instructions to [add an access policy to your Azure Key Vault instance](../cosmos-db/how-to-setup-cmk.md#add-access-policy) with an Azure Cosmos DB principal.

### STEP 5: Contact the Microsoft Sentinel Product group to confirm onboarding 

You must confirm onboarding of your CMK enabled workspace by contacting the [Microsoft Sentinel Product Group](mailto:azuresentinelCMK@microsoft.com).

### STEP 6: Onboard the workspace to Microsoft Sentinel via the onboarding API

Onboard the workspace to Microsoft Sentinel via the [Onboarding API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx).

## Key Encryption Key revocation or deletion

If a user revokes the key encryption key (the CMK), either by deleting it or removing access for the dedicated cluster and Azure Cosmos DB Resource Provider, Microsoft Sentinel honors the change and behave as if the data is no longer available, within one hour. At this point, any operation that uses persistent storage resources such as data ingestion, persistent configuration changes, and incident creation, is prevented. Previously stored data isn't deleted but remains inaccessible. Inaccessible data is governed by the data-retention policy and is purged in accordance with that policy.

The only operation possible after the encryption key is revoked or deleted is account deletion.

If access is restored after revocation, Microsoft Sentinel restores access to the data within an hour.

Access to the data can be revoked by disabling the customer-managed key in the key vault, or deleting the access policy to the key, for both the dedicated Log Analytics cluster and Azure Cosmos DB. Revoking access by removing the key from the dedicated Log Analytics cluster, or by removing the identity associated with the dedicated Log Analytics cluster isn't supported.

To understand more about how this works in Azure Monitor, see [Azure Monitor CMK revocation](../azure-monitor/logs/customer-managed-keys.md#key-revocation).

## Customer-managed key rotation

Microsoft Sentinel and Log Analytics support key rotation. When a user performs key rotation in Key Vault, Microsoft Sentinel supports the new key within an hour.

In Key Vault, you can perform key rotation by creating a new version of the key:

![key rotation](./media/customer-managed-keys/key-rotation.png)

You can disable the previous version of the key after 24 hours, or after the Azure Key Vault audit logs no longer show any activity that uses the previous version.

After rotating a key, you must explicitly update the dedicated Log Analytics cluster resource in Log Analytics with the new Azure Key Vault key version. For more information, see [Azure Monitor CMK rotation](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

## Replacing a customer-managed key

Microsoft Sentinel doesn't support replacing a customer-managed key. You should use the [key rotation capability](#customer-managed-key-rotation) instead.

## Next steps
In this document, you learned how to set up a customer-managed key in Microsoft Sentinel. To learn more about Microsoft Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](get-visibility.md).
- Get started [detecting threats with Microsoft Sentinel](./detect-threats-built-in.md).
- [Use workbooks](monitor-your-data.md) to monitor your data.
