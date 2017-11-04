---
title: "Windowsos VM erőforrás áthelyezése az Azure-ban |} Microsoft Docs"
description: "A Windows virtuális gépek áthelyezése egy másik Azure-előfizetés vagy az erőforrás-csoportok a Resource Manager üzembe helyezési modellben."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 1db25a5d9ff5cb6aa2787a0cafa40cfb010e3b06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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
A virtuális gép áthelyezése egy másik erőforráscsoportban, győződjön meg arról, hogy Ön is a függő erőforrásokat áthelyezni kell. A Move-AzureRMResource parancsmag használatához szüksége van az erőforrás nevének és az erőforrás típusát. A keresés-AzureRMResource parancsmag is kaphat.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"


Helyezze át a virtuális Gépet kell a több erőforrások áthelyezése. Jelenleg csak az egyes erőforrások külön változók létrehozása, és rendezze őket. Ebben a példában az alapvető erőforrások többségét tartalmazza a virtuális gépek, de igény szerint több is hozzáadhat.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"

    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"

    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Az erőforrások áthelyezése másik előfizetést, tartalmazza a **- DestinationSubscriptionId** paraméter. 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Győződjön meg arról, hogy szeretné-e a megadott erőforrások áthelyezése fog kérni. Típus **Y** annak ellenőrzéséhez, hogy szeretné-e az erőforrások áthelyezése.

## <a name="next-steps"></a>Következő lépések
Számos különböző típusú erőforrások áthelyezheti erőforráscsoport-sablonok és előfizetések között. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).    

