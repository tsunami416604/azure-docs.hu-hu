---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: sdwheeler
ms.service: virtual-machines
ms.topic: include
ms.date: 04/18/2018
ms.author: kirpas;iainfou;sewhee
ms.custom: include file
ms.openlocfilehash: ab4d5326fc06cc8676ea93ed529315cc47651e26
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943453"
---
## <a name="overview"></a>Áttekintés
Amikor hoz létre egy új virtuális gép (VM) erőforráscsoportban lemezképek [Azure piactér](https://azure.microsoft.com/marketplace/), az alapértelmezett operációsrendszer-meghajtón gyakran 127 GB (néhány képet, operációsrendszer-lemez kisebb méretű alapértelmezés szerint rendelkezik). Bár hozzáadhat adatlemezeket a virtuális géphez (ezeknek száma a választott termékváltozattól függ), és ajánlott ezekre a kiegészítő lemezekre telepíteni az alkalmazásokat és a processzorigényes számítási feladatokat, az ügyfeleknek gyakran bővíteniük kell az operációs rendszer meghajtóját bizonyos forgatókönyvek támogatásához, mint például a következők:

1. Az operációs rendszer meghajtójára összetevőket telepítő, régebbi alkalmazások támogatása.
2. Nagyobb operációsrendszer-meghajtóval rendelkező fizikai számítógép vagy virtuális gép migrálása a helyszínről.

> [!IMPORTANT]
> Az Azure két különböző üzemi modellel rendelkezik az erőforrások létrehozásához és használatához: Resource Manager-alapú és klasszikus. Ez a cikk a Resource Manager-alapú modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> 
> 
> [!WARNING]
> Az Azure virtuális gép operációsrendszer-lemez átméretezése miatt úgy, hogy indítsa újra.
>

## <a name="resize-the-os-drive"></a>Az operációs rendszer meghajtójának átméretezése
Ez a cikk az operációs rendszer meghajtójának az [Azure Powershell](/powershell/azureps-cmdlets-docs) erőforrás-kezelő moduljaival történő átméretezését ismerteti. Bemutatjuk az operációs rendszer meghajtóját mind a nem kezelt, mind a kezelt átméretezése, mert mindkét lemeztípusok eltérnek a lemezek átméretezése megközelítése.

### <a name="for-resizing-unmanaged-disks"></a>Nem felügyelt lemezek átméretezéséhez:

Nyissa meg a Powershell ISE-t vagy PowerShell-ablakot rendszergazdai módban, és kövesse az alábbi lépéseket:

1. Jelentkezzen be a Microsoft Azure-fiókjához erőforrás módban, és jelölje ki az előfizetését a következőképpen:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Állítsa az erőforráscsoport és a virtuális gép nevét a következőre:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Szerezzen be egy hivatkozást a virtuális gépre a következő módon:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. A lemez átméretezése előtt állítsa le a virtuális gépet a következő módon:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Íme a pillanat, amire vártunk! A nem felügyelt operációsrendszer-lemez méretének beállítása a kívánt értéket, és frissítse a virtuális Gépet az alábbiak szerint:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. A megengedett maximális: 2048 GB operációsrendszer-lemezek. (Bontsa ki a Virtuálismerevlemez-blobot, ez a méret túl lehet, de az operációs rendszer csak az első 2048 GB lemezterületet dolgozhasson.)
   > 
   > 
6. A virtuális gép frissítése eltarthat néhány másodpercig. Miután a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet a következő módon:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>Felügyelt lemezek átméretezéséhez:

Nyissa meg a Powershell ISE-t vagy PowerShell-ablakot rendszergazdai módban, és kövesse az alábbi lépéseket:

1. Jelentkezzen be a Microsoft Azure-fiókjához erőforrás módban, és jelölje ki az előfizetését a következőképpen:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Állítsa az erőforráscsoport és a virtuális gép nevét a következőre:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Szerezzen be egy hivatkozást a virtuális gépre a következő módon:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. A lemez átméretezése előtt állítsa le a virtuális gépet a következő módon:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Szerezzen be egy hivatkozást a felügyelt operációsrendszer-lemezt. A felügyelt operációsrendszer-lemez méretének beállítása a kívánt értéket, és a lemez frissítése az alábbiak szerint:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. A megengedett maximális: 2048 GB operációsrendszer-lemezek. (Bontsa ki a Virtuálismerevlemez-blobot, ez a méret túl lehet, de az operációs rendszer csak az első 2048 GB lemezterületet dolgozhasson.)
   > 
   > 
6. A virtuális gép frissítése eltarthat néhány másodpercig. Miután a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet a következő módon:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

Készen is van. Csatlakozzon RDP-kapcsolaton keresztül a virtuális géphez, nyissa meg a Számítógép-kezelés (vagy Lemezkezelés) elemet, és bővítse ki a meghajtót az újonnan kiosztott tárhellyel.

## <a name="summary"></a>Összegzés
Ebben a cikkben a Powershell Azure Resource Manager-moduljaival bővítettük egy IaaS-beli virtuális gép operációsrendszer-meghajtóját. Mind a nem kezelt, mind a kezelt referenciaként a teljes parancsfájl másolható alatt van:

Nem felügyelt lemezek:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
Managed Disks:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="for-resizing-data-disks"></a>Az adatlemezek átméretezéséhez
Bár ebben a cikkben azt összpontosított elsősorban a nem kezelt/felügyelt operációsrendszer-lemez a virtuális gép kiterjesztését, a fejlett parancsfájl is felhasználhatók a bővített a adatlemezt csatolni a virtuális géphez. A virtuális géphez csatolt első adatlemez bővítéséhez például cserélje ki a ```StorageProfile``` ```OSDisk``` objektumát a ```DataDisks``` tömbre, és egy numerikus indexszel szerezzen be az első csatolt adatlemezre mutató hivatkozást az alább látható módon:

Nem felügyelt lemezt:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Felügyelt lemezes:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

Hasonlóképpen hivatkozhat más, a virtuális géphez csatolt adatlemezekre is, akár a fent látható módon egy index használatával, akár a lemez ```Name``` tulajdonságával, az alább látható módon:

Nem felügyelt lemezt:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Felügyelt lemezes:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Ha arról szeretne tájékozódni, hogyan csatlakoztathat lemezeket egy Azure Resource Manager-alapú virtuális géphez, tekintse meg ezt a [cikket](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

