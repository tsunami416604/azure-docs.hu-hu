---
title: Azure konvertálása felügyelt lemezes tárhely standardról prémium vagy standard |} A Microsoft Docs
description: Azure konvertálása felügyelt lemezek standardról prémium vagy standard Azure PowerShell használatával.
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
ms.openlocfilehash: f97140ffeed9115a0308215ea082baee611501fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009888"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>A tárolótípus felügyelt lemez frissítése

Az Azure négy lehetőség van a felügyelt lemezek: Ultranagy az Azure Disk Storage, a prémium szintű SSD, a Standard SSD és a Standard HDD. A minimális állásidővel teljesítmény igények alapján tárolási típusok között válthat. Ez a funkció nem támogatott a nem felügyelt lemezek esetén. De könnyen [egy nem felügyelt lemez konvertálása felügyelt lemez](convert-unmanaged-to-managed-disks.md) tudni váltani a lemeztípusok.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Előfeltételek

* Átalakítás újra kell indítani a virtuális gép (VM), mert a lemezes tárolás áttelepítése során egy már létező karbantartási időszakra ütemezze.
* Ha a lemez nem felügyelt, először [alakíthatja át egy felügyelt lemezt](convert-unmanaged-to-managed-disks.md) úgy válthat a tárolási lehetőségek között.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>A virtuális gépek között a prémium és standard szintű minden felügyelt lemezek

Ez a példa bemutatja, hogyan alakítják át az összes olyan Virtuálisgép-lemezek a Standard, prémium szintű Storage, illetve prémiumról standard szintű tárolóba. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a Premium storage. Ebben a példában mezőkben, amely támogatja a premium storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Váltson a Standard és prémium szintű között az egyes felügyelt lemezek

A fejlesztési és tesztelési számítási feladatok esetében érdemes lehet többféle, Standard és prémium szintű lemezek a költségek csökkentése érdekében. Ha szeretné, csak ezeket a lemezeket, amelyeket jobb teljesítményt kell frissíteni. Ez a példa bemutatja, hogyan egyetlen Virtuálisgép-lemez konvertálása a Standard, prémium szintű Storage, illetve prémiumról standard szintű tárolóba. Prémium szintű felügyelt lemezek használata, a virtuális Gépet kell használnia egy [Virtuálisgép-méret](sizes.md) , amely támogatja a Premium storage. Ebben a példában is bemutatja, hogyan lehet váltani, amely támogatja a Premium storage:

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

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Konvertálása felügyelt lemezek standardról Premium az Azure Portalon

Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a virtuális Gépet a listából **virtuális gépek** a portálon.
3. Ha a virtuális gép nem fut, válassza ki a **leállítása** felső részén a virtuális gép **áttekintése** panelre, és várja meg a virtuális gép leállításához.
3. A virtuális gép panelén válassza **lemezek** a menüből.
4. Válassza ki az átalakítani kívánt lemezt.
5. Válassza ki **konfigurációs** a menüből.
6. Módosítsa a **fiók típusa** a **Standard HDD** való **prémium szintű SSD**.
7. Kattintson a **mentése**, és zárja be a lemez panelt.

A lemez típusának átalakítása az azonnali. Az átalakítás után újraindíthatja a virtuális gép.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Felügyelt lemezek közötti Standard HDD és Standard SSD 

Ez a példa bemutatja, hogyan egyetlen Virtuálisgép-lemez konvertálása a Standard HDD Standard SSD-re, illetve a standard szintű HDD Standard SSD:

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
