---
title: Windows virtuális gép átalakítása nem felügyeltről felügyelt managed disksbe – az Azure Managed Disks |} A Microsoft Docs
description: Windows virtuális gép átalakítása nem felügyeltről felügyelt a felügyelt lemezek a Resource Manager-alapú üzemi modellben a PowerShell használatával
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: 26ddc1df8353a8a8b0e0c54a0c906c8d8b6b45b9
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495341"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Windows virtuális gép átalakítása nem felügyeltről felügyelt a managed Disks szolgáltatásba

Ha rendelkezik meglévő Windows virtuális gépek (VM), amely a nem felügyelt lemezeket használ, a virtuális gépek keresztül felügyelt lemezek használata alakíthatja a [Azure Managed Disks](managed-disks-overview.md) szolgáltatás. Ez a folyamat az operációsrendszer-lemez és a bármely más csatolt lemez alakítja.

Ez a cikk bemutatja, hogyan átalakítása a virtuális gépek Azure PowerShell használatával. Ha szeretné telepíteni, vagy frissít a csomagon belül, lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Előkészületek


* Felülvizsgálat [tervezze meg a migrálás a Managed Disks szolgáltatásba](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Felülvizsgálat [a migrálás a Managed Disks – gyakori kérdések](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Egypéldányos virtuális gépek konvertálása
Ez a szakasz bemutatja, hogyan átalakítása nem felügyeltről felügyelt egypéldányos Azure virtuális gépek felügyelt lemezeket. (Ha a virtuális gépek rendelkezésre állási csoportban, lásd a következő szakaszban.) 

1. Szabadítsa fel a virtuális gép használatával a [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) parancsmagot. Az alábbi példa felszabadítja a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`: 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. A virtuális gép konvertálása a felügyelt lemezek használatával a [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) parancsmagot. A következő folyamat alakítja át a korábbi VM, beleértve az operációsrendszer-lemez és bármely adatlemez, és elindítja a virtuális gépet:

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```



## <a name="convert-vms-in-an-availability-set"></a>Konvertálja a virtuális gépeket egy rendelkezésre állási csoportban

Ha az átalakítani kívánt virtuális gépek felügyelt lemezeket egy rendelkezésre állási csoportban, először konvertálnia kell a rendelkezésre állási felügyelt rendelkezésre állási csoportban.

1. A rendelkezésre állási csoportot az átalakítás a [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) parancsmagot. Az alábbi példa frissíti a rendelkezésre állási csoportot elnevezett `myAvailabilitySet` az erőforráscsoport neve `myResourceGroup`:

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Ha a régió, ahol a rendelkezésre állási található csak 2 felügyelt hibatűrési tartományt tartalmaz, de nem felügyelt tartalék tartományok száma: 3, ez a parancs hasonló jelenít meg hiba "a megadott tartalék tartományok száma 3 1-2 tartományban kell lennie." A hiba elhárításához frissítse a tartalék tartomány 2 és a frissítés `Sku` való `Aligned` módon:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Szabadítsa fel, és konvertálja a virtuális gépek rendelkezésre állási csoportban. Az alábbi parancsfájlt minden virtuális gép felszabadítja a használatával a [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) parancsmag használatával konvertálja [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk), és automatikusan is vannak, az átalakítási folyamat újraindítása :

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

Ha a konvertálás során hiba történik, vagy ha a virtuális gép egy korábbi konverziós a problémák miatt a hibás állapotban van, futtassa a `ConvertTo-AzureRmVMManagedDisk` újra a parancsmagot. Egy egyszerű újra általában feloldja a helyzet.
Átalakítás, előtt ellenőrizze a Virtuálisgép-bővítmények "A kiépítés sikeres" állapotban van, vagy az átalakítás sikertelen lesz a következő hibakóddal: 409.


## <a name="convert-using-the-azure-portal"></a>Konvertálja az Azure portal használatával

Nem felügyelt lemezek is konvertálása felügyelt lemezeket az Azure portal használatával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A portál virtuális gépek listájából válassza ki a virtuális gép.
3. Válassza ki a virtuális gép paneljén **lemezek** a menüből.
4. Felső részén a **lemezek** panelen válassza ki **áttelepítése a managed Disks szolgáltatásba**.
5. Ha a virtuális gép egy rendelkezésre állási csoportban van, lesz egy figyelmeztetés a a **áttelepítése a managed Disks szolgáltatásba** panel, amelyen a rendelkezésre állási csoportot, először konvertálnia kell. A figyelmeztetés rendelkeznie kell egy hivatkozást is kattinthat a rendelkezésre állási csoport konvertálásához. Miután dátumformátumra alakítja át a rendelkezésre állási csoport, vagy ha a virtuális gép nem egy rendelkezésre állási csoportban, kattintson a **áttelepítése** a lemezek a managed Disks szolgáltatásba való migrálásának a folyamat elindításához. 

A virtuális gép leáll és újraindul a migrálás befejezése után.

## <a name="next-steps"></a>További lépések

[A standard szintű felügyelt lemezek konvertálása prémium](convert-disk-storage.md)

Készítsen róla egy írásvédett másolatot a virtuális gépek használatával [pillanatképek](snapshot-copy-managed-disk.md).

