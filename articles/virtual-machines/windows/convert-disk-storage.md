---
title: Felügyelt lemezek tárolásának konvertálása a standard és a prémium szintű SSD között
description: Hogyan konvertálhatja az Azure felügyelt lemezeit standardról prémium vagy prémium szintre az Azure PowerShell használatával.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720945"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Felügyelt lemez tárolási típusának frissítése

Az Azure által felügyelt lemezeknek négy lemeztípusa van: Az Azure ultra SSD-k (előzetes verzió), a prémium szintű SSD, a standard SSD és a szabványos HDD. A három GA-lemeztípus (prémium szintű SSD, szabványos SSD és szabványos HDD) között a teljesítményigények alapján válthat. Még nem tud váltani egy ultra SSD-ről, telepítenie kell egy újat.

Ez a funkció nem felügyelt lemezek esetében nem támogatott. A nem felügyelt lemezeket azonban könnyedén [konvertálhatja felügyelt lemezvé,](convert-unmanaged-to-managed-disks.md) hogy képes legyen váltani a lemeztípusok között.

 

## <a name="prerequisites"></a>Előfeltételek

* Mivel az átalakítás a virtuális gép (VM) újraindítását igényli, ütemeznie kell a lemeztároló áttelepítését egy már meglévő karbantartási időszak ban.
* Ha a lemez nem felügyelt, először [konvertálja felügyelt lemezre,](convert-unmanaged-to-managed-disks.md) hogy válthasson a tárolási lehetőségek között.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>A virtuális gép összes felügyelt lemezének váltása a Prémium és a Standard között

Ez a példa bemutatja, hogyan konvertálhatja a virtuális gép összes lemezét standardról prémium szintű tárhelyre vagy prémium szintű tárolóról standard szintű tárolóra. Prémium szintű felügyelt lemezek használatához a virtuális gép kell használnia a [virtuális gép méretét,](sizes.md) amely támogatja a prémium szintű storage. Ez a példa olyan méretre is vált, amely támogatja a prémium szintű tárolást:

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
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Egyéni felügyelt lemezek váltása standard és prémium lemezek között

A fejlesztési és tesztelési számítási feladatok hoz, érdemes lehet a standard és a prémium szintű lemezek a költségek csökkentése érdekében. Dönthet úgy, hogy csak azokat a lemezeket frissíti, amelyeknek jobb teljesítményt kell nyújtaniuk. Ez a példa bemutatja, hogyan konvertálhatja egyetlen virtuális gép lemez standard prémium szintű tároló vagy prémium standard storage. Prémium szintű felügyelt lemezek használatához a virtuális gép kell használnia a [virtuális gép méretét,](sizes.md) amely támogatja a prémium szintű storage. Ez a példa azt is bemutatja, hogyan válthat olyan méretre, amely támogatja a prémium szintű tárhelyet:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Felügyelt lemezek konvertálása standardról prémium verzióra az Azure Portalon

Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza ki a virtuális gépet a **portálon** lévő virtuális gépek listájából.
3. Ha a virtuális gép nem áll le, válassza **leállítás** a virtuális gép **áttekintése** ablaktábla tetején, és várja meg, amíg a virtuális gép leáll.
3. A virtuális gép ablaktáblájában válassza a menü **Lemezek parancsát.**
4. Jelölje ki a konvertálni kívánt lemezt.
5. Válassza a menü **Konfiguráció** parancsát.
6. Módosítsa a **fiók típusát** **standard HDD-ről** **prémium SSD-re.**
7. Kattintson a **Mentés**gombra, és zárja be a lemezablaktáblát.

A lemeztípus-átalakítás azonnali. A virtuális gép a konvertálás után indítható el.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Felügyelt lemezek váltása szabványos HDD és standard SSD között 

Ez a példa bemutatja, hogyan konvertálható egyetlen virtuálisgép-lemez szabványos HDD-ről standard SSD-re vagy standard SSD-ről szabványos HDD-re:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>További lépések

A virtuális gép írásvédett másolata [pillanatkép](snapshot-copy-managed-disk.md)használatával.
