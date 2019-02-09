---
title: Hozzon létre egy pillanatképet egy VHD-t az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy másolatot egy Azure virtuális gépek biztonsági mentése vagy kapcsolatos hibaelhárítás során használja.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 1015e6774dac1258820e3ca4b3d06786046a8554
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980856"
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Pillanatkép a virtuális merevlemez (VHD) egy teljes körű, csak olvasható példányát. A rendszer- és lemez VHD-t használja, mint a biztonsági mentés, vagy a virtuális gép (VM) hibák elhárításához pillanatképet is igénybe vehet.

Ha azt tervezi, a pillanatkép használatával hozzon létre egy új virtuális Gépet, azt javasoljuk, hogy Ön bezárva, a virtuális gép pillanatkép, tisztítsa meg a folyamatban lévő folyamatok való kiléptetése előtt.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **erőforrás létrehozása**, majd keresse meg és válassza a **pillanatkép**.
3. Az a **pillanatkép** ablakban válassza **létrehozás**. A **létrehozás pillanatkép** ablak jelenik meg.
4. Adjon meg egy **neve** elkészíteni a pillanatképet.
5. Válasszon egy meglévő [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#resource-groups) vagy adjon meg egy új tároló nevét. 
6. Válassza ki egy Azure-adatközpont **helyét**.  
7. A **forráslemez**, válassza ki a felügyelt lemez, pillanatkép.
8. Válassza ki a **fiók típusa** tárolja a pillanatkép. Válassza ki **Standard_HDD**, ha nincs szükség a pillanatképet egy nagy teljesítményű lemezen tárolja.
9. Kattintson a **Létrehozás** gombra.

## <a name="use-powershell"></a>A PowerShell használata

A következő lépések bemutatják, hogyan másolja a VHD-lemez, a pillanatkép-konfiguráció létrehozása és a lemez pillanatfelvételének a [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) parancsmagot. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

1. Egyes paraméterek beállítása: 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

2. A virtuális gép lekérése:

 ```azurepowershell-interactive
$vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
```

3. Hozza létre a pillanatkép-konfigurációt. Ebben a példában a pillanatképet, az operációsrendszer-lemez van:

 ```azurepowershell-interactive
$snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
```
   
   > [!NOTE]
   > Ha szeretné tárolni a pillanatkép zóna rugalmas tárolás, hozza létre, amely támogatja az egy régióban [rendelkezésre állási zónák](../../availability-zones/az-overview.md) , és tartalmazzák a `-SkuName Standard_ZRS` paraméter.   
   
4. A pillanatkép:

 ```azurepowershell-interactive
New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
```


## <a name="next-steps"></a>További lépések

Virtuális gép létrehozása felügyelt lemez létrehozása pillanatképből, és ezután a az operációsrendszer-lemezként az új felügyelt lemez csatolása egy pillanatképből. További információkért tekintse meg a mintát, a [virtuális gép létrehozása pillanatképből a PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
