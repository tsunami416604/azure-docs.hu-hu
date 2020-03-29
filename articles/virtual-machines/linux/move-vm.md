---
title: Virtuális gép áthelyezése az Azure CLI használatával
description: Virtuális gép áthelyezése egy másik Azure-előfizetésbe vagy erőforráscsoportba az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: ebcd5f166fd1876f67121787c23d23860c9fa4b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944589"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Virtuális gép áthelyezése másik előfizetésbe vagy erőforráscsoportba
Ez a cikk bemutatja, hogyan helyezhet át egy virtuális gépet (VM) erőforráscsoportok vagy előfizetések között. A virtuális gép előfizetések közötti áthelyezése akkor lehet hasznos, ha létrehozott egy virtuális számítógépet egy személyes előfizetésben, és most át szeretné helyezni a vállalat előfizetésébe.

> [!IMPORTANT]
>Az áthelyezés részeként új erőforrásazonosítók jönnek létre. A virtuális gép áthelyezése után frissítenie kell az eszközöket és a parancsfájlokat az új erőforrás-azonosítók használatához.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Virtuális gép áthelyezése az Azure CLI segítségével


Mielőtt áthelyezheti a virtuális gép az Azure CLI használatával, meg kell győződnie arról, hogy a forrás- és cél-előfizetések ugyanazon a bérlőn belül léteznek. Annak ellenőrzéséhez, hogy mindkét előfizetés azonos bérlői azonosítóval rendelkezik-e, használja az [az-fiókmegjelenítést.](/cli/azure/account)

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Ha a forrás- és cél-előfizetések bérlői azonosítói nem azonosak, az erőforrások új bérlőbe való áthelyezéséhez forduljon az [ügyfélszolgálathoz.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

A virtuális gép sikeres áthelyezéséhez át kell helyeznie a virtuális gép és az összes támogató erőforrását. Az [erőforráslista](/cli/azure/resource) paranccsal az erőforráscsoport összes erőforrását és azonosítóját listázta. Segít a parancs kimenetének egy fájlba való átirányításában, így az azonosítókat átmásolhatja és beillesztheti a későbbi parancsokba.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Ha egy virtuális gép és annak erőforrásait egy másik erőforráscsoportba szeretné áthelyezni, használja [az erőforrás áthelyezése](/cli/azure/resource). A következő példa bemutatja, hogyan helyezheti át a virtuális gépeket és a szükséges leggyakoribb erőforrásokat. Használja az **-ids paramétert,** és adja át az erőforrások áthelyezéséhez az azonosítók vesszővel tagolt listájában (szóközök nélkül).

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

Ha át szeretné helyezni a virtuális gép és az erőforrások egy másik előfizetés, adja hozzá a **--destination-subscriptionId** paraméter a cél-előfizetés megadása.

Amikor a rendszer megkéri, hogy erősítse meg, hogy át kívánja helyezni a megadott erőforrásokat, adja meg az **Y** értéket a megerősítéshez.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>További lépések
Az erőforráscsoportok és az előfizetések között számos különböző típusú erőforrást helyezhet át. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)    
