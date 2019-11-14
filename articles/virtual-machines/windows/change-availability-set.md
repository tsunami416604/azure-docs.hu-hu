---
title: Virtuális gépek rendelkezésre állási csoportjának módosítása
description: Megtudhatja, hogyan módosíthatja a virtuális gépek rendelkezésre állási csoportját a Azure PowerShell és a Resource Manager-alapú üzemi modell használatával.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/12/2019
ms.author: cynthn
ms.openlocfilehash: 7d03d684edfded1450043b943fc188c7aa07dc16
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039566"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Windows rendszerű virtuális gép rendelkezésre állási csoportjának módosítása
A következő lépések azt ismertetik, hogyan módosítható a virtuális gépek rendelkezésre állási csoportja a Azure PowerShell használatával. A virtuális gépeket csak akkor lehet felvenni a rendelkezésre állási csoportba, ha az létrejött. A rendelkezésre állási csoport módosításához törölnie kell, majd újra létre kell hoznia a virtuális gépet. 

Ez a cikk [Azure Cloud Shell](https://shell.azure.com/powershell) a 2/12/2019-es és az az [PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) 1.2.0.

 

## <a name="change-the-availability-set"></a>A rendelkezésre állási csoport módosítása 

Az alábbi parancsfájl egy példát mutat be a szükséges információk összegyűjtésére, az eredeti virtuális gép törlésére, majd egy új rendelkezésre állási csoportba való újbóli létrehozására.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
  
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
        {
            Add-AzVMNetworkInterface `
            -VM $newVM `
            -Id $nic.Id -Primary
            }
        else
            {
              Add-AzVMNetworkInterface `
              -VM $newVM `
              -Id $nic.Id 
                }
    }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Következő lépések

Adjon hozzá további tárhelyet a virtuális géphez egy további [adatlemez](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)hozzáadásával.

