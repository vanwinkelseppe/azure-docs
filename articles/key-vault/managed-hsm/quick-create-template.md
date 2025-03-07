---
title: Azure Quickstart - Create a Managed HSM using an Azure Resource Manager template
description: Quickstart showing how to create Azure an Azure Key Vault Managed HSM using Resource Manager template
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/15/2020
ms.topic: quickstart
ms.service: key-vault
ms.subservice: managed-hsm
tags: azure-resource-manager
ms.custom: mvc, devx-track-azurecli, mode-other
#Customer intent: As a security admin who is new to Azure, I want to create a managed HSM using an Azure Resource Manager template.
---

# Quickstart: Create a Managed HSM using an Azure Resource Manager template

Managed HSM is a fully managed, highly available, single-tenant, standards-compliant cloud service that enables you to safeguards cryptographic keys for your cloud applications, using **FIPS 140-2 Level 3** validated HSMs.  

This quickstart focuses on the process of deploying a Resource Manager template to create a Managed HSM.  [Resource Manager template](../../azure-resource-manager/templates/overview.md) is a JavaScript Object Notation (JSON) file that defines the infrastructure and configuration for your project. The template uses declarative syntax, which lets you state what you intend to deploy without having to write the sequence of programming commands to create it. If you want to learn more about developing Resource Manager templates, see [Resource Manager documentation](../../azure-resource-manager/index.yml) and the [template reference](/azure/templates/microsoft.keyvault/allversions).

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

To complete the steps in this article, you must have the following items:

- A subscription to Microsoft Azure. If you don't have one, you can sign up for a [free trial](https://azure.microsoft.com/pricing/free-trial).
- The Azure CLI version 2.12.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install the Azure CLI]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## Sign in to Azure

To sign in to Azure using the CLI, you can type:

```azurecli
az login
```

For more information on login options via the CLI, see [sign in with Azure CLI](/cli/azure/authenticate-azure-cli)

## Create a Managed HSM

The template used in this quickstart is from [Azure Quickstart templates](https://azure.microsoft.com/resources/templates/managed-hsm-create/).

The Azure resource defined in the template:

* **Microsoft.KeyVault/managedHSMs**: create an Azure Key Vault Managed HSM.

More Azure Key Vault template samples can be found [here](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

The template requires the object ID associated with your account. To find it, use the Azure CLI [az ad user show](/cli/azure/ad/user#az-ad-user-show) command, passing your email address to the `--id` parameter. You can limit the output to the object ID only with the `--query` parameter.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

You may also need your tenant ID. To find it, use the Azure CLI [az ad user show](/cli/azure/account#az-account-show) command. You can limit the output to the tenant ID only with the `--query` parameter.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Select the following image to sign in to Azure and open a template. The template creates a Managed HSM.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2F%2Fmaster%2Fquickstarts%2Fmicrosoft.keyvault%2Fmanaged-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Select or enter the following values.

    Unless it is specified, use the default value to create the Managed HSM.

    - **Subscription**: Select an Azure subscription.
    - **Resource group**: Select **Create new**, enter a unique name for the resource group, and then select **OK**.
    - **Location**: Select a location. For example, **West US 2**.
    - **managedHSMName**: Enter a name for your Managed HSM.
    - **Tenant ID**: The template function automatically retrieves your tenant ID; don't change the default value.  If there is no value, enter the Tenant ID that you retrieved in [Prerequisites](#prerequisites).
    * **initialAdminObjectIds**: Enter the Object ID that you retrieved in [Prerequisites](#prerequisites).

3. Select **Purchase**. After the Managed HSM has been deployed successfully, you get a notification:

The Azure portal is used to deploy the template. In addition to the Azure portal, you can also use the Azure PowerShell, Azure CLI, and REST API. To learn other deployment methods, see [Deploy templates](../../azure-resource-manager/templates/deploy-powershell.md).

## Next steps

In this quickstart, you created a Managed HSM. This Managed HSM will not be fully functional until it is activated. See [Activate your Managed HSM](quick-create-cli.md#activate-your-managed-hsm) to learn how to activate your HSM.

- Read an [Overview of Managed HSM](overview.md)
- Learn about [Managing keys in a Managed HSM](key-management.md)
- Review [Managed HSM best practices](best-practices.md)
