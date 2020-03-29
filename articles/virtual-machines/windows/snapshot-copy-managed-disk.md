---
title: Hozzon létre pillanatképet egy virtuális merevlemezről az Azure-ban
description: Megtudhatja, hogyan hozhat létre egy másolatot egy Azure-beli virtuális gépről biztonsági másolatként vagy hibaelhárítási problémákhoz.
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
ms.openlocfilehash: bc74a3eea1f99de6080788d6f3fddcac823092dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370901"
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása

A pillanatkép egy virtuális merevlemez (VHD) teljes, csak olvasható másolata. Pillanatképet készíthet egy operációs rendszerről vagy adatlemez virtuális merevlemezéről biztonsági másolatként való használatához, vagy elháríthatja a virtuális gép (VM) problémáit.

Ha a pillanatkép használatával egy új virtuális gép létrehozásához, azt javasoljuk, hogy a pillanatkép készítése előtt állítsa le a virtuális gép, törölje ki a folyamatban lévő folyamatokat.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata 

Pillanatkép létrehozásához hajtsa végre az alábbi lépéseket: 
1.  Az [Azure Portalon](https://portal.azure.com)válassza az **Erőforrás létrehozása**lehetőséget.
2. Keresse meg a Keresést, és válassza **a Pillanatkép**lehetőséget.
3. A **Pillanatkép** ablakban válassza a **Létrehozás lehetőséget.** Megjelenik **a Pillanatkép létrehozása** ablak.
4. Adja meg a pillanatkép **nevét.**
5. Jelöljön ki egy meglévő [erőforráscsoportot,](../../azure-resource-manager/management/overview.md#resource-groups) vagy adja meg egy új nevét. 
6. Válassza ki egy Azure-adatközpont **helyét**.  
7. **Forráslemez**esetén jelölje ki a pillanatfelvételhez kívánt felügyelt lemezt.
8. Válassza ki a pillanatkép tárolásához használni kívánt **fióktípust.** Válassza **a Standard_HDD**lehetőséget, kivéve, ha a pillanatképet nagy teljesítményű lemezen szeretné tárolni.
9. Kattintson a **Létrehozás** gombra.

## <a name="use-powershell"></a>A PowerShell használata

A következő lépések bemutatják, hogyan másolhatja a VHD-lemezt, és hozzon létre a pillanatkép konfigurációját. Ezután pillanatképet készíthet a lemezről a [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) parancsmag használatával. 

 

1. Állítson be néhány paramétert: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. A virtuális gép beszerezése:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Hozza létre a pillanatkép konfigurációját. Ebben a példában a pillanatkép az operációs rendszer lemezéről készült:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Ha szeretné tárolni a pillanatképet a zóna-rugalmas tároló, hozza létre egy olyan `-SkuName Standard_ZRS` régióban, amely támogatja a [rendelkezésre állási zónákat,](../../availability-zones/az-overview.md) és tartalmazza a paramétert.   
   
4. A pillanatkép készítése:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>További lépések

Hozzon létre egy virtuális gépet egy pillanatképből egy felügyelt lemez pillanatképből történő létrehozásával, majd csatolja az új felügyelt lemezt operációs rendszerlemezként. További információkért tekintse meg a minta [létrehozása virtuális gép egy pillanatkép a PowerShell.](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)
