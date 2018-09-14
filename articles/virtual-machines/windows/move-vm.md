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
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 1daf04e3f878d0748bfa0904259c7b7187481843
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580486"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Windows virtuális gép áthelyezése egy másik Azure előfizetés vagy a resource group
Ez a cikk végigvezeti egy Windows virtuális gép (VM) áthelyezése erőforráscsoportok vagy előfizetések között. Az előfizetések közötti áthelyezése lehet hasznos, ha egy személyes előfizetés egy virtuális Gépet eredetileg létrehozták, és most át kívánja helyezni a vállalati előfizetésre, és folytatni a munkáját.

> [!IMPORTANT]
>Jelenleg nem helyezhetők át az Azure Managed Disks. 
>
>Új erőforrás-azonosítók az áthelyezés során jönnek létre. Után a virtuális gép át lett helyezve, az eszközök és parancsfájlok használata az új erőforrás-azonosítók frissíteni kell. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Virtuális gép áthelyezése a Powershell használatával

Virtuális gép áthelyezése másik erőforráscsoportba, győződjön meg arról, hogy akkor is helyezze át az összes függő erőforrást kell. Egy listát az egyes ezeket az erőforrásokat az erőforrás-azonosító lekéréséhez használja a [Get-AzureRMResource](/powershell/module/azurerm.resources/get-azurermresource) parancsmagot.

```azurepowershell-interactive
 Get-AzureRMResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Az erőforrás-azonosítók vesszővel elválasztott listáját is használhatja az előző parancs kimenetéből [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) az egyes erőforrások áthelyezése a célhelyre. 

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


Amikor a rendszer felkéri, hogy szeretné-e a megadott erőforrások áthelyezése, adja meg, ellenőrizze **Y** megerősítéséhez.

## <a name="next-steps"></a>További lépések
Számos különböző típusú erőforrások helyezheti át erőforráscsoportok és előfizetések között. További információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).    

