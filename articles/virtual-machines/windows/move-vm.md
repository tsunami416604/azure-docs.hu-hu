---
title: Windows virtuálisgép-erőforrás áthelyezése az Azure-ban
description: Windows virtuális gép áthelyezése egy másik Azure-előfizetésbe vagy erőforráscsoportba az Erőforrás-kezelő telepítési modelljében.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: ed29c92d20a6b0d749ec44a22f42ec446ec58650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919566"
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
