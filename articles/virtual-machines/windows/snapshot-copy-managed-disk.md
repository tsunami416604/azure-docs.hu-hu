---
title: "Pillanatkép készítése a virtuális merevlemez létrehozása az Azure-ban |} Microsoft Docs"
description: "Útmutató egy példányát kívánja használni, mint biztonsági másolatot, vagy a problémák elhárítása az Azure virtuális gép létrehozásához."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: dba70db512d88dfc57107bade0df50d1834eb883
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Pillanatkép készítése a biztonsági mentéshez vagy elhárítása a virtuális gép virtuális merevlemez állít egy operációs rendszer vagy az adatok lemezt. Pillanatkép virtuális merevlemez teljes, csak olvasható másolatát. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Készítsen pillanatképet az Azure portál használatával 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kezdési bal felső sarokban, kattintson a **új** keresse meg a **pillanatkép**.
3. A pillanatkép paneljén kattintson **létrehozása**.
4. Adjon meg egy **neve** a pillanatkép.
5. Válasszon ki egy létező [Erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#resource-groups), vagy adja meg egy új csoport nevét. 
6. Válasszon egy Azure-adatközpontban helyét.  
7. A **forráslemez**, válassza ki a felügyelt lemezt pillanatkép.
8. Válassza ki a **fiók típusa** használni a pillanatkép tárolására. Ajánlott **Standard_LRS** kivéve, ha esetleg szükség lenne rá egy nagy teljesítményű lemezen tárolja.
9. Kattintson a **Create** (Létrehozás) gombra.

## <a name="use-powershell-to-take-a-snapshot"></a>Pillanatkép készítése a PowerShell használatával
A következő lépések bemutatják, hogyan kérhet a VHD lemez másolni, hozzon létre a pillanatkép-konfigurációkat és pillanatképet készít, a lemez használatával a [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) parancsmag. 

Győződjön meg arról, hogy rendelkezik-e a legújabb verzióját a AzureRM.Compute PowerShell-modul telepítve. A következő parancsot a telepítéshez.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
További információkért lásd: [Azure PowerShell Versioning](/powershell/azure/overview).


1. Egyes paraméterek megadása 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. A VHD lemez másolandó beolvasása.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. A pillanatkép-konfigurációk létrehozása. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. A pillanatkép elkészítésére.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Ha azt tervezi, a pillanatkép segítségével kezelt lemez létrehozása, és csatlakoztassa a virtuális gépek magas végrehajtása szükséges, használja a paraméterrel `-AccountType Premium_LRS` a New-AzureRmSnapshot paranccsal. A paraméter a pillanatkép hoz, hogy prémium felügyelt lemezként tárolja. Prémium szintű kezelt lemezek drágább szabvány. Ezért mindenképpen valóban szükség van prémium, hogy a paraméter használata előtt.

## <a name="next-steps"></a>Következő lépések

Virtuális gép létrehozása egy pillanatképből létrehozásával felügyelt lemezes pillanatképet, és majd lemezcsatlakoztatás az új felügyelt az operációs rendszer lemezeként. További információkért lásd: a [hozzon létre egy virtuális Gépet egy pillanatképből](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) minta.
