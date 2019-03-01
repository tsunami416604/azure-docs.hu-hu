---
title: Átalakítása az Azure disks storage felügyelt standardról prémium szintre, és fordítva |} A Microsoft Docs
description: Azure konvertálása felügyelt lemezek a standard, prémium szintű, és fordítva, az Azure PowerShell-lel.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 8f04f566d6d403d15a08a8900223061cdff1938c
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008661"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>A tárolótípus felügyelt lemez frissítése

Azure által felügyelt lemezek ajánlatok négy tárolási típus beállításai: Ultranagy tartós állapotú meghajtókkal (SSD), a prémium szintű SSD, a Standard SSD és a szabványos merevlemez-meghajtókon (HDD). Felügyelt lemez közötti tárolási teljesítmény igényei szerinti minimális állásidővel válthat. A tárolási típusok közötti váltás nem támogatott; a nem felügyelt lemez azonban egyszerűen [egy nem felügyelt lemez konvertálása felügyelt lemez](convert-unmanaged-to-managed-disks.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az átalakításhoz újra kell indítani a virtuális gép (VM), mert a lemezek tároló áttelepítése egy már létező karbantartási időszakra ütemezze.
* Ha egy nem felügyelt lemez használata először [alakíthatja át egy felügyelt lemezt](convert-unmanaged-to-managed-disks.md) lehetővé teszi a tárolási típusok közötti váltani.

## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>A felügyelt lemezek, a virtuális gépek premium standard átalakítása

Az alábbi példa bemutatja, hogyan lehet váltani a prémium szintű Storage standard virtuális gép mindegyik lemeze. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a premium storage. Ebben a példában mezőkben, amely támogatja a premium storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Standard szintű felügyelt lemez konvertálása prémium

A fejlesztési és tesztelési számítási feladatok esetében érdemes a standard és prémium szintű lemezek a költségek csökkentése érdekében keverékét. Ehhez frissítse a premium storage csak ezeket a lemezeket, amelyek jobb teljesítményt igényelnek. Az alábbi példa bemutatja, hogyan lehet váltani a prémium szintű Storage, a standard szintű virtuális gépek egyetlen lemezre, és ez fordítva is igaz. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a premium storage. Ebben a példában is bemutatja, hogyan lehet váltani, amely támogatja a premium storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-azure-portal"></a>Standard szintű felügyelt lemezek konvertálása prémium szintű Azure-portálon

Átválthat egy felügyelt lemezt standardról prémium szintre az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a virtuális Gépet a listából **virtuális gépek** a portálon.
3. Ha a virtuális gép nem áll le, kattintson a **leállítása** felső részén a virtuális gépek áttekintése panelre, és várja meg a virtuális gép leállításához.
3. Válassza ki a virtuális gép paneljén **lemezek** a menüből.
4. Válassza ki az átalakítani kívánt lemez.
5. Válassza ki **konfigurációs** a menüből.
6. Módosítsa a **fiók típusa** a **Standard HDD** való **prémium szintű SSD**.
7. Kattintson a **mentése** és a lemez panel bezárásához.

A lemez típusát a frissítés azonnali. Az átalakítás után újraindíthatja a virtuális gép.

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Felügyelt lemez konvertálása a standard HDD standard SSD-re

Az alábbi példa bemutatja, hogyan lehet váltani egy egyetlen lemezt egy virtuális gép a szabványos HDD standard SSD-re, és ez fordítva is igaz:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>További lépések

Győződjön meg arról, egy virtuális Gépet egy csak olvasható példányát használatával egy [pillanatkép](snapshot-copy-managed-disk.md).

