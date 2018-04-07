---
title: Windowsos VM erőforrás áthelyezése az Azure-ban |} Microsoft Docs
description: A Windows virtuális gépek áthelyezése egy másik Azure-előfizetés vagy az erőforrás-csoportok a Resource Manager üzembe helyezési modellben.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: b98b8c947fb34b60c7bd27b006672e0e9d923d3b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>A Windows virtuális gépek áthelyezése egy másik Azure-előfizetés vagy az erőforrás-csoportok
Ez a cikk végigvezeti a Windows virtuális gépek áthelyezése erőforráscsoportok vagy előfizetések között. Az előfizetések közötti áthelyezés lehet hasznos, ha az eredetileg létrehozott virtuális gép személyes előfizetés, és most szeretné helyezze át a vállalat előfizetésének a munka folytatásához.

> [!IMPORTANT]
>Felügyelt lemezek jelenleg nem helyezhető át. 
>
>Új erőforrás-azonosítók az áthelyezés részeként jönnek létre. Miután a virtuális gép át lett helyezve, módosítania az eszközök és parancsfájlok használata az új erőforrás-azonosítók. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Helyezze át a virtuális Gépet a Powershell használatával

A virtuális gép áthelyezése egy másik erőforráscsoportban, győződjön meg arról, hogy Ön is a függő erőforrásokat áthelyezni kell. A Move-AzureRMResource parancsmagot használja, az erőforrás-azonosítója az egyes erőforrások kell. Az erőforrás-azonosítója használatával listája kaphat a [keresés-AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource) parancsmag.

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Helyezze át a virtuális Gépet kell a több erőforrások áthelyezése. Is használjuk keresés-AzureRMResource kimenetének létrehozása az erőforrás-azonosítók vesszővel tagolt listáját, és adja át, amely a [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) áthelyezheti őket a cél. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Az erőforrások áthelyezése másik előfizetést, tartalmazza a **- DestinationSubscriptionId** paraméter. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Győződjön meg arról, hogy szeretné-e a megadott erőforrások áthelyezése fog kérni. 

## <a name="next-steps"></a>További lépések
Számos különböző típusú erőforrások áthelyezheti erőforráscsoport-sablonok és előfizetések között. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).    

