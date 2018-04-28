---
title: Pillanatkép készítése a virtuális merevlemez létrehozása az Azure-ban |} Microsoft Docs
description: Útmutató egy példányát kívánja használni, mint biztonsági másolatot, vagy a problémák elhárítása az Azure virtuális gép létrehozásához.
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: d7315d3fb7fc156beb85271d0e5aa19ec6baa7a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Pillanatkép készítése a biztonsági mentéshez vagy elhárítása a virtuális gép virtuális merevlemez állít egy operációs rendszer vagy az adatok lemezt. Pillanatkép virtuális merevlemez teljes, csak olvasható másolatát. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Készítsen pillanatképet az Azure portál használatával 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kezdési bal felső sarokban, kattintson a **hozzon létre egy erőforrást** keresse meg a **pillanatkép**.
3. A pillanatkép paneljén kattintson **létrehozása**.
4. Adjon meg egy **neve** a pillanatkép.
5. Válasszon ki egy létező [Erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#resource-groups), vagy adja meg egy új csoport nevét. 
6. Válasszon egy Azure-adatközpontban helyét.  
7. A **forráslemez**, válassza ki a felügyelt lemezt pillanatkép.
8. Válassza ki a **fiók típusa** használni a pillanatkép tárolására. Ajánlott **Standard_LRS** kivéve, ha esetleg szükség lenne rá egy nagy teljesítményű lemezen tárolja.
9. Kattintson a **Create** (Létrehozás) gombra.

## <a name="use-powershell-to-take-a-snapshot"></a>Pillanatkép készítése a PowerShell használatával

A következő lépések bemutatják, hogyan kérhet a VHD lemez másolni, hozzon létre a pillanatkép-konfigurációkat és pillanatképet készít, a lemez használatával a [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) parancsmag. 

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik-e a legújabb verzióját a AzureRM.Compute PowerShell-modult. Ez a cikk a AzureRM 5.7.0 verziója szükséges, vagy később. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Egyes paraméterek megadása 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

A virtuális gép beolvasása.

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

A pillanatkép-konfiguráció létrehozása. Ebben a példában fogjuk pillanatkép az operációsrendszer-lemezképet.

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> Ha azt szeretné, a pillanatkép zóna rugalmas tárolás tárolni, olyan régióhoz, amely támogatja a létrehozásához szükséges [rendelkezésre állási zónák](../../availability-zones/az-overview.md) , és tartalmazzák a `-SkuName Standard_ZRS` paraméter.   

   
A pillanatkép elkészítésére.

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>További lépések

Virtuális gép létrehozása egy pillanatképből létrehozásával felügyelt lemezes pillanatképet, és majd lemezcsatlakoztatás az új felügyelt az operációs rendszer lemezeként. További információkért lásd: a [hozzon létre egy virtuális Gépet egy pillanatképből](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) minta.
