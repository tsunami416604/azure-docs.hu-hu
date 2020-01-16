---
title: Linux rendszerű virtuális gép áthelyezése az Azure-ban
description: Linux rendszerű virtuális gép áthelyezése egy másik Azure-előfizetésre vagy erőforráscsoporthoz a Resource Manager-alapú üzemi modellben.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 2ba8a8cdc324f46e25f9665cfce0aa07fc948e88
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979038"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Linux rendszerű virtuális gép áthelyezése másik előfizetésre vagy erőforráscsoport-csoportba
Ez a cikk bemutatja, hogyan helyezhet át egy linuxos virtuális gépet (VM) Az erőforráscsoportok vagy előfizetések között. A virtuális gépek előfizetések közötti áthelyezése akkor lehet hasznos, ha létrehozott egy virtuális gépet egy személyes előfizetésben, és most át szeretné helyezni a céges előfizetésbe.

> [!IMPORTANT]
>Jelenleg nem helyezhető át az Azure Managed Disks.
>
>Az áthelyezés részeként új erőforrás-azonosítók jönnek létre. A virtuális gép áthelyezése után frissítenie kell az eszközöket és a parancsfájlokat az új erőforrás-azonosítók használatára.
>
>

## <a name="use-the-azure-cli-to-move-a-vm"></a>Virtuális gép áthelyezése az Azure CLI használatával


Mielőtt áthelyezi a virtuális gépet az Azure CLI-vel, meg kell győződnie arról, hogy a forrás-és a cél-előfizetések ugyanazon a bérlőn belül találhatók. Annak a megadásához, hogy mindkét előfizetés ugyanazzal a bérlői AZONOSÍTÓval rendelkezik-e, használja az [az Account show](/cli/azure/account)lehetőséget.

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Ha a forrás-és a cél-előfizetéshez tartozó bérlői azonosítók nem egyeznek meg, forduljon az [ügyfélszolgálathoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , és helyezze át az erőforrásokat egy új bérlőre.

A virtuális gép sikeres áthelyezéséhez át kell helyeznie a virtuális gépet és annak összes támogató erőforrását. Az az [Resource List](/cli/azure/resource) parancs használatával listázhatja az erőforráscsoport összes erőforrását és azonosítóját. Ez segít a parancs kimenetének egy fájlba való átirányításában, így az azonosítókat később is másolhatja és beillesztheti.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Ha egy virtuális gépet és erőforrásait egy másik erőforráscsoporthoz szeretné áthelyezni, használja [az az erőforrás áthelyezése](/cli/azure/resource)lehetőséget. Az alábbi példa bemutatja, hogyan helyezheti át a virtuális gépet és a szükséges leggyakoribb erőforrásokat. Használja az **-IDS** paramétert, és adja át az áthelyezni kívánt erőforrások azonosítóinak vesszővel tagolt listáját (szóközök nélkül).

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Ha át szeretné helyezni a virtuális gépet és annak erőforrásait egy másik előfizetésbe, adja hozzá a **--Destination-subscriptionId** paramétert a cél előfizetésének megadásához.

Ha a rendszer arra kéri, hogy erősítse meg, hogy szeretné-e áthelyezni a megadott erőforrásokat, adja meg az **Y** értéket a megerősítéshez.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Következő lépések
Több különböző típusú erőforrást is áthelyezhet az erőforráscsoportok és az előfizetések között. További információ: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
