---
title: A Key Vault beállítása
description: A Key Vault beállítása virtuális géppel való használatra.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: e4bff4d1826d9586495207095eccf8f6c66164a0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870010"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>A Key Vault beállítása virtuális gépekhez az Azure Resource Managerben

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Az Azure Resource Manager-veremben a titkos kulcsok/tanúsítványok a Key Vault erőforrás-szolgáltató által biztosított erőforrásokként vannak modellezve. Ha többet szeretne megtudni a Key Vaultról, olvassa el [a Mi az Azure Key Vault?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Ahhoz, hogy a Key Vault használható legyen az Azure Resource Manager virtuális gépekkel, a Key Vault **EnabledForDeployment** tulajdonságát igaz értékre kell állítani. Ezt különböző ügyfeleken teheti meg.
> 2. A Key Vault kell létrehozni ugyanabban az előfizetésben és helyen, mint a virtuális gép.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>A Key Vault beállítása a PowerShell használatával
Ha a PowerShell használatával szeretne létrehozni egy kulcstartót, olvassa el a Titkos kulcs beállítása és lekérése az [Azure Key Vaultból a PowerShell használatával című témakört.](../../key-vault/secrets/quick-create-powershell.md)

Új kulcstartók esetén használhatja ezt a PowerShell-parancsmac:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Meglévő kulcstartók esetén használhatja ezt a PowerShell-parancsmast:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>A CLI használata a Key Vault beállítása
Ha a parancssori csatolóval (CLI) szeretne létrehozni egy kulcstartót, olvassa el a Key Vault kezelése a CLI használatával című [témakört.](../../key-vault/general/manage-with-cli2.md#create-a-key-vault)

A CLI, létre kell hoznia a key vault hozzárendelése előtt a központi telepítési szabályzat. Ehhez futtassa az alábbi parancsot:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Ezután engedélyezze a Key Vault sablontelepítéssel való használatát, futtassa a következő parancsot:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>A Key Vault beállítása sablonok használatával
Sablon használata közben be kell állítania `enabledForDeployment` `true` a tulajdonságot a Key Vault erőforráshoz.

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

A key vault sablonok használatával történő létrehozásakor konfigurálható egyéb beállításokról a [Key Vault létrehozása című](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)témakörben olvashat.
