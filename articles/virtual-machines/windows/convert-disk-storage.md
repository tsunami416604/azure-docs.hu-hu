---
title: "Alakítsa át az Azure szabványos lemezek tárolási kezelik, prémium, és ez fordítva is igaz |} Microsoft Docs"
description: "Azure konvertálása által kezelt lemezeken standard, premium, és fordítva, Azure PowerShell használatával."
services: virtual-machines-windows
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 407cfe7d9eee4e226938f383c04bb359a17290fc
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Alakítsa át az Azure szabványos lemezek tárolási kezelik, prémium, és ez fordítva is igaz

Kezelt lemezek két tárolási lehetőségeket kínál: [prémium](premium-storage.md) (SSD-alapú) és [szabványos](standard-storage.md) (HDD-alapú). Ez lehetővé teszi, hogy egyszerűen válthat a két lehetőség közül választhat a teljesítmény igények alapján minimális állásidővel között. Ez a funkció nem felügyelt lemezek nem érhető el. Azonban úgy is könnyen [alakítsa át a felügyelt](convert-unmanaged-to-managed-disks.md) egyszerűen a két beállítás közötti váltáshoz.

Ez a cikk bemutatja, hogyan felügyelt lemezek konvertálása standard, premium, és ez fordítva is igaz Azure PowerShell használatával. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Előkészületek

* Az átalakítás újra kell indítani a virtuális gép, úgy ütemezni a áttelepítését a lemezek már meglévő karbantartási időszak alatt történjen. 
* Használatakor a nem felügyelt lemezek, először [alakítsa át a felügyelt](convert-unmanaged-to-managed-disks.md) használatára Ez a cikk a két tárolási lehetőségek közötti váltáshoz. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Alakítsa át a virtuális gépek összes felügyelt lemezeket szabványos prémium szintű, és ez fordítva is igaz

A következő példában megmutatjuk, hogyan lehet váltani a prémium szintű Storage-standard a virtuális gép összes lemeze. Felügyelt premium lemezek, a virtuális Gépet kell használnia egy [Virtuálisgép-méretet](sizes.md) , amely támogatja a prémium szintű storage. Ebben a példában is, amely támogatja a prémium szintű storage méretre vált.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'

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
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Standard felügyelt lemezes átalakítása prémium, és ez fordítva is igaz

A fejlesztési és tesztelési célú munkaterheléshez érdemes lehet a költségek csökkentése a standard és premium lemezek keverékével van. Prémium szintű Storage, frissítse a lemezek csak az jobb teljesítményt igénylő végrehajtható. A következő példában megmutatjuk, hogyan lehet váltani egyetlen lemezt a virtuális gépek a szabványos prémium szintű Storage, és ez fordítva is igaz. Felügyelt premium lemezek, a virtuális Gépet kell használnia egy [Virtuálisgép-méretet](sizes.md) , amely támogatja a prémium szintű storage. Ebben a példában is, amely támogatja a prémium szintű storage méretre vált.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get the ARM resource to get name and resource group of the VM
$vmResource = Get-AzureRmResource -ResourceId $disk.diskId

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

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

## <a name="next-steps"></a>További lépések

Tegye meg a virtuális gépek csak olvasható másolatát a használatával [pillanatképek](snapshot-copy-managed-disk.md).

