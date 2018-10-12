---
title: Átalakítása az Azure disks storage felügyelt standardról prémium szintre, és fordítva |} A Microsoft Docs
description: Azure konvertálása felügyelt lemezek a standard, prémium szintű, és fordítva, az Azure PowerShell-lel.
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.openlocfilehash: 958f661585b38b156cf523fe00986e7594474917
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093816"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>A tárolótípus felügyelt lemez frissítése

Az Azure Managed Disks három tárolási típus lehetőséget kínál: [prémium szintű SSD](../windows/premium-storage.md), [Standard SSD](../windows/disks-standard-ssd.md), és [Standard HDD](../windows/standard-storage.md). Felügyelt lemez közötti tárolási teljesítmény igényei szerinti minimális állásidővel válthat. A tárolási típusok közötti váltás nem támogatott; a nem felügyelt lemez azonban egyszerűen [egy nem felügyelt lemez konvertálása felügyelt lemez](convert-unmanaged-to-managed-disks.md).

Ez a cikk bemutatja a felügyelt lemez konvertálása a standard, prémium szintű, és fordítva, az Azure PowerShell használatával. Ha telepíteni vagy frissíteni a PowerShell, lásd: kell [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1).

## <a name="prerequisites"></a>Előfeltételek

* Az átalakításhoz újra kell indítani a virtuális gép (VM), mert a lemezek tároló áttelepítése egy már létező karbantartási időszakra ütemezze. 
* Ha egy nem felügyelt lemez használata először [alakíthatja át egy felügyelt lemezt](convert-unmanaged-to-managed-disks.md) lehetővé teszi a tárolási típusok közötti váltani. 
* Ebben a cikkben szereplő példák az Azure PowerShell-modul verzióját 6.0.0 megkövetelése vagy újabb. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Futtatás [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) kapcsolat létrehozása az Azure-ral.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>A felügyelt lemezek, a virtuális gépek premium standard átalakítása

Az alábbi példa bemutatja, hogyan lehet váltani a prémium szintű Storage standard virtuális gép mindegyik lemeze. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a premium storage. Ebben a példában mezőkben, amely támogatja a premium storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Standard szintű felügyelt lemez konvertálása prémium

A fejlesztési és tesztelési számítási feladatok esetében érdemes a standard és prémium szintű lemezek a költségek csökkentése érdekében keverékét. Igen, a frissítés a prémium szintű storage teendők csak ezeket a lemezeket igénylő végezhet a teljesítmény. Az alábbi példa bemutatja, hogyan lehet váltani a prémium szintű Storage, a standard szintű virtuális gépek egyetlen lemezre, és ez fordítva is igaz. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a premium storage. Ebben a példában is bemutatja, hogyan lehet váltani, amely támogatja a premium storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Felügyelt lemez konvertálása a standard HDD standard SSD-re

Az alábbi példa bemutatja, hogyan lehet váltani egy egyetlen lemezt egy virtuális gép a szabványos HDD standard SSD-re, és ez fordítva is igaz:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>További lépések

Győződjön meg arról, egy virtuális Gépet egy csak olvasható példányát használatával egy [pillanatkép](snapshot-copy-managed-disk.md).

