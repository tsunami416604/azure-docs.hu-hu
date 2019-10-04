---
title: A Windows VM-erőforrás áthelyezése az Azure-ban |} A Microsoft Docs
description: Windows virtuális gép áthelyezése egy másik Azure vagy erőforráscsoportonként csoportba a Resource Manager-alapú üzemi modellben.
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
ms.openlocfilehash: 6b189c4bfcc61084ed197649d376cae8fdf2eb56
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723087"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Windows virtuális gép áthelyezése egy másik Azure előfizetés vagy a resource group
Ez a cikk végigvezeti egy Windows virtuális gép (VM) áthelyezése erőforráscsoportok vagy előfizetések között. Az előfizetések közötti áthelyezése lehet hasznos, ha egy személyes előfizetés egy virtuális Gépet eredetileg létrehozták, és most át kívánja helyezni a vállalati előfizetésre, és folytatni a munkáját. Nem kell ahhoz, hogy helyezze át a virtuális gép elindításához, és továbbra is az áthelyezés idejére.

> [!IMPORTANT]
>Új erőforrás-azonosítók az áthelyezés során jönnek létre. Után a virtuális gép át lett helyezve, az eszközök és parancsfájlok használata az új erőforrás-azonosítók frissíteni kell. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Virtuális gép áthelyezése a Powershell használatával

Virtuális gép áthelyezése másik erőforráscsoportba, győződjön meg arról, hogy akkor is helyezze át az összes függő erőforrást kell. Egy listát az egyes ezeket az erőforrásokat az erőforrás-azonosító lekéréséhez használja a [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) parancsmagot.

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

Az erőforrás-azonosítók vesszővel elválasztott listáját is használhatja az előző parancs kimenetéből [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) az egyes erőforrások áthelyezése a célhelyre. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Az erőforrások áthelyezése másik előfizetésben, adjon meg a **- DestinationSubscriptionId** paraméter. 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Amikor a rendszer felkéri, hogy szeretné-e a megadott erőforrások áthelyezése, adja meg, ellenőrizze **Y** megerősítéséhez.

## <a name="next-steps"></a>További lépések
Számos különböző típusú erőforrások helyezheti át erőforráscsoportok és előfizetések között. További információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).    

