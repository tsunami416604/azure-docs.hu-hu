---
title: Linux rendszerű virtuális gép áthelyezése az Azure-ban |} A Microsoft Docs
description: Linux rendszerű virtuális gép áthelyezése egy másik Azure vagy erőforráscsoportonként csoportot a Resource Manager-alapú üzemi modellben.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
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
ms.openlocfilehash: c451377d6274c50f22e3b1d4cd32fb0f3edd9d9e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220393"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Linux rendszerű virtuális gép áthelyezése egy másik előfizetést vagy az erőforrás-csoport
Ez a cikk végigvezeti egy Linux rendszerű virtuális gép (VM) áthelyezése erőforráscsoportok vagy előfizetések között. Virtuális gép áthelyezése előfizetések között lehet hasznos, ha létrehozott egy virtuális Gépet egy személyes előfizetésben, és most át kívánja helyezni a vállalati előfizetéséhez.

> [!IMPORTANT]
>Jelenleg nem helyezhetők át az Azure Managed Disks. 
>
>Új erőforrás-azonosítók az áthelyezés során jönnek létre. Után a virtuális gép át lett helyezve, az eszközök és parancsfájlok használata az új erőforrás-azonosítók frissíteni kell. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Virtuális gép áthelyezése az Azure CLI használatával


A továbblépés előtt a virtuális gép az Azure CLI-vel, annak biztosításához, hogy a forrás- és az előfizetések ugyanahhoz a bérlőhöz létezniük kell. Ellenőrizze, hogy mindkét előfizetéshez tartozik-e az azonos bérlő azonosítója, használja a [az fiók show](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Ha a bérlőazonosítók a forrás- és előfizetés esetében nem ugyanaz, hogy kapcsolatba kell lépnie [támogatja](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) kell helyeznie az erőforrásokat az új bérlőhöz.

Sikerült áthelyezni egy virtuális gép, helyezze át a virtuális gép és a támogató erőforrások kell. Használja a [az erőforrások listájából](/cli/azure/resource#az_resource_list) paranccsal listát készíthet az erőforráscsoportot és a hozzájuk tartozó azonosítóik összes erőforrást. Segít pipe Ez a parancs kimenete egy fájlba, így másolja és illessze be az azonosítók újabb parancsok.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Egy virtuális Gépet és erőforrásainak áthelyezése másik erőforráscsoportba, használja a [az erőforrás-áthelyezési](/cli/azure/resource#az_resource_move). Az alábbi példa bemutatja, hogyan kívánja áthelyezni a virtuális gép és a leggyakrabban használt erőforrás-mennyiséggel. Használja a **-azonosítók** paramétert, és adja meg egy vesszővel tagolt (szóközök nélkül) azonosítók listáját az erőforrások áthelyezése.

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

Ha szeretné helyezni a virtuális gép és az erőforrások másik előfizetésbe való, vegye fel a **– cél-subscriptionId** paraméterrel adja meg a cél előfizetést.

Amikor a rendszer felkéri, hogy szeretné-e a megadott erőforrások áthelyezése, adja meg, ellenőrizze **Y** megerősítéséhez.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>További lépések
Számos különböző típusú erőforrások helyezheti át erőforráscsoportok és előfizetések között. További információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).    

