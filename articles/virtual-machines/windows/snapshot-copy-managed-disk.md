---
title: Virtuális merevlemez pillanatképének létrehozása az Azure-ban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre másolatot egy Azure-beli virtuális gép biztonsági mentésre vagy hibaelhárítási problémákra való használatra.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 76110fa04441540875e65c8bc056fc21555c5db6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102283"
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása

A pillanatkép egy virtuális merevlemez (VHD) teljes, csak olvasható másolata. Készíthet pillanatképet egy operációsrendszer-vagy adatlemez virtuális merevlemezről, amely biztonsági mentésre használható, illetve a virtuális gép (VM) problémáinak elhárításához.

Ha az új virtuális gép létrehozásához a pillanatképet fogja használni, javasoljuk, hogy a pillanatkép elkészítése előtt törölje a virtuális gépet, és távolítsa el a folyamatban lévő folyamatokat.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza az **erőforrás létrehozása**lehetőséget, majd keresse meg és válassza kia pillanatképet.
3. A **Pillanatkép** ablakban válassza a **Létrehozás**elemet. Megjelenik a **pillanatkép létrehozása** ablak.
4. Adja meg a pillanatkép **nevét** .
5. Válasszon ki egy meglévő [erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#resource-groups) , vagy adja meg egy új nevet. 
6. Válassza ki egy Azure-adatközpont **helyét**.  
7. A **forrásoldali lemez**lapon válassza ki a felügyelt lemezt a pillanatképhez.
8. Válassza ki a pillanatkép tárolására használni kívánt **fiókot** . Válassza a **Standard_HDD**lehetőséget, ha a pillanatképet nagy teljesítményű lemezen kell tárolni.
9. Kattintson a **Létrehozás** gombra.

## <a name="use-powershell"></a>A PowerShell használata

A következő lépések bemutatják, hogyan másolhatók a VHD-lemezek, hogyan hozhatók létre a pillanatkép-konfiguráció, és hogy a [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) parancsmaggal készítsen pillanatképet a lemezről. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

1. Néhány paraméter beállítása: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. A virtuális gép beszerzése:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Hozza létre a pillanatkép-konfigurációt. Ebben a példában a pillanatkép az operációsrendszer-lemez:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Ha a pillanatképet a zóna rugalmas tárolásban szeretné tárolni, akkor azt egy olyan régióban hozza létre, amely támogatja a [rendelkezésre állási zónákat](../../availability-zones/az-overview.md) , és tartalmazza a `-SkuName Standard_ZRS` paramétert.   
   
4. Pillanatkép készítése:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>További lépések

Hozzon létre egy virtuális gépet egy pillanatképből egy felügyelt lemez pillanatképből történő létrehozásával, majd az új felügyelt lemez csatlakoztatásával operációsrendszer-lemezként. További információkért lásd a [virtuális gép létrehozása pillanatképből a PowerShell használatával](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)című részt.
