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
ms.openlocfilehash: f5aa04da40c6681385f0debc3999b94a1f507f05
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978986"
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
Kulcstartó létrehozása PowerShell használatával, lásd: [első lépései az Azure Key Vault](../../key-vault/key-vault-get-started.md#vault).

Új kulcstartó a PowerShell-parancsmagot is használhatja:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

A meglévő kulcstartók a következő PowerShell-parancsmagot is használhatja:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Key Vault beállítása a parancssori felület használatával
Key vault létrehozása a parancssori felület (CLI) használatával, lásd: [kezelése a Key Vault parancssori felület használatával](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

CLI-hez akkor a key vault létrehozása előtt a központi telepítési szabályzatot rendel. Ehhez futtassa az alábbi parancsot:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

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
