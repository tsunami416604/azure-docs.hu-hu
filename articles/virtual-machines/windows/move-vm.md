---
title: A Windows VM-erőforrás áthelyezése az Azure-ban |} A Microsoft Docs
description: Windows virtuális gép áthelyezése egy másik Azure vagy erőforráscsoportonként csoportba a Resource Manager-alapú üzemi modellben.
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
ms.openlocfilehash: 168ba57399b2649af29820f7321dd0151618346e
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436480"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Windows virtuális gép áthelyezése egy másik Azure előfizetés vagy a resource group
Ez a cikk végigvezeti egy Windows virtuális gép áthelyezése erőforráscsoportok vagy előfizetések között. Az előfizetések közötti áthelyezése lehet hasznos, ha egy személyes előfizetés egy virtuális Gépet eredetileg létrehozták, és most át kívánja helyezni a vállalati előfizetésre, és folytatni a munkáját.

> [!IMPORTANT]
>Managed Disks jelenleg nem lehet áthelyezni. 
>
>Új erőforrás-azonosítók az áthelyezés során jönnek létre. Miután a virtuális gép át lett helyezve, az eszközök és parancsfájlok használata az új erőforrás-azonosítók frissíteni szeretné. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Virtuális gép áthelyezése a Powershell használatával

Virtuális gép áthelyezése másik erőforráscsoportba, győződjön meg arról, hogy akkor is helyezze át az összes függő erőforrást kell. Használja a Move-AzureRMResource parancsmagot, az erőforrás-azonosító, az egyes erőforrásokat kell. Az erőforrás-azonosító használatával listát kap a [Get-AzureRMResource](/powershell/module/azurerm.resources/get-azurermresource) parancsmagot.

```azurepowershell-interactive
 Get-AzureRMResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

A virtuális gép áthelyezése kell áthelyezni több erőforrást. A Get-AzureRMResource kimenetét használatával hozzon létre egy vesszővel elválasztott listáját a ResourceId és el kell küldenie a [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) áthelyezi őket a célhelyre. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Az erőforrások áthelyezése másik előfizetésben, adjon meg a **- DestinationSubscriptionId** paraméter. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Annak ellenőrzéséhez, hogy szeretné-e a megadott erőforrások helyezhetők át kell adnia. 

## <a name="next-steps"></a>További lépések
Számos különböző típusú erőforrások helyezheti át erőforráscsoportok és előfizetések között. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).    

