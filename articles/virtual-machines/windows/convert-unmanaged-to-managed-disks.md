---
title: "Alakítsa át a Windows rendszerű virtuális gép nem felügyelt lemezekből felügyelt - Azure felügyelt lemezek |} Microsoft Docs"
description: "A Windows virtuális gépek átalakítása nem felügyelt lemezekről történő által kezelt lemezeken a Resource Manager üzembe helyezési modellben a PowerShell használatával"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: 92168ba5605e119d42ba40ee694cebb3ad116041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Alakítsa át a Windows rendszerű virtuális gép nem felügyelt lemezekből felügyelt

Ha meglévő Windows virtuális gépek (VM), a nem felügyelt lemezeket használó, a virtuális gépek keresztül felügyelt lemezeket használni átválthat a [Azure felügyelt lemezek](managed-disks-overview.md) szolgáltatás. Ez a folyamat az operációsrendszer-lemez és a mellékelt adatok lemezzel alakítja át.

Ez a cikk bemutatja, hogyan alakítsa át a virtuális gépek Azure PowerShell használatával. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Előkészületek


* Felülvizsgálati [kezelt lemezek az áttelepítés megtervezése](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Felülvizsgálati [felügyelt lemezekre áttelepítésével kapcsolatos gyakori kérdések](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Egypéldányos virtuális gépek átalakítása
Ez a szakasz bemutatja, hogyan adhat alakítsa át a egypéldányos Azure virtuális gépek nem felügyelt lemezekből felügyelt. (Ha a virtuális gépek rendelkezésre állási beállítása, a következő szakaszban talál.) 

1. A virtuális gép felszabadítása használatával a [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) parancsmag. Az alábbi példa felszabadítja a nevű virtuális gép `myVM` az erőforráscsoport neve `myResourceGroup`: 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. A virtuális gép átalakítása felügyelt lemezek használatával a [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) parancsmag. A következő folyamat alakítja át a korábbi VM, beleértve az operációsrendszer-lemezképet és az adatok lemezzel, és elindítja a virtuális gépet:

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```



## <a name="convert-vms-in-an-availability-set"></a>Alakítsa át a virtuális gépek rendelkezésre állási csoportba

Ha az átalakítani kívánt virtuális gépek lemezek rendelkezésre állási csoportba, először alakítsa át a rendelkezésre állási csoportot egy felügyelt rendelkezésre állási csoportba.

1. A rendelkezésre állási csoportot használatával alakítsa át a [frissítés-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) parancsmag. Az alábbi példa frissíti a rendelkezésre állási csoportot elnevezett `myAvailabilitySet` az erőforráscsoport neve `myResourceGroup`:

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Ha helyezkedik el a régió, ahol a rendelkezésre állási beállítása csak 2 felügyelt tartalék tartományok rendelkezik, de a nem felügyelt tartalék tartományok száma: 3, ez a parancs megjeleníti hiba hasonló "a tartalék tartományok megadott számának 3 1 és 2 között kell lennie." A hiba megoldásához frissítse a tartalék tartomány 2 és a frissítés `Sku` való `Aligned` az alábbiak szerint:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Felszabadítani, és alakítsa át a virtuális gépek a rendelkezésre állási csoportot. Az alábbi parancsfájlt minden virtuális gép felszabadítja a használatával a [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) -parancsmag használatával konvertálja [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk), és automatikusan, egymástól, az átalakítási folyamat újraindul :

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem sikerül átalakítás során, vagy az előző konverzió problémák miatt sikertelen állapotban van a virtuális gépek, futtassa a `ConvertTo-AzureRmVMManagedDisk` újra a parancsmagot. Egy egyszerű újra általában feloldja a problémát.
Átalakítás, előtt győződjön meg arról, a Virtuálisgép-bővítmények az "A kiépítés sikeres" állapotban van, vagy az átalakítás 409 hibakóddal meghiúsul.


## <a name="next-steps"></a>További lépések

[Standard szintű felügyelt lemez konvertálása premium](convert-disk-storage.md)

Tegye meg a virtuális gépek csak olvasható másolatát a használatával [pillanatképek](snapshot-copy-managed-disk.md).

