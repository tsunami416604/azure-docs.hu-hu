---
title: Windows virtuális gép konvertálása nem felügyelt lemezekből felügyelt lemezekké
description: Windows virtuális gép konvertálása nem felügyelt lemezekről felügyelt lemezekké a PowerShell használatával az Erőforrás-kezelő telepítési modelljében
author: roygara
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: d8069b174b7a69cc2e6c47171159569c56a15563
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081948"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Windows virtuális gép konvertálása nem felügyelt lemezekből felügyelt lemezekké

Ha meglévő, nem felügyelt lemezeket használó Windows virtuális gépekkel (VM-ekkel) rendelkezik, konvertálhatja a virtuális gépeket felügyelt lemezek használatára az [Azure Managed Disks](managed-disks-overview.md) szolgáltatáson keresztül. Ez a folyamat átalakítja az operációs rendszer lemezét és a csatlakoztatott adatlemezeket is.

 

## <a name="before-you-begin"></a>Előkészületek


* Tekintse át [a Felügyelt lemezekre való áttelepítés tervét.](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)

* Tekintse át [a felügyelt lemezekre való áttelepítéssel kapcsolatos gyakori kérdéseket.](faq-for-disks.md#migrate-to-managed-disks)

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* A virtuális gép által az átalakítás előtt használt eredeti virtuális merevlemezeket és a tárfiókot nem törli a rendszer. A futtatásuk továbbra is költségekkel jár. Annak érdekében, hogy ezekért az összetevőkért a továbbiakban ne fizessen, törölje az eredeti virtuálismerevlemez-blobokat, miután meggyőződött arról, hogy az átalakítás befejeződött. Ha ezeknek a nem csatlakoztatott lemezeknek a törléséhez meg kell találnia ezeket a lemezeket, olvassa el a Nem csatolt Azure által felügyelt és nem felügyelt lemezek keresése és törlése című [cikkünket.](find-unattached-disks.md)


## <a name="convert-single-instance-vms"></a>Egypéldányos virtuális gépek konvertálása
Ez a szakasz bemutatja, hogyan konvertálhatja az egypéldányos Azure-virtuális gépeket nem felügyelt lemezekről felügyelt lemezekre. (Ha a virtuális gépek egy rendelkezésre állási csoportban vannak, tekintse meg a következő szakaszt.) 

1. A virtuális gép felszabadítása a [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) parancsmag használatával. A következő példa felszabadítja `myVM` a megnevezett `myResourceGroup`erőforráscsoportban megnevezett virtuális gép felszabadítását: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Konvertálja a virtuális gép felügyelt lemezek et a [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) parancsmag használatával. A következő folyamat átalakítja az előző virtuális gépet, beleértve az operációs rendszer lemezét és az adatlemezeket, és elindítja a virtuális gépet:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Virtuális gépek konvertálása rendelkezésre állási csoportban

Ha a felügyelt lemezekre konvertálni kívánt virtuális gépek rendelkezésre állási csoportban vannak, először a rendelkezésre állási készletet felügyelt rendelkezésre állási csoportná kell konvertálnia.

1. Konvertálja a rendelkezésre állási készletet az [Update-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) parancsmag használatával. A következő példa frissíti `myAvailabilitySet` a nevesített `myResourceGroup`erőforráscsoportban megnevezett elérhetőségi csoportot:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Ha a régió, ahol a rendelkezésre állási csoport található, csak 2 felügyelt tartalék tartományban, de a nem felügyelt tartalék tartományok száma 3, ez a parancs azt mutatja, hasonló a hiba: "A megadott tartalék tartomány száma 3 kell esnie a tartomány 1-2." A hiba elhárításához frissítse a tartalék `Sku` tartományt 2-re, és frissítse a `Aligned` következőkre:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. A rendelkezésre állási csoportban lévő virtuális gépek felszabadítása és átalakítása. A következő parancsfájl felszabadítja az egyes virtuális gépeket a [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) parancsmag használatával, konvertálja azt a [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)segítségével, és automatikusan újraindítja az átalakítási folyamat részeként:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Hibaelhárítás

Ha hiba történik az átalakítás során, vagy ha egy virtuális gép egy korábbi átalakítás `ConvertTo-AzVMManagedDisk` problémái miatt meghibásodott állapotban van, futtassa újra a parancsmast. Egy egyszerű újrapróbálkozás általában feloldja a helyzetet.
Konvertálás előtt győződjön meg arról, hogy az összes virtuálisgép-bővítmények a "Kiépítése sikeres" állapotban van, vagy az átalakítás sikertelen lesz a 409-es hibakóddal.

## <a name="convert-using-the-azure-portal"></a>Konvertálás az Azure Portal használatával

A nem felügyelt lemezeket felügyelt lemezekké is konvertálhatja az Azure Portalhasználatával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a virtuális gépet a portálon lévő virtuális gépek listájából.
3. A panela virtuális gép, válassza **a lemezek** a menüből.
4. A **Lemezek** panel tetején válassza az **Áttelepítés felügyelt lemezekre**lehetőséget.
5. Ha a virtuális gép egy rendelkezésre állási csoportban van, figyelmeztetést kap a **felügyelt lemezek áttelepítése** panelen, amely először a rendelkezésre állási készlet átalakításához szükséges. A figyelmeztetésnek rendelkeznie kell egy hivatkozással, amelyre kattintva konvertálhatja az elérhetőségi készletet. Miután a rendelkezésre állási csoport konvertált, vagy ha a virtuális gép nem egy rendelkezésre állási csoportban, kattintson az **Áttelepítés** gombra a lemezek felügyelt lemezekre való áttelepítése folyamatának elindításához.

A virtuális gép leáll, és az áttelepítés befejezése után újraindul.

## <a name="next-steps"></a>További lépések

[Szabványos felügyelt lemezek átalakítása prémium verzióra](convert-disk-storage.md)

A virtuális gépek írásvédett példányát [pillanatképek](snapshot-copy-managed-disk.md)használatával készítheti el.

