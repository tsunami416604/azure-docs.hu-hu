---
title: Key Vault beállítása a Windows rendszerű virtuális gépekhez Azure Resource Manager
description: Key Vault beállítása Azure Resource Manager virtuális géppel való használatra.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a64163da1dee2bceb567436dc18ba0fa5274cfcb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243145"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Key Vault beállítása virtuális gépekhez Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager veremben a titkok/tanúsítványok a Key Vault erőforrás-szolgáltatója által biztosított erőforrásokként vannak modellezve. További információ a Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)

> [!NOTE]
> 1. Ahhoz, hogy a Key Vault Azure Resource Manager virtuális gépekkel is használhatók legyenek, a Key Vault **EnabledForDeployment** tulajdonságát True értékre kell állítani. Ezt megteheti különböző ügyfeleken.
> 2. A Key Vault a virtuális géppel megegyező előfizetésben és helyen kell létrehozni.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Key Vault beállítása a PowerShell használatával
Ha a PowerShell használatával szeretne kulcstartót létrehozni, olvassa el [a titkos kód beállítása és lekérése Azure Key Vault a PowerShell használatával](../../key-vault/quick-create-powershell.md)című témakört.

Új kulcstartók esetén ezt a PowerShell-parancsmagot használhatja:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

A meglévő kulcstartók esetében ezt a PowerShell-parancsmagot használhatja:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Key Vault beállítása a CLI használatával
Key Vault parancssori felületen (CLI) való létrehozásával kapcsolatban lásd: [Key Vault kezelése a CLI használatával](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

A CLI esetében létre kell hoznia a kulcstartót, mielőtt hozzárendeli a központi telepítési szabályzatot. Ehhez futtassa az alábbi parancsot:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Ezután az Key Vault a sablonok központi telepítésének engedélyezéséhez futtassa a következő parancsot:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Sablonok használata a Key Vault beállításához
A sablon használatakor a `enabledForDeployment` tulajdonságot a Key Vault erőforráshoz tartozó `true`re kell állítania.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

A Key Vault sablonok használatával történő létrehozásakor konfigurálható egyéb beállításokért lásd: [kulcstartó létrehozása](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
