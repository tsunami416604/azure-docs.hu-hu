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
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 459e0d591e2279b63864a273f713e4c1df8c0858
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
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


A továbblépés előtt a virtuális Gépet a parancssori felület használatával, ellenőrizze, hogy a forrás és cél előfizetések vannak ugyanannak a bérlőnek kell. Ellenőrizze, hogy mindkét előfizetéshez tartozik-e az azonos Bérlőazonosító, használja a [az fiók megjelenítése](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Ha a bérlő azonosítók a forrás és cél előfizetésekhez nem egyeznek, vegye fel a kapcsolatot [támogatja](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az erőforrások áthelyezése új bérlőhöz.

Sikeresen helyezi át a virtuális gép, helyezze át a virtuális gép és annak támogató erőforrásokat kell. Használja a [az erőforráslistát](/cli/azure/resource#az_resource_list) paranccsal listát készíthet az erőforráscsoportot és az azonosítók abban tárolt összes erőforrás. Ez a parancs kimenetében pipe fájlba, így másolja és illessze be az azonosítók újabb parancsok segíti.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

A virtuális gépek és az erőforrások áthelyezése egy másik erőforráscsoportban, használja a [az erőforrás-áthelyezés](/cli/azure/resource#az_resource_move). A következő példa bemutatja, hogyan kívánja áthelyezni a virtuális gép és a leggyakrabban használt erőforrásokat igényel. Használja a **-azonosítók** paraméter és egy vesszővel tagolt (szóközök nélkül) azonosítók listáját az erőforrások áthelyezése adjon át.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageacountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Ha azt szeretné, a virtuális gép és az erőforrások áthelyezése egy másik előfizetést, vegye fel a **– cél-subscriptionId** paraméterrel adhatja meg a célként megadott előfizetés.

Ha a rendszer felkéri győződjön meg arról, hogy szeretné-e a megadott erőforrás áthelyezése. Típus **Y** annak ellenőrzéséhez, hogy szeretné-e az erőforrások áthelyezése.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Következő lépések
Számos különböző típusú erőforrások áthelyezheti erőforráscsoport-sablonok és előfizetések között. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).    

