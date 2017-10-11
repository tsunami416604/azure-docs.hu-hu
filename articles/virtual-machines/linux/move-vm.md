---
title: "A Linux virtuális gépek áthelyezése az Azure-ban |} Microsoft Docs"
description: "Linux virtuális gép áthelyezése egy másik Azure-előfizetés vagy az erőforrás-csoportok a Resource Manager üzembe helyezési modellben."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 4695a9c934f97f2b2d448c4990e7ad5533e38e9f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Linux virtuális gép áthelyezése egy másik előfizetés vagy az erőforrás-csoport
Ez a cikk végigvezeti a Linux virtuális gépek áthelyezése erőforráscsoportok vagy előfizetések között. A virtuális gépek áthelyezése másik előfizetések lehet hasznos, ha egy virtuális Gépet egy személyes előfizetésben hozott létre, és most szeretné helyezze át a vállalat előfizetés.

> [!IMPORTANT]
>Felügyelt lemezek jelenleg nem helyezhető át. 
>
>Új erőforrás-azonosítók az áthelyezés részeként jönnek létre. Miután a virtuális gép át lett helyezve, módosítania az eszközök és parancsfájlok használata az új erőforrás-azonosítók. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Helyezze át a virtuális Gépet az Azure parancssori felület használatával
Sikeresen helyezi át a virtuális gép, helyezze át a virtuális gép és annak támogató erőforrásokat kell. Használja a **azure-csoportok megjelenítése** paranccsal listát készíthet az erőforráscsoportot és az azonosítók abban tárolt összes erőforrás. Ez a parancs kimenetében pipe fájlba, így másolja és illessze be az azonosítók újabb parancsok segíti.

    azure group show <resourceGroupName>

A virtuális gépek és az erőforrások áthelyezése egy másik erőforráscsoportban, használja a **az azure erőforrás-áthelyezés** CLI parancsot. A következő példa bemutatja, hogyan kívánja áthelyezni a virtuális gép és a leggyakrabban használt erőforrásokat igényel. Használjuk a **-i** paraméter és egy vesszővel tagolt (szóközök nélkül) azonosítók listáját az erőforrások áthelyezése adjon át.

    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      

    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"

Ha azt szeretné, a virtuális gép és az erőforrások áthelyezése egy másik előfizetést, vegye fel a **– cél-subscriptionId &#60; destinationSubscriptionID &#62;** paraméterrel adhatja meg a célként megadott előfizetés.

Ha Windows-számítógépen a parancssorból dolgozik, akkor hozzon létre egy  **$**  a változó neve, amikor azok deklarálhatja elé. Ez a Linux nem szükséges.

Győződjön meg arról, hogy szeretné-e a megadott erőforrás áthelyezése kérni. Típus **Y** annak ellenőrzéséhez, hogy szeretné-e az erőforrások áthelyezése.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Következő lépések
Számos különböző típusú erőforrások áthelyezheti erőforráscsoport-sablonok és előfizetések között. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).    

