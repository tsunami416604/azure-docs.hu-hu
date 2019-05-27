---
title: Állítsa be a Key Vault a Windows virtuális gépek az Azure Resource Managerben |} A Microsoft Docs
description: Hogyan állítható be a Key Vault az Azure Resource Manager virtuális géppel való használatra.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: e9d2b518af50aaec43bc7075e51829e80cdeddf5
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016133"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>A virtuális gépek az Azure Resource Manager Key Vault beállítása

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager veremben titkos kódok és tanúsítványok a Key vault erőforrás-szolgáltató által biztosított erőforrásként vannak modellezve. A Key Vaulttal kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Ahhoz, hogy a Key Vault az Azure Resource Manager virtuális gépeken, amely használható a **EnabledForDeployment** állítson be a Key Vault tulajdonság igaz értékre. Ezt megteheti a különböző ügyfelek részére.
> 2. A Key Vault kell létrehozni az ugyanazon előfizetésben és helyen a virtuális gép is.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Key Vault beállítása a PowerShell használatával
Kulcstartó létrehozása PowerShell használatával, lásd: [beállítása és lekérése egy titkos kulcsot az Azure Key vault PowerShell-lel](../../key-vault/quick-create-powershell.md).

Új kulcstartó a PowerShell-parancsmagot is használhatja:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

A meglévő kulcstartók a következő PowerShell-parancsmagot is használhatja:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Key Vault beállítása a parancssori felület használatával
Key vault létrehozása a parancssori felület (CLI) használatával, lásd: [kezelése a Key Vault parancssori felület használatával](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

CLI-hez akkor a key vault létrehozása előtt a központi telepítési szabályzatot rendel. Ehhez futtassa az alábbi parancsot:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Engedélyezi a Key Vault-sablonalapú telepítéssel használatra, majd futtassa a következő parancsot:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>A sablonok segítségével a Key Vault beállítása
Bár egy sablont használ, be kell állítania a `enabledForDeployment` tulajdonságot `true` a Key Vault erőforrás.

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

Egyéb beállítások, amelyek konfigurálásával megadhatja a sablonok használatával hozzon létre egy kulcstartót, lásd: [hozzon létre egy kulcstartót](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
