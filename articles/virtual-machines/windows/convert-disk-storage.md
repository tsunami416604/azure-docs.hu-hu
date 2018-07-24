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
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: bd6ab8dd91d7beb1b73461569bde893157c98d39
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213778"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Átalakítása az Azure disks storage felügyelt standardról prémium szintre, és fordítva

A felügyelt lemezek három tárolási lehetőséget kínál az: [prémium szintű SSD](../windows/premium-storage.md), Standard SSD(Preview) és [Standard HDD](../windows/standard-storage.md). Lehetővé teszi a teljesítmény igények alapján minimális állásidővel lehetőségek közötti egyszerű váltást. Nem felügyelt lemezek esetén ez nem támogatott. De könnyen [konvertálás felügyelt lemezekké](convert-unmanaged-to-managed-disks.md) egyszerűen a lemeztípusok közötti váltáshoz.

Ez a cikk bemutatja a felügyelt lemezek konvertálása a standard, prémium szintű, és fordítva az Azure PowerShell használatával. Ha szeretné telepíteni, vagy frissít a csomagon belül, lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Előkészületek

* Az átalakításhoz újra kell indítani a virtuális gép, ezért ütemezze a lemezek tároló áttelepítése egy már létező karbantartási időszakra. 
* Ha először használja a nem felügyelt lemezek [konvertálás felügyelt lemezekké](convert-unmanaged-to-managed-disks.md) Ez a cikk használatával válthat a tárolási lehetőségek között. 
* Ehhez a cikkhez az Azure PowerShell-modul verzióját 6.0.0 vagy újabb. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>A felügyelt lemezek, a virtuális gépek konvertálása standard prémium szintre, és ez fordítva is igaz

Az alábbi példa bemutatja, hogyan lehet váltani a prémium szintű Storage standard virtuális gép mindegyik lemeze. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a premium storage. Ebben a példában is, amely támogatja a prémium szintű storage vált.

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
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Standard szintű felügyelt lemez konvertálása prémium szintű, és ez fordítva is igaz

A fejlesztési és tesztelési számítási feladatok esetében érdemes a standard és prémium szintű lemezek a költségek csökkentése érdekében keverékét rendelkezik. Ez a premium storage, csak azok a lemezek jobb teljesítményt igénylő frissítésével végezheti el. Az alábbi példa bemutatja, hogyan lehet váltani a prémium szintű Storage, a standard szintű virtuális gépek egyetlen lemezre, és ez fordítva is igaz. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a premium storage. Ebben a példában is, amely támogatja a prémium szintű storage vált.

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

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Felügyelt lemez konvertálása a standard HDD standard SSD-re és fordítva

Az alábbi példa bemutatja, hogyan lehet váltani egy egyetlen lemezt egy virtuális gép a szabványos HDD standard SSD-re és fordítva.

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

$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>További lépések

Készítsen róla egy írásvédett másolatot a virtuális gépek használatával [pillanatképek](snapshot-copy-managed-disk.md).

