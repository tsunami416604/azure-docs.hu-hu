---
title: Windows virtuálisgép-erőforrás áthelyezése az Azure-ban
description: Windows virtuális gép áthelyezése egy másik Azure-előfizetésbe vagy erőforráscsoportba az Erőforrás-kezelő telepítési modelljében.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 0c3f5541405d1fd983bbf988b99d2b4e10d8908c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865663"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Windows rendszerű virtuális gép áthelyezése más Azure-előfizetésbe vagy erőforráscsoportba
Ez a cikk bemutatja, hogyan helyezhet át egy Windows virtuális gépet (VM) erőforráscsoportok vagy előfizetések között. Az előfizetések közötti váltás akkor lehet hasznos, ha eredetileg egy virtuális gép egy személyes előfizetés, és most szeretné áthelyezni a vállalat előfizetését, hogy folytassa a munkát. Nem kell elindítani a virtuális gép áthelyezése érdekében, és továbbra is fut az áthelyezés során.

> [!IMPORTANT]
>Az áthelyezés részeként új erőforrásazonosítók jönnek létre. A virtuális gép áthelyezése után frissítenie kell az eszközöket és a parancsfájlokat az új erőforrás-azonosítók használatához.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Virtuális gép áthelyezése a PowerShell használatával

Virtuális gép áthelyezése egy másik erőforráscsoportba, győződjön meg arról, hogy az összes függő erőforrást is áthelyezi. A [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) parancsmag használatával az egyes erőforrások erőforrás-azonosítóját tartalmazó lista lekért.

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Az előző parancs kimenetének használatával vesszővel tagolt listát hozhat létre az [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) erőforráshoz, hogy az egyes erőforrásokat áthelyezhesse a célhelyre.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Az erőforrások áthelyezése egy másik előfizetés, adja meg a **-DestinationSubscriptionId** paraméter.

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Amikor a rendszer megkéri, hogy erősítse meg, hogy át kívánja helyezni a megadott erőforrásokat, adja meg az **Y** értéket a megerősítéshez.

## <a name="next-steps"></a>További lépések
Az erőforráscsoportok és az előfizetések között számos különböző típusú erőforrást helyezhet át. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)    
