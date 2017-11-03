---
title: "Állítsa be a kulcsot tároló a Windows virtuális gépek az Azure Resource Manager |} Microsoft Docs"
description: "Hogyan állítható be Key Vault az Azure Resource Manager virtuális gép való használatra."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a5083a5216efbfd76fd912ec48c2f0ec3b30c4a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>A virtuális gépek az Azure Resource Manager Key Vault beállítása

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager-készletben, a titkos kulcsokat vagy tanúsítványokat van modellezve a Key Vault erőforrás-szolgáltató által biztosított erőforrásokhoz. Key Vault kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Ahhoz, hogy az Azure Resource Manager virtuális gépekkel, használandó kulcstároló a **EnabledForDeployment** be kell állítani a Key Vault tulajdonságot igaz értékre. Ehhez a különböző ügyfelek részére.
> 2. A Key Vault kell létrehozni az előfizetés és a virtuális gép és a helyen.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Key Vault beállítása a PowerShell használatával
Kulcstároló létrehozása PowerShell használatával: [Ismerkedés az Azure Key Vault](../../key-vault/key-vault-get-started.md#vault).

Új kulcstárolók, a következő PowerShell-parancsmagot is használhatja:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Meglévő kulcstárolók, a következő PowerShell-parancsmagot is használhatja:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Parancssori felület beállítása a Key Vault us
Kulcstároló létrehozása a parancssori felület (CLI) használatával, lásd: [kezelése Key Vault parancssori felület használatával](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

A parancssori felületen kell létrehoznia a key vault a központi telepítési házirend hozzárendelése előtt. Ehhez futtassa az alábbi parancsot:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>A sablonok segítségével Key Vault beállítása
Amíg egy sablont használ, meg kell adnia a `enabledForDeployment` tulajdonságot `true` a Key Vault erőforrás.

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

Más beállításokat konfigurálhatja, ha a sablonok használatával hozzon létre egy kulcstartót, lásd: [hozzon létre egy kulcstartót](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
